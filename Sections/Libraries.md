
## Cryptographic Libraries


#### Use 「 Ordered 」

1. [Libsodium](https://doc.libsodium.org/): a modern, extremely fast, easy-to-use, well documented, and [audited](https://www.privateinternetaccess.com/blog/libsodium-v1-0-12-and-v1-0-13-security-assessment/) library that covers all common use cases, except for implementing TLS. However, it’s much bigger than Monocypher, meaning it’s harder to audit and not suitable for constrained environments, and sometimes requires the [Visual C++ Redistributable](https://support.microsoft.com/sl-si/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) to work on Windows.

2. [Monocypher](https://monocypher.org/): another modern, easy-to-use, well documented, and [audited](https://monocypher.org/quality-assurance/audit) library, but it’s about [half](https://monocypher.org/speed) the speed of libsodium on desktops/servers, has no misuse resistant functions (e.g. like libsodium’s [secretstream()](https://doc.libsodium.org/secret-key_cryptography/secretstream) and [secretbox()](https://doc.libsodium.org/secret-key_cryptography/secretbox)), only supports Argon2i for password hashing, allowing for insecure parameters (please see the [Password Hashing/Password-Based Key Derivation](#password-hashingpassword-based-key-derivation) Notes section), and offers no memory locking, random number generation, or convenience functions (e.g. Base64/hex encoding, padding, etc). However, it’s compatible with libsodium whilst being much smaller, portable, and fast for constrained environments (e.g microcontrollers).

3. [Tink](https://developers.google.com/tink): a misuse resistant library that prevents common pitfalls like nonce reuse. However, it doesn’t support hashing or password hashing, it’s not available in as many programming languages as libsodium and Monocypher, and it provides access to some algorithms that you shouldn’t use.

4. [LibHydrogen](https://libhydrogen.org): a lightweight, easy-to-use, hard-to-misuse, and well documented library suitable for constrained environments. The downsides are that it's not compatible with libsodium whilst also running [slower](https://monocypher.org/speed) than Monocypher. However, it has some advantages over Monocypher like support for random number generation, even on Arduino boards, and easy access to key exchange patterns, among other things.


---

#### Avoid 「 Ordered 」

1. A random library (with 0 stars) on GitHub: assuming it’s not been written by an experienced professional and it’s not a libsodium or Monocypher [binding](https://github.com/ektrah/nsec) to another programming language, you should generally stay away from unpopular, unaudited libraries. They are much more likely to suffer from vulnerabilities and be significantly slower than the more popular, audited libraries. Also, note that even [experienced professionals make mistakes](https://github.com/agl/ed25519/issues/27).

2. [OpenSSL](https://www.openssl.org/): very difficult to use, let alone use correctly, offers access to algorithms that you shouldn't use, the documentation is a mess, and lots of [vulnerabilities](https://www.openssl.org/news/vulnerabilities.html) have been found over the years. These issues have led to OpenSSL [forks](https://www.libressl.org/index.html) and new, non-forked [libraries](https://bearssl.org/goals.html) that aim to be better alternatives if you need to implement TLS.

3. The library available in your [programming language](https://docs.microsoft.com/en-us/dotnet/api/system.security.cryptography?view=net-5.0): most languages provide access to old algorithms (e.g. MD5 and SHA1) that shouldn’t be used anymore instead of newer ones (e.g. BLAKE2, BLAKE3, and SHA3), which can lead to poor algorithm choices. Furthermore, the APIs are typically easy to misuse, the documentation may fail to mention important security related information, and the implementations will be slower than libsodium.

4. Other popular libraries I haven’t mentioned (e.g. [BouncyCastle](https://bouncycastle.org/), [CryptoJS](https://cryptojs.gitbook.io/docs/), etc): these again often provide or rely on dated algorithms and typically have bad documentation. For instance, CryptoJS uses an [insecure](https://www.npmjs.com/package/evp_bytestokey) KDF called [EVP_BytesToKey()](https://www.openssl.org/docs/man1.1.1/man3/EVP_BytesToKey.html) in OpenSSL when you pass a string password to AES.encrypt(), and BouncyCastle has no C# documentation. However, this recommendation is too broad really since there are *some* libraries that I haven't mentioned that are worth using, like [PASETO](https://github.com/paragonie/paseto). Therefore, as a rule of thumb, **if it doesn't include several of the algorithms I recommend in this document, then it's probably bad**. Just do your research and assess the quality of the documentation.

5. [NaCl](https://nacl.cr.yp.to/): an unmaintained, less modern, and more confusing version of libsodium and Monocypher. For example, crypto_sign() for digital signatures has been [experimental](https://nacl.cr.yp.to/sign.html) for several years. It also doesn’t have password hashing support and is supposedly [difficult to install/package](https://monocypher.org/why).

6. [TweetNaCl](https://tweetnacl.cr.yp.to/): unmaintained, [slower](https://monocypher.org/speed) than Monocypher, doesn’t offer access to newer algorithms, doesn’t have password hashing, and [doesn’t zero out buffers](https://monocypher.org/why).


---

#### Notes

1. If the library you’re currently using/planning to use doesn’t support several of the algorithms I’m recommending, then it’s time to upgrade and take advantage of the improved security and performance benefits available to you if you switch.

2. Please read the documentation: don’t immediately jump into coding something because that’s how mistakes are made. Good libraries have high quality documentation that will explain potential security pitfalls and how to avoid them.

3. Some libraries release unauthenticated plaintext when using AEADs: for example, OpenSSL and BouncyCastle [apparently do](https://github.com/SalusaSecondus/CryptoGotchas). Firstly, don’t use these libraries for this reason and the reasons I’ve already listed. Secondly, **never do anything with unauthenticated plaintext; ignore it to be safe**.

4. Older does not mean better: you can argue that older algorithms are more battle tested and therefore proven to be a safe choice, but the reality is that most modern algorithms, like ChaCha20, BLAKE2, and Argon2, have been properly analysed at this point and shown to offer security and performance benefits over their older counterparts. Therefore, it doesn’t make sense to stick to this overly cautious mindset of avoiding newer algorithms, except for algorithms that are still candidates in a [competition](https://csrc.nist.gov/projects/post-quantum-cryptography) (e.g. new post-quantum algorithms), which do need further analysis to be considered safe.

5. You should prioritise speed: this can make a noticeable difference for the user. For example, a C# Argon2 library is not going to even come close to the speed of Argon2 in libsodium, meaning unnecessary and unwanted extra delay during key derivation. Libsodium is the go-to for speed on desktops/servers, and Monocypher is the go-to for constrained environments (e.g. microcontrollers).
