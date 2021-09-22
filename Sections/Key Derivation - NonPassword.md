
## (Non-Password-Based) Key Derivation Functions


#### Use (in order):

1. [Salted BLAKE2b](https://doc.libsodium.org/key_derivation): [restricted](https://www.blake2.net/blake2.pdf) to a 128-bit salt and 128-bit (16 character) personalisation parameter for domain separation, which is annoying. However, you can feed more context information into the message parameter. Besides the weird personal size limit, this is easier to use than HKDF because there’s only one function rather than three, which can be confusing. Furthermore, please see the [Hashing](#hashing) section for why BLAKE2b should be preferred over other hash functions. If there's no KDF variant of BLAKE2b available in your library, then you should probably use HKDF (please see below). However, **if you know what you're doing**, you can construct a BLAKE2b KDF using `BLAKE2b(message: salt || info || saltLength || infoLength, key: inputKeyingMaterial)`, with the `saltLength` and `infoLength` parameters being encoded as specified in point 5 of the [Message Authentication Codes](#message-authentication-codes) Notes section. Like HKDF, this custom approach allows for salt and info parameters of practically any length.

2. [HKDF-SHA512](https://en.wikipedia.org/wiki/HKDF) or [HKDF-SHA3-512](https://en.wikipedia.org/wiki/HKDF): the most popular KDF with support for a larger salt and lots of context information. However, people get confused about the difference between the Expand and Extract functions, it doesn’t require a salt despite it being [recommended and beneficial for security](https://datatracker.ietf.org/doc/html/rfc5869#section-3.1), and it’s slower than salted BLAKE2b. Please see the [Hashing](#hashing) and [Message Authentication Codes](#message-authentication-codes) sections for a comparison between SHA2/SHA3 and HMAC-SHA2/HMAC-SHA3.

3. [BLAKE3](https://github.com/BLAKE3-team/BLAKE3/#the-blake3-crate-): as mentioned before, BLAKE3 has a [lower security margin](https://github.com/BLAKE3-team/BLAKE3-specs/blob/master/blake3.pdf), but it also doesn’t have a salt parameter. With that said, [very good guidance](https://github.com/BLAKE3-team/BLAKE3#the-blake3-crate-) is given on how to produce globally unique and application specific context strings in the [official GitHub repo](https://github.com/BLAKE3-team/BLAKE3). If you'd like a salt parameter, then you can construct a custom KDF implementation as explained in point 1 above.


---

#### Avoid (not in order because they’re all bad):

1. Regular (salted or unsalted) hash functions: whilst this *can* be fine for deriving an encryption key from a Diffie-Hellman shared secret for example, it’s typically **not recommended**. Just use an actual KDF when possible as there’s less that can go wrong (e.g. there's no risk of [length extension attacks](https://en.wikipedia.org/wiki/Length_extension_attack)).

2. Password-based KDFs (e.g. [PBKDF2](https://en.wikipedia.org/wiki/PBKDF2)): if you’re not using a password, then you shouldn’t be using a password-based KDF. Password-based KDFs are designed to be slow to prevent [bruteforce attacks](https://en.wikipedia.org/wiki/Brute-force_attack), whereas non-password-based KDFs are fast because they're designed for high-entropy keys. Even with a small delay (e.g. 1 iteration of PBKDF2), this is likely slower and makes the code more confusing because an inappropriate function is being used.

3. [HChaCha20](https://doc.libsodium.org/key_derivation#nonce-extension) and [HSalsa20](https://cr.yp.to/snuffle/xsalsa-20110204.pdf): **these are not general-purpose cryptographic hash functions**, can only take a 256-bit key as input and output a 256-bit key, and are very rarely used, except in the case of implementing XChaCha20 and XSalsa20. If you want something based on ChaCha, then use BLAKE2b or BLAKE3.


---

#### Notes:

1. **These KDFs are not suitable for hashing passwords**: they should be used for deriving multiple subkeys from a **high-entropy** master key or converting a shared secret into a cryptographically strong secret key.

2. Using the same parameters besides changing the output length can result in related outputs (e.g. for HKDF and BLAKE3): this is exactly why you shouldn’t reuse the same parameters for different keys.

3. Use different contexts for different keys: a good format is `[application] [date and time] [purpose]` because this means the context information is application-specific and unique, which provides domain separation.

4. Salted BLAKE2b can use a **counter** salt: if you’re deriving multiple subkeys from a master key, then you can use a counter salt starting at 0 (16 bytes of 0s) that gets incremented for each subkey. However, if you’re deriving a single key (e.g. from a shared secret), then you should use a random salt.

5. Use a **random** salt with HKDF when possible: the [RFC](https://datatracker.ietf.org/doc/html/rfc5869#section-3.1) explains that using a random salt adds to the security of the scheme. The authors recommend a salt as long as the hash output length, but a 128-bit or 256-bit salt is sufficient. Using a secret salt (a bit like a [pepper](https://en.wikipedia.org/wiki/Pepper_(cryptography)) further improves the security guarantees.
