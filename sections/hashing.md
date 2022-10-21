# Hashing
## Use
### BLAKE2
[Faster](https://www.blake2.net/) than MD5, SHA-1, SHA-2, and SHA-3, yet as real-world [secure](https://eprint.iacr.org/2019/1492.pdf) as SHA-3. It relies on essentially the same core algorithm (borrowed from ChaCha20) as BLAKE, which received a [significant amount of cryptanalysis](https://nvlpubs.nist.gov/nistpubs/ir/2012/NIST.IR.7896.pdf) as part of the [SHA-3 competition](https://competitions.cr.yp.to/sha3.html).

It's available in [many](https://www.blake2.net/#us) cryptographic libraries and is used in [password hashing schemes](https://www.rfc-editor.org/rfc/rfc9106.html) and well-known software (e.g. [WireGuard](https://www.wireguard.com/protocol/) and the [Linux kernel](https://www.kernel.org/)).

There are two main variants. In most cases, use BLAKE2b-256, -384, or -512. However, on 8- to 32-bit platforms, BLAKE2s-256 will be more performant.

The biggest weakness is the [large](https://www.imperialviolet.org/2017/05/31/skipsha3.html) number of variants (e.g. BLAKE2x to get an XOF), but this issue is hidden from users by cryptographic libraries.

### SHAKE
Still [part of](https://en.wikipedia.org/wiki/SHA-3#Instances) the SHA-3 standard but [faster](https://en.wikipedia.org/wiki/SHA-3#Speed) than SHA-3 (similar to SHA-2) and an XOF.

SHAKE128 provides a 128-bit security level with at least a 256-bit output, and SHAKE256 provides a 256-bit security level with at least a 512-bit output.

To get the same security level as SHA3-256/SHA-256, you should use SHAKE256 with a 256-bit output. This provides 128-bit collision resistance.

### SHA-3
[Relatively slow](https://www.imperialviolet.org/2017/05/31/skipsha3.html) in software, but the [new standard](https://www.nist.gov/publications/sha-3-standard-permutation-based-hash-and-extendable-output-functions), [fast](https://keccak.team/2017/is_sha3_slow.html) in hardware, [well analysed](https://keccak.team/third_party.html), [very different](https://keccak.team/keccak.html) to SHA-2 due to the praised [sponge construction](https://keccak.team/sponge_duplex.html), and has a [higher security margin](https://eprint.iacr.org/2019/1492.pdf) than the other algorithms listed here.

The main criticism aside from speed is that the security is [over the top](https://crypto.stackexchange.com/a/70582). Even the designers [agree](https://en.wikipedia.org/wiki/SHA-3#Weakening_controversy) as they've developed [reduced functions](https://keccak.team/kangarootwelve.html) based on it. However, those alternative functions haven't seen much use compared to BLAKE2/BLAKE3.

### BLAKE3
The [fastest](https://github.com/BLAKE3-team/BLAKE3-specs/blob/master/blake3.pdf) cryptographic hash in software when accelerated at the cost of receiving less analysis, having a [lower security margin](https://github.com/BLAKE3-team/BLAKE3-specs/blob/master/blake3.pdf), and being limited to a [128-bit security level](https://github.com/BLAKE3-team/BLAKE3-specs/blob/master/blake3.pdf). It's also less accessible via cryptographic libraries than the other recommended algorithms.

However, it has the BLAKE legacy behind it and improves on BLAKE2 in that there’s only one variant that covers all use cases (e.g. a KDF and XOF too).

The speed is a huge bonus when using it as a MAC for Encrypt-then-MAC because it holds up against Poly1305 (from ChaCha20-Poly1305) and GHASH (from AES-GCM) when hashing enough data whilst providing stronger security guarantees (e.g. collision resistance for [committing security](https://youtu.be/dZqEtrLh9aM)).

### SHA-2
The most popular hash function. It’s widely available in cryptographic libraries, still secure after many years of [cryptanalysis](https://en.wikipedia.org/wiki/SHA-2#Cryptanalysis_and_validation), and offers decent performance.

However, unlike the other recommendations, it suffers from [length extension attacks](https://en.wikipedia.org/wiki/Length_extension_attack) (please see the [Notes](#beware-of-length-extension-attacks) section) and uses the [Merkle-Damgård construction](https://en.wikipedia.org/wiki/Merkle%E2%80%93Damg%C3%A5rd_construction), which [isn't used](https://crypto.stackexchange.com/a/83279) for new hash functions. It also [wasn't](https://keccak.team/keccak_strengths.html) the result of an open competition.

Thus, to take advantage of security improvements, a newer hash function should be used if possible.

## Avoid
### Non-cryptographic hash functions
For example, [Meow Hash](https://peter.website/meow-hash-cryptanalysis) and error-detecting codes (e.g. [CRC](https://en.wikipedia.org/wiki/Cyclic_redundancy_check)). These are **not secure**.

### MD5 and SHA-1
Both are very old and **no longer secure**. For instance, there’s an [attack](https://eprint.iacr.org/2013/170.pdf) that breaks MD5 collision resistance in 2<sup>18</sup> time, which takes less than a second to execute on an ordinary computer.

Obviously, don't use their older counterparts either (e.g. MD4 and SHA-0) because they're **even less secure**.

### Streebog
It has a [flawed](https://eprint.iacr.org/2016/071.pdf) [S-Box](https://eprint.iacr.org/2019/092.pdf), with no design rationale ever being made public, which is **likely a [backdoor](https://www.schneier.com/blog/archives/2019/05/cryptanalyzing_.html)**. It's somehow available in [VeraCrypt](https://www.veracrypt.fr/en/Streebog.html), but I've luckily not seen it used anywhere else.

### Non-finalist SHA-3 candidates
For example, [Edon-R](https://eprint.iacr.org/2009/378.pdf), which is **broken**. Nobody uses these, and they've received less scrutiny than finalists.

### SipHash
Despite the name, this is a [MAC](https://en.wikipedia.org/wiki/Message_authentication_code) (please see the [Message Authentication Codes](message-authentication-codes.md) section), meaning it requires a key. It's also [not collision resistant](https://crypto.stackexchange.com/questions/35086/siphashs-non-collision-resistance).

### Chaining hash functions
For instance, `SHA-256(SHA-1(message))` or [SHA-256d](https://crypto.stackexchange.com/a/7896). This can be [**insecure**](https://crypto.stackexchange.com/a/44454) (an inner collision means an outer collision) and is obviously less efficient than hashing once.

### RIPEMD
The original RIPEMD has [collisions](https://eprint.iacr.org/2004/199.pdf) and RIPEMD-128 has a small output size, meaning they're **insecure**.

Then the longer variants (e.g. RIPEMD-160) are still old, not long enough, unpopular, suffer from [length extension attacks](https://en.wikipedia.org/wiki/Length_extension_attack), and have worse performance and have received less analysis compared to the recommended algorithms.

Fun fact, RIPEMD-256 uses RIPEMD-128 internally, and RIPEMD-320 uses RIPEMD-160 internally. This means the longer versions provide the same security level as their half-sized counterpart, which isn't what you want.

### Cryptographic hash functions nobody uses
Whirlpool, Tiger, SHA-224, and so on. These are all worse in one way or another than the recommended algorithms.

For instance, there are attacks too close for comfort on Tiger plus there are [weird versions](https://crypto.stackexchange.com/questions/28986/what-is-tiger192-4-in-php), Whirlpool is [slower](https://www.cryptopp.com/benchmarks.html) than most other cryptographic hash functions and only produces a 512-bit output, and SHA-224 only provides [112-bit collision resistance](https://en.wikipedia.org/wiki/SHA-2#Comparison_of_SHA_functions), which is below the recommended 128-bit security level.

### 128-bit hashes
These only provide a 64-bit security level when you want 128-bit security, which requires using a 256-bit output.

### KangarooTwelve
From the people behind Keccak/SHA-3, much [faster](https://keccak.team/2017/is_sha3_slow.html) than SHA-3 and SHAKE, has a safe security margin, and has no variants. However, it's not that accessible, rarely used, and only offers [128-bit security](https://crypto.stackexchange.com/a/46529) like SHAKE128/BLAKE3.

## Notes
### These are not suitable for password hashing
Regular hash functions are fast, whereas password hashing [needs to be slow](https://crypto.stackexchange.com/a/3198) to prevent [password cracking](https://en.wikipedia.org/wiki/Password_cracking). Furthermore, password hashing requires using a **random** salt for each password to derive unique hashes when given the same input and to protect against [precomputation attacks](https://en.wikipedia.org/wiki/Rainbow_table).

### These are not suitable for authentication
These algorithms are unkeyed. You need to use a [MAC](https://en.wikipedia.org/wiki/Message_authentication_code) (please see the [Message Authentication Codes](message-authentication-codes.md) section), such as keyed BLAKE2b-256 or HMAC-SHA-256, for authentication because they provide the [appropriate security guarantees](https://en.wikipedia.org/wiki/Message_authentication_code#Security).

### Beware of length extension attacks
MD4, MD5, SHA-1, RIPEMD, Whirlpool, SHA-256, and SHA-512 are susceptible to [length extension attacks](https://en.wikipedia.org/wiki/Length_extension_attack).

An attacker can use `Hash(message1)` and the length of `message1` to calculate `Hash(message1 || message2)` for an attacker-controlled `message2`, without knowing `message1`.

Therefore, **concatenating things (e.g. `Hash(secret || message)`) with these algorithms is a bad idea**.

Use any of the non-SHA-2 recommendations instead because they're not susceptible. SHA-512/256, SHA-384, and HMAC-SHA-2 aren't either.

### Concatenation requires care
When feeding multiple inputs into a hash function, you [need to be careful](https://soatok.blog/2021/07/30/canonicalization-attacks-against-macs-and-signatures/) to avoid canonicalization attacks. As this is mostly relevant for MACs, please read the [Message Authentication Codes](message-authentication-codes.md) Notes section for more details.

### Hash functions do not increase entropy
If you hash a single ASCII character, there are still only 128 possible values. Therefore, [prehashing passwords](https://crypto.stackexchange.com/questions/66581/is-there-an-advantage-to-using-a-hash-in-combination-with-a-key-derivation-funct) before using a password-based KDF doesn't improve the entropy of the password.

### Truncation
With a modern, fixed-length hash (e.g. SHA3-512), use the full standard output length if possible, meaning no truncation. This maximises the security level.

If the hash function lets you specify a range (e.g. BLAKE2b), use that instead of manual truncation.

The same is true for XOFs (e.g. SHAKE and BLAKE3) because that's what they're designed for. However, with XOFs, a larger output for the same input will also start the same.

If you can use a fixed-length hash that does the truncation for you internally (e.g. SHA-512/256), use that instead of manual truncation. This provides [domain separation](https://crypto.stackexchange.com/questions/60966/which-attacks-are-prevented-by-the-different-initial-hash-values-for-sha-2-with).

Otherwise, you can take the first n bits from the left to get an n-bit hash. This is [safe](https://crypto.stackexchange.com/a/3156), but you lose domain separation compared to the above.