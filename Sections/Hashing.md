[ General Hashing ]: https://doc.libsodium.org/hashing/generic_hashing
[ IRL Secure ]: https://eprint.iacr.org/2019/1492.pdf
[ Significant Analysis ]: https://nvlpubs.nist.gov/nistpubs/ir/2012/NIST.IR.7896.pdf
[ Introduction ]: https://www.rfc-editor.org/rfc/rfc9106.html#name-introduction
[ Blake2 ]: https://www.blake2.net/#us
[ SHA Comparison ]: https://en.wikipedia.org/wiki/SHA-2#Comparison_of_SHA_functions
[ Length Extension Attack ]: https://en.wikipedia.org/wiki/Length_extension_attack
[ SHA 3 Comparison ]: https://en.wikipedia.org/wiki/SHA-3#Comparison_of_SHA_functions
[ Slow SHA3 ]: https://www.imperialviolet.org/2017/05/31/skipsha3.html
[ New Standard ]: https://www.nist.gov/publications/sha-3-standard-permutation-based-hash-and-extendable-output-functions
[ Fast SHA3 ]: https://keccak.team/2017/is_sha3_slow.html
[ Security Margin ]: https://eprint.iacr.org/2012/421.pdf
[ Blake3 ]: https://github.com/BLAKE3-team/BLAKE3#readme
[ Blake3 Implementation ]: https://github.com/BLAKE3-team/BLAKE3/issues/31
[ Blake3 Specs ]: https://github.com/BLAKE3-team/BLAKE3-specs/blob/master/blake3.pdf
[ SHA3 Candidates ]: https://eprint.iacr.org/2009/378.pdf
[ Cycling Redundancy ]: https://en.wikipedia.org/wiki/Cyclic_redundancy_check
[ MD5 ]: https://en.wikipedia.org/wiki/MD5
[ SHA1 ]: https://en.wikipedia.org/wiki/SHA-1
[ MD5 Collision ]: https://eprint.iacr.org/2013/170.pdf
[ RIPEMD ]: https://en.wikipedia.org/wiki/RIPEMD
[ Whirlpool ]: https://en.wikipedia.org/wiki/Whirlpool_(hash_function)
[ Streetbog ]: https://en.wikipedia.org/wiki/Streebog
[ MD6 ]: https://en.wikipedia.org/wiki/MD6
[ Benchmark ]: https://www.cryptopp.com/benchmarks.html
[ S-Box Design ]: https://eprint.iacr.org/2016/071.pdf
[ Password Cracking ]: https://en.wikipedia.org/wiki/Password_cracking
[ Rainbow Tables ]: https://en.wikipedia.org/wiki/Rainbow_table
[ MAC ]: https://en.wikipedia.org/wiki/Message_authentication_code
[ MAC Security ]: https://en.wikipedia.org/wiki/Message_authentication_code#Security
[ SHA3 ]: https://competitions.cr.yp.to/sha3.html



## Hashing


#### Use 「 Ordered 」

1. [BLAKE2b-512][ General Hashing ] or [BLAKE2b-256][ General Hashing ]: fast, modern, as [real-world secure][ IRL Secure ] as SHA3, BLAKE (what BLAKE2 was on) received a [significant amount of cryptanalysis][ Significant Analysis ], even more than Keccak (the SHA3 finalist), as part of the SHA3 competition, and now quite popular in software (e.g. it’s used in [Argon2][ Introduction ] and many [other][ Blake2 ] password hashing schemes).

2. [SHA512][ SHA Comparison ], [SHA512/256][ SHA Comparison ], or [SHA256][ SHA Comparison ]: SHA2 is the most popular hash function, meaning it’s widely available in cryptographic libraries, it’s still secure besides [length extension attacks][ Length Extension Attack ] (please see point 3 of the Notes section), and it offers decent performance.

3. [SHA3-512][ SHA 3 Comparison  ] or [SHA3-256][ SHA 3 Comparison  ]: [slow][ Slow SHA3 ] in software, but the [new standard][ New Standard ], [fast][ Fast SHA3 ] in hardware, and has a [higher security margin][ Security Margin ] than the other algorithms listed here. If it was more common in software, then I would recommend it over SHA2 to prevent length extension attacks, but I’d still recommend BLAKE2b in such a case due to the improved software performance and equivalent security.

4. [BLAKE3-256][ Blake3 ]: the [fastest][ Blake3 Specs ] cryptographic hash in software (assuming you don’t suffer from [performance issues in the main implementation][ Blake3 Implementation ]) at the cost of having a [lower security margin][ Blake3 Specs ] and being limited to a [128-bit security level][ Blake3 Specs ]. However, it improves on BLAKE2 in that there’s only one variant that covers all use cases (it’s a regular hash, PRF, MAC, KDF, and XOF), but depending on the cryptographic library you use, this isn’t necessarily something you’ll notice when using BLAKE2b anyway.


---

#### Avoid 「 Unordered | All Unsuitable 」

- **Non-cryptographic** hash functions and error-detecting codes (e.g. [CRC][ Cycling Redundancy ]): the clue is in the name. These are **not secure**.

- [MD5][ MD5 ] and [SHA1][ SHA1 ]: both are very old and **no longer secure**. For instance, there’s an [attack][ MD5 Collision ] that breaks MD5 collision resistance in 2^18 time. This takes less than a second to execute on an ordinary computer.

- **Insecure** SHA3 competition candidates (e.g. [EDON-R][ SHA3 Candidates ]): if you want to use something from the SHA3 competition, then you should either use BLAKE2b (based on BLAKE, which was thoroughly analysed and deemed to have a [very high security margin][ Significant Analysis ]), SHA3 (the winner, very different to SHA2 in design, and has a [very high security margin][ Significant Analysis ]), or BLAKE3 (based on BLAKE2 but with a [lower security margin][ Blake3 Specs ]).

- [RIPEMD][ RIPEMD ]: old, unpopular, most implementations are limited to small output lengths (e.g. 160-bit is the most common), and it has worse performance and has received less analysis compared to the recommended algorithms.

- [Whirlpool][ Whirlpool ], [SHA224][ SHA Comparison ], [Streetbog][ Streetbog ], [MD6][ MD6 ], and other hashes nobody uses: these are all worse in one way or another than the recommended algorithms, which is why nobody uses them. For instance, Whirlpool is [slower][ Benchmark ] than most other cryptographic hash functions, SHA224 only provides [112-bit collision resistance][ SHA Comparison ], which is below the recommended 128-bit security level, Streetbog has a [poor S-Box design with no design rational ever being made public][ S-Box Design ], MD6 didn't make it to the [second round][ SHA3 ] of the SHA3 competition and has [speed issues][ MD6 ], and so on.

- Chaining hash functions (e.g. `SHA256(SHA1(message))`): this can be **insecure** (e.g. SHA1 has worse collision resistance than SHA256, meaning a collision for SHA1 results in a collision for `SHA256(SHA1(message))`) and is obviously less efficient than hashing once. **Just don’t do this**.

- 128-bit hashes: you shouldn’t go below a 256-bit output with hash functions to ensure 128-bit security.


---

#### Notes

1. **These hash functions are not suitable for password hashing**: these algorithms are fast, whereas password hashing needs to be slow to prevent [bruteforce attacks][ Password Cracking ]. Furthermore, password hashing requires using a **random** salt for each password to derive unique hashes when given the same input and to protect against attacks using [precomputed hashes][ Rainbow Tables ].

2. **These unkeyed hash functions are not suitable for authentication**: you need to use [MACs][ MAC ] (please see the [Message Authentication Codes](./Message Authentication.md) section), such as keyed BLAKE2b-512 and HMAC-SHA512, for authentication because they provide the [appropriate security guarantees][ MAC Security ].

3. **SHA2** (except for SHA512/256 – SHA224 and SHA384 [don’t provide the same level of protection][ SHA Comparison ]), MD5, SHA1, Whirlpool, RIPEMD-160, and MD4 are [susceptible to length extension attacks][ Length Extension Attack ]: an attacker can use `Hash(message1)` and the length of `message1` to calculate `Hash(message1 || message2)`, with `message2` being controlled by the attacker, without knowing what `message1` is. Therefore, **concatenating things (e.g. `Hash(secret || message)`) with these algorithms is a bad idea**. Instead, BLAKE2b, SHA512/256, HMAC-SHA2, SHA3, HMAC-SHA3, or BLAKE3 should be used because none of these are susceptible to length extension attacks. Also, please read point 5 of the [Message Authentication Codes](./Message Authentication.md) Notes section because concatenating parameters incorrectly can lead to another type of attack.
