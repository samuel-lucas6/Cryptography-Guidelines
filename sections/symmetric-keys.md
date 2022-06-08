# Symmetric Keys
## Use
### 256-bit keys
There are two main arguments for 128-bit keys:
- [Increased performance](https://github.com/jedisct1/zig-rocca#readme) for many algorithms
- A 256-bit key is [excessive](https://security.stackexchange.com/questions/14068/why-most-people-use-256-bit-encryption-instead-of-128-bit) since a 128-bit key cannot be bruteforced

Whilst the first point is generally true, some algorithms only support 256-bit keys (e.g. [ChaCha20-Poly1305](https://datatracker.ietf.org/doc/html/rfc8439)) and newer algorithms are closing/eliminating the performance gap (e.g. [Rocca](https://tosc.iacr.org/index.php/ToSC/article/view/8904/8480)).

Regarding the second argument, **a 128-bit key doesn't really translate to 128-bit security** due to [batch attacks](https://blog.cr.yp.to/20151120-batchattacks.html), which involve attacking [many keys at once](https://crypto.stackexchange.com/questions/75880/what-is-a-multi-target-attack).

Furthermore, it's [recommended](https://www.bsi.bund.de/SharedDocs/Downloads/EN/BSI/Publications/Brochure/quantum-safe-cryptography.html?nn=433196) that 256-bit keys are used to ensure post-quantum security since there's concern that future quantum computers will eventually be able to bruteforce 128-bit keys.

Nobody can accurately predict how the quantum computer situation will play out, but it's clear that **256-bit keys should be used if security is a priority**.

### 512-bit keys
With MACs like [HMAC](https://datatracker.ietf.org/doc/html/rfc2104), it’s [recommended](https://www.rfc-editor.org/rfc/rfc2104#section-3) to use a key size as large as the output length to avoid a reduction in security (e.g. a 512-bit key for HMAC-SHA512).

Realistically, this is overkill because 512-bit security is not necessary, but using a larger key size can act as good domain separation when deriving keys (e.g. for Encrypt-then-MAC).

**I do not recommend this for encryption (please see below)**.

## Avoid
### Smaller than 128-bit keys
In [some cases](https://en.wikipedia.org/wiki/Data_Encryption_Standard#Brute-force_attack), such keys can already be bruteforced. For slightly larger keys, they very likely won’t stand the test of time.

### 128-bit keys
Please see the 256-bit keys discussion. In sum, this is safe today and often leads to a performance increase, but this doesn't really offer a 128-bit security level and won't provide long-term protection.

Also, the [argument](https://crypto.stackexchange.com/questions/91877/so-is-aes-256-more-secure-or-less-secure-than-aes-128-after-all) that AES-128 is more secure than AES-256 due to [certain attacks](https://security.stackexchange.com/a/14537) being more effective on AES-256 is misleading because such attacks are [not practical](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard#Known_attacks) in properly designed protocols.

### Large keys (e.g. 512-bit and 1024-bit)
Some symmetric encryption algorithms support large key sizes (e.g. [Threefish](https://en.wikipedia.org/wiki/Threefish)). However, key sizes over 256-bit are widely regarded as [unnecessary](https://xkcd.com/538/) because they provide [no practical](https://crypto.stackexchange.com/a/1160) security benefit.

Furthermore, encryption algorithms supporting such key sizes are unpopular in practice. This is a good sign that they should be avoided.

Similarly, HMAC keys larger than the hash function block size (e.g. > 512 bits with HMAC-SHA256 and > 1024 bits with HMAC-SHA512) should be avoided. Firstly, this is blatantly excessive. Secondly, this causes the key to get hashed down to the output length of the hash function, which ironically reduces security compared to using a key as large as the block size.

**Stick to 256-bit keys for encryption, and use 512-bit keys for MACs with large output lengths or if you want extra domain separation.**

## Notes
### Symmetric keys must be kept secret
Unlike with public-key cryptography, where you can share the public key safely, you must **not** share a symmetric key via an insecure (e.g. unencrypted) channel.

Also, revealing an [unsalted/unkeyed hash of a key](https://keymaterial.net/2020/09/07/invisible-salamanders-in-aes-gcm-siv/) **leaks** the identity, violating indistinguishability. Thus, this should be avoided. A MAC of the key solves this problem.

### Keys must be uniformly random
They can be generated in three ways:
- Randomly using a **cryptographically secure** pseudorandom number generator (please see the [Random Numbers](random-numbers.md) section)
- Derived from a **high-entropy** key (e.g. a [shared secret](https://doc.libsodium.org/advanced/scalar_multiplication#usage)) using a key derivation function (please see the [(Non-Password-Based) Key Derivation Functions](non-password-based-key-derivation-functions.md) section)
- Derived from a password using a password-based key derivation function (please see the [Password Hashing/Password-Based Key Derivation](password-hashing-password-based-key-derivation.md) section)

[String keys](https://littlemaninmyhead.wordpress.com/2021/09/15/if-you-copied-any-of-these-popular-stackoverflow-encryption-code-snippets-then-you-did-it-wrong/), passwords/passphrases, any other low-entropy information, and [shared secrets](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange#General_overview) must **not** be used directly!

### Use different keys for different algorithms
For instance, two **distinct** keys for XChaCha20 and BLAKE2b when doing Encrypt-then-MAC. This is [recommended](https://crypto.stackexchange.com/questions/8081/using-the-same-secret-key-for-encryption-and-authentication-in-a-encrypt-then-ma) practice because it's safest since there can be overlap between algorithms.

There are two main ways of doing this:
- With passwords, use a larger output length (e.g. 96 bytes) for the password-based KDF and split the output into two keys (e.g. 256-bit and 512-bit)
- With **high entropy** keys (e.g. a randomly generated key), you can use a non-password-based KDF twice with the same input keying material but different context information and output lengths for domain separation

Please read the [Password Hashing/Password-Based Key Derivation](password-hashing-password-based-key-derivation.md) and [(Non-Password-Based) Key Derivation Functions](non-password-based-key-derivation-functions.md) sections for more important information.

### Use a new key each time
Generally, it's sensible to use a unique key each time you encrypt or authenticate a different message.

The main exception is when chunking the same message for encryption, as explained in the [Symmetric Encryption](sections/symmetric-encryption.md) Notes section.

This helps prevent [cryptographic wear-out](https://soatok.blog/2020/12/24/cryptographic-wear-out-for-symmetric-encryption/) (using a single key to encrypt too much data), nonce reuse, and reusing keys with multiple algorithms.

It's also beneficial for security in that a compromise of one key doesn’t compromise data encrypted under different keys.

One common way of doing this is to:
- Randomly generate a unique data encryption key (DEK) for each message
- Encrypt the DEK using a key encryption key (KEK), which can be reused for many DEKs, derived using a key derivation function (KDF)
- Prepend the encrypted DEK to the ciphertext

For decryption:
- Derive the KEK using a KDF
- Use it to decrypt the encrypted DEK
- Use the DEK to decrypt the ciphertext

Alternatively, you can derive unique keys using a random salt with a KDF, although this is inefficient when using a password-based KDF since it means a delay for every message.

### Erase keys from memory
Once you’ve finished using a key, an attempt should be made to erase it from memory to prevent an attacker with physical or remote access to a machine being able to retrieve it.

Note that in garbage collected programming languages, such as [C#](https://docs.microsoft.com/en-us/dotnet/standard/garbage-collection/), [Go](https://go.dev/blog/ismmkeynote), and [JavaScript](https://javascript.info/garbage-collection), this is difficult to achieve because the garbage collector can copy keys around in memory.

It may be possible to [pin](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.gchandle.alloc?view=net-6.0) memory, which would prevent the key from being copied. Then compiler optimisations should be [disabled](https://docs.microsoft.com/en-us/dotnet/api/system.security.cryptography.cryptographicoperations.zeromemory?view=net-6.0#System_Security_Cryptography_CryptographicOperations_ZeroMemory_System_Span_System_Byte__). These steps will ensure that when you zero out the key variable, it will be wiped properly.

[Locking memory](https://doc.libsodium.org/memory_management#locking-memory) via an external library can also help prevent keys being written to disk.