
## Message Authentication Codes


#### Use (in order):

1. [Keyed BLAKE2b-256](https://doc.libsodium.org/hashing/generic_hashing) or [keyed BLAKE2b-512](https://doc.libsodium.org/hashing/generic_hashing): these are faster than HMAC, BLAKE (what BLAKE2 was on) received a [significant amount of cryptanalysis](https://nvlpubs.nist.gov/nistpubs/ir/2012/NIST.IR.7896.pdf), even more than Keccak (the SHA3 finalist), as part of the SHA3 competition, and BLAKE2b provides the [same practical level of security](https://eprint.iacr.org/2019/1492.pdf) as SHA3 whilst also being more popular in software (e.g. it’s used in [Argon2](https://www.rfc-editor.org/rfc/rfc9106.html#name-introduction) and many [other](https://www.blake2.net/#us) password hashing schemes).

2. [HMAC-SHA256](https://doc.libsodium.org/advanced/hmac-sha2) or [HMAC-SHA512](https://doc.libsodium.org/advanced/hmac-sha2): slower and older than BLAKE2b but [well-studied](https://en.wikipedia.org/wiki/SHA-2#Cryptanalysis_and_validation). SHA2 is also faster and far more available than SHA3, which has seen somewhat limited adoption so far since SHA2 is still secure.

3. [HMAC-SHA3-256](https://en.wikipedia.org/wiki/SHA-3) or [HMAC-SHA3-512](https://en.wikipedia.org/wiki/SHA-3): SHA3 is [slower](https://www.imperialviolet.org/2017/05/31/skipsha3.html) in software than BLAKE2b and SHA2 but has a [higher security margin](https://csrc.nist.gov/csrc/media/projects/hash-functions/documents/sha-3_selection_announcement.pdf) than both and is [fast](https://keccak.team/2017/is_sha3_slow.html) in hardware. Moreover, HMAC-SHA3 is needlessly inefficient because SHA3 is already a MAC. The only reason I’m recommending HMAC-SHA3 is because SHA3 is designed to be a replacement for SHA2, KMAC is rarely available, and you shouldn't have to construct a MAC using concatenation yourself in most scenarios.

4. [Keyed BLAKE3-256](https://github.com/BLAKE3-team/BLAKE3#readme): [faster](https://github.com/BLAKE3-team/BLAKE3-specs/blob/master/blake3.pdf) than HMAC, BLAKE2b, and SHA3, but it has a [smaller security margin](https://github.com/BLAKE3-team/BLAKE3-specs/blob/master/blake3.pdf), only targets the [128-bit security level](https://github.com/BLAKE3-team/BLAKE3-specs/blob/master/blake3.pdf), and hasn't been implemented in many cryptographic libraries yet.


---

#### Avoid (not in order because they’re all bad):

- [HMAC-MD5](https://en.wikipedia.org/wiki/HMAC#Security) and [HMAC-SHA1](https://en.wikipedia.org/wiki/HMAC): MD5 and SHA1 should no longer be used for anything.

- Regular, unencrypted hashes (e.g. `SHA256(ciphertext)`): this is **insecure** because unkeyed hashes don't provide authentication.

- Regular, encrypted hashes (e.g. `AES-CTR(SHA256(ciphertext))`): this is **insecure**. For example, with a stream cipher, you could flip bits in the ciphertext hash.

- `SHA2(key || message)`: this is **vulnerable** to [length extension attacks](https://en.wikipedia.org/wiki/Length_extension_attack), as discussed in point 3 of the Notes in the [Hashing](#hashing) section. Technically speaking, `SHA2(message || key)` works as a MAC if the attacker doesn’t know the key, but it’s weaker than constructions like HMAC because it requires the hash function to be collision resistant rather than a pseudorandom function and therefore shouldn’t be used. Newer hash functions, like BLAKE2b, SHA3, and BLAKE3, are resistant to length extension attacks and could be used to perform `Hash(key || message)` safely, but you should still just use a keyed hash function or HMAC instead to do the work for you.

- [Poly1305](https://doc.libsodium.org/advanced/poly1305) and other polynomial MACs: these produce small tags that are designed for online protocols and small messages. They’re also easier to misuse than the recommended algorithms (e.g. Poly1305 requires a secret, unique, and unpredictable key each time that’s independent from the encryption key).

- [CBC-MAC](https://en.wikipedia.org/wiki/CBC-MAC): this is unpopular and often [implemented incorrectly](https://blog.cryptographyengineering.com/2013/02/15/why-i-hate-cbc-mac/) because it has [weird requirements](https://en.wikipedia.org/wiki/CBC-MAC#Security_with_fixed_and_variable-length_messages) that most people are completely unaware of, **allowing for attacks**. Even when implemented correctly, the recommended algorithms are better.

- [CMAC/OMAC](https://en.wikipedia.org/wiki/One-key_MAC): almost nobody uses this, even though it improves on CBC-MAC in terms of preventing mistakes.

- 128-bit [keyed hashes](https://doc.libsodium.org/hashing/generic_hashing#usage) or [HMACs](https://en.wikipedia.org/wiki/HMAC): **you shouldn’t go below a 256-bit output** with hash functions because 128-bit security should be the minimum.

- [KMAC](https://en.wikipedia.org/wiki/SHA-3#Additional_instances): whilst more efficient than HMAC-SHA3, it seems to be rarely available. Furthermore, it’s likely that HMAC-SHA3 will be the norm because SHA3 is designed to replace SHA2, which is used with HMAC.


---

#### Notes:

1. **Please read** points 14-17 of the [Symmetric Encryption](#symmetric-encryption) Notes for guidance on implementing a MAC correctly.

2. **Please read** point 2 of the [Symmetric Key Size](#symmetric-key-size) Use section for guidance on what key size to use.

3. A 256-bit authentication tag is sufficient for most use cases: however, a 512-bit tag provides additional security if you’re concerned about quantum computing. I wouldn’t recommend bothering with an output length in-between (e.g. HMAC-SHA384) because that’s not common, and you may as well go all the way to get a 256-bit security level.

4. Append the authentication tag to the ciphertext: this is common practice and how AEADs operate.

5. Concatenating multiple variable length parameters (e.g. `HMAC(message: additionalData || ciphertext, key: macKey)`) can lead to **attacks**: if you fail to concatenate the lengths of the parameters (e.g. `HMAC(message: additionalData || ciphertext || additionalDataLength || ciphertextLength, key: macKey)`, with the lengths converted to a fixed number of bytes consistently in either big- or little-endian, regardless of the endianness of the machine) or always ensure that they are fixed in size, then your implementation will be susceptible to [canonicalization attacks](https://soatok.blog/2021/07/30/canonicalization-attacks-against-macs-and-signatures/) because an attacker can shift bytes in the different parameters whilst producing a valid authentication tag. AEADs do this length concatenation for you to prevent this.
