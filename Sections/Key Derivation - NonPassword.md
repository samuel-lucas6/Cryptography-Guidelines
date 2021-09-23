[ Key Derivation ]: https://doc.libsodium.org/key_derivation
[ Blake 2 ]: https://www.blake2.net/blake2.pdf
[ Blake 3 ]: https://github.com/BLAKE3-team/BLAKE3#the-blake3-crate-
[ HKDF ]: https://en.wikipedia.org/wiki/HKDF
[ RFC5869 ]: https://datatracker.ietf.org/doc/html/rfc5869#section-3.1
[ Blake 3 Specs ]: https://github.com/BLAKE3-team/BLAKE3-specs/blob/master/blake3.pdf
[ Blake 3 Github ]: https://github.com/BLAKE3-team/BLAKE3
[ Length Extension Attack ]: https://en.wikipedia.org/wiki/Length_extension_attack
[ PBKDF2 ]: https://en.wikipedia.org/wiki/PBKDF2
[ Bruteforce Attack ]: https://en.wikipedia.org/wiki/Brute-force_attack
[ Nonce Extension ]: https://doc.libsodium.org/key_derivation#nonce-extension
[ XSalsa ]: https://cr.yp.to/snuffle/xsalsa-20110204.pdf
[ Pepper ]: https://en.wikipedia.org/wiki/Pepper_(cryptography)





## (Non-Password-Based) Key Derivation Functions


#### Use 「 Ordered 」

1. [Salted BLAKE2b][ Key Derivation ]: [restricted][ Blake 2 ] to a 128-bit salt and 128-bit (16 character) personalisation parameter for domain separation, which is annoying. However, you can feed more context information into the message parameter. Besides the weird personal size limit, this is easier to use than HKDF because there’s only one function rather than three, which can be confusing. Furthermore, please see the [Hashing](./Hashing.md) section for why BLAKE2b should be preferred over other hash functions. If there's no KDF variant of BLAKE2b available in your library, then you should probably use HKDF (please see below). However, **if you know what you're doing**, you can construct a BLAKE2b KDF using `BLAKE2b(message: salt || info || saltLength || infoLength, key: inputKeyingMaterial)`, with the `saltLength` and `infoLength` parameters being encoded as specified in point 5 of the [Message Authentication Codes](./Message Authentication.md) Notes section. Like HKDF, this custom approach allows for salt and info parameters of practically any length.

2. [HKDF-SHA512][ HKDF ] or [HKDF-SHA3-512][ HKDF ]: the most popular KDF with support for a larger salt and lots of context information. However, people get confused about the difference between the Expand and Extract functions, it doesn’t require a salt despite it being [recommended and beneficial for security][ RFC5869 ], and it’s slower than salted BLAKE2b. Please see the [Hashing](./Hashing.md) and [Message Authentication Codes](./Message Authentication.md) sections for a comparison between SHA2/SHA3 and HMAC-SHA2/HMAC-SHA3.

3. [BLAKE3][ Blake 3 ]: as mentioned before, BLAKE3 has a [lower security margin][ Blake 3 Specs ], but it also doesn’t have a salt parameter. With that said, [very good guidance][ Blake 3 ] is given on how to produce globally unique and application specific context strings in the [official GitHub repo][ Blake 3 Github ]. If you'd like a salt parameter, then you can construct a custom KDF implementation as explained in point 1 above.


---

#### Avoid 「 Unordered | All Unsuitable 」

- Regular (salted or unsalted) hash functions: whilst this *can* be fine for deriving an encryption key from a Diffie-Hellman shared secret for example, it’s typically **not recommended**. Just use an actual KDF when possible as there’s less that can go wrong (e.g. there's no risk of [length extension attacks][ Length Extension Attack ]).

- Password-based KDFs (e.g. [PBKDF2][ PBKDF2 ]): if you’re not using a password, then you shouldn’t be using a password-based KDF. Password-based KDFs are designed to be slow to prevent [bruteforce attacks][ Bruteforce Attack ], whereas non-password-based KDFs are fast because they're designed for high-entropy keys. Even with a small delay (e.g. 1 iteration of PBKDF2), this is likely slower and makes the code more confusing because an inappropriate function is being used.

- [HChaCha20][ Nonce Extension ] and [HSalsa20][ XSalsa ]: **these are not general-purpose cryptographic hash functions**, can only take a 256-bit key as input and output a 256-bit key, and are very rarely used, except in the case of implementing XChaCha20 and XSalsa20. If you want something based on ChaCha, then use BLAKE2b or BLAKE3.


---

#### Notes

1. **These KDFs are not suitable for hashing passwords**: they should be used for deriving multiple subkeys from a **high-entropy** master key or converting a shared secret into a cryptographically strong secret key.

2. Using the same parameters besides changing the output length can result in related outputs (e.g. for HKDF and BLAKE3): this is exactly why you shouldn’t reuse the same parameters for different keys.

3. Use different contexts for different keys: a good format is `[application] [date and time] [purpose]` because this means the context information is application-specific and unique, which provides domain separation.

4. Salted BLAKE2b can use a **counter** salt: if you’re deriving multiple subkeys from a master key, then you can use a counter salt starting at 0 (16 bytes of 0s) that gets incremented for each subkey. However, if you’re deriving a single key (e.g. from a shared secret), then you should use a random salt.

5. Use a **random** salt with HKDF when possible: the [RFC][ RFC5869 ] explains that using a random salt adds to the security of the scheme. The authors recommend a salt as long as the hash output length, but a 128-bit or 256-bit salt is sufficient. Using a secret salt (a bit like a [pepper][ Pepper ] further improves the security guarantees.
