# Cryptographic Libraries
As a rule of thumb, **if the library doesn't include many of the algorithms I recommend in these guidelines, it's probably bad**.

## Use
### [Libsodium](https://doc.libsodium.org/)
A modern, extremely fast, easy-to-use, well documented, and [audited](https://www.privateinternetaccess.com/blog/libsodium-v1-0-12-and-v1-0-13-security-assessment/) library that covers all common use cases, except for implementing [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security). It's frequently [recommended](https://crypto.stackexchange.com/questions/50760/how-can-a-non-crypto-expert-implement-crypto-libraries-in-a-programming-language/50762#50762) and used by [large companies](https://doc.libsodium.org/libsodium_users#companies-using-libsodium).

However, it’s much bigger than Monocypher (see below), meaning it’s harder to audit and not suitable for constrained environments. It also annoyingly requires the [Visual C++ Redistributable](https://support.microsoft.com/sl-si/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) to work on Windows. The latest vcruntime DLLs to bundle with portable programs can be found [here](https://github.com/abbodi1406/vcredist).

### [Monocypher](https://monocypher.org/)
Another modern, easy-to-use, well documented, and [audited](https://monocypher.org/quality-assurance/audit) library. Assuming you like [Daniel J. Bernstein](https://en.wikipedia.org/wiki/Daniel_J._Bernstein), it covers typical and some rarer use cases (e.g. steganography support using [Elligator 2](https://elligator.cr.yp.to/)). It's also compatible with libsodium whilst being much smaller, portable, and fast for constrained environments (e.g microcontrollers).

However, it’s about [half](https://monocypher.org/speed) the speed of libsodium on desktops/servers, has no misuse resistant functions (e.g. like libsodium’s [secretstream()](https://doc.libsodium.org/secret-key_cryptography/secretstream) and [secretbox()](https://doc.libsodium.org/secret-key_cryptography/secretbox)), only supports [Argon2i](https://www.rfc-editor.org/rfc/rfc9106.html#name-introduction) for password hashing, allowing for insecure parameters (please see the [Password Hashing/Password-Based Key Derivation](password-hashing-password-based-key-derivation.md) Notes section), and offers no memory locking, random number generation, or convenience functions (e.g. Base64/hex encoding, padding, etc).

### [Tink](https://developers.google.com/tink)
A misuse resistant library by Google that prevents common pitfalls like [nonce reuse](https://www.daemonology.net/blog/2011-01-18-tarsnap-critical-security-bug.html). Unlike Monocypher, it supports [FIPS approved algorithms](https://developers.google.com/tink/FIPS) if that's a requirement.

However, it doesn’t support hashing or password hashing, it’s not available in [as many programming languages](https://github.com/google/tink/blob/master/docs/PRIMITIVES.md#primitives-supported-by-language) as [libsodium](https://doc.libsodium.org/bindings_for_other_languages) and [Monocypher](https://monocypher.org/download/), the documentation is a bit [harder to navigate](https://github.com/google/tink/tree/master/docs), and it provides access to [some algorithms](https://github.com/google/tink/blob/master/docs/PRIMITIVES.md#primitive-implementations-supported-by-language) that you ideally shouldn’t use.

### [LibHydrogen](https://libhydrogen.org)
A lightweight, easy-to-use, hard-to-misuse, and well documented library suitable for constrained environments.

The downsides are that it's [not compatible](https://monocypher.org/why/) with libsodium whilst also running [slower](https://monocypher.org/speed) than Monocypher. However, it has some advantages over Monocypher, like support for [random number generation](https://github.com/jedisct1/libhydrogen/wiki/Random-numbers) and easy access to [key exchange patterns](https://github.com/jedisct1/libhydrogen/wiki/Key-exchange), among other things.

## Avoid
### A [random library](https://github.com/martijnat/crypturd) (e.g. with 0 stars) on GitHub
Assuming it’s not been written by an [experienced](https://github.com/jedisct1) [professional](https://github.com/FiloSottile) and it’s not a libsodium or Monocypher [binding](https://github.com/ektrah/nsec) to another programming language, you should generally stay away from less popular, unaudited libraries.

They are much more likely to suffer from vulnerabilities and be significantly slower than the more popular, audited libraries. Also, note that even [experienced professionals make mistakes](https://www.daemonology.net/blog/2011-01-18-tarsnap-critical-security-bug.html).

### [OpenSSL](https://www.openssl.org/)
Very [difficult](https://blog.trailofbits.com/2020/05/29/detecting-bad-openssl-usage/) to use, let alone use correctly, offers access to algorithms and functions that you shouldn't use, the [documentation](https://www.openssl.org/docs/) is a mess, and lots of [vulnerabilities](https://www.openssl.org/news/vulnerabilities.html) have been found over the years.

These issues have led to OpenSSL [forks](https://www.libressl.org/index.html) and new, non-forked [libraries](https://bearssl.org/goals.html) that aim to be better alternatives if you need to implement TLS. However, OpenSSL is sadly the [standard](https://news.ycombinator.com/item?id=25346355) and often [relied upon](https://github.com/dotnet/runtime/issues/52482).

### Your [programming language](https://docs.microsoft.com/en-us/dotnet/api/system.security.cryptography?view=net-6.0)
Most programming languages provide access to old algorithms (e.g. MD5 and SHA1) that shouldn’t be used anymore instead of newer ones (e.g. BLAKE2, BLAKE3, and SHA3). Alongside missing or unnoticeable [warnings](https://docs.microsoft.com/en-us/dotnet/api/system.security.cryptography.sha1?view=net-6.0#remarks), this can lead to poor algorithm choices.

Furthermore, the APIs are typically easy to misuse, the documentation may fail to mention important security related information, and the implementations will be slower than libsodium.

However, certain languages, such as [Go](https://golang.org/) and [Zig](https://ziglang.org/) have impressive modern cryptography support.

### Other popular but unmentioned libraries
For example, [BouncyCastle](https://bouncycastle.org/) and [CryptoJS](https://cryptojs.gitbook.io/docs/). These again often provide or rely on dated algorithms and typically have bad documentation. For instance, CryptoJS uses an [insecure](https://www.npmjs.com/package/evp_bytestokey) KDF called [EVP_BytesToKey()](https://www.openssl.org/docs/man1.1.1/man3/EVP_BytesToKey.html) in OpenSSL when you pass a string password to [AES.encrypt()](https://cryptojs.gitbook.io/docs/#ciphers), and BouncyCastle has [no](https://github.com/bcgit/bc-csharp/wiki) C# documentation.

However, this avoidance recommendation is too broad really since there are *some* libraries that I haven't mentioned that are worth using, like [PASETO](https://github.com/paragonie/paseto) and various [RustCrypto](https://github.com/RustCrypto) libraries. Just do your research and assess the quality of the documentation. **There's no excuse for poor documentation**.

### [NaCl](https://nacl.cr.yp.to/)
An unmaintained, less modern, and more confusing version of libsodium and Monocypher. For example, [crypto_sign()](https://nacl.cr.yp.to/sign.html) for digital signatures has been experimental for many years. It also doesn’t have password hashing support and is [difficult to install/package](https://monocypher.org/why).

### [TweetNaCl](https://tweetnacl.cr.yp.to/)
Unmaintained, [slower](https://monocypher.org/speed) than Monocypher, doesn’t offer access to newer algorithms, doesn’t have password hashing, and [doesn’t zero out buffers](https://monocypher.org/why).

## Notes
### Older algorithms aren't necessarily better
You can argue that older algorithms are more battle-tested and therefore proven to be a safe choice, but the reality is that most modern algorithms, like [ChaCha20-Poly1305](https://www.rfc-editor.org/rfc/rfc8439.html), [BLAKE2](https://www.blake2.net/), and [Argon2](https://www.rfc-editor.org/rfc/rfc9106.html), have been properly analysed at this point and shown to offer security and performance [benefits](https://eprint.iacr.org/2019/1492.pdf) over their older counterparts.

Therefore, it doesn’t make sense to stick to this overly cautious mindset of avoiding newer algorithms unless they [lack](https://github.com/tscholl2/siec) analysis or are still candidates in a [competition](https://csrc.nist.gov/projects/post-quantum-cryptography) (e.g. new post-quantum algorithms), which do [need](https://csrc.nist.gov/CSRC/media/Projects/Post-Quantum-Cryptography/documents/round-1/official-comments/guess-again-official-comment.pdf) further analysis to be considered safe.

### Read the documentation
Don’t immediately jump into coding something because that’s how mistakes are made. Good libraries have high quality documentation that will explain potential security pitfalls and how to avoid them.

I also **strongly** recommend reading bits of [RFC standards](https://datatracker.ietf.org/doc/html/rfc2104) for algorithms you're using, particularly the ['Security Considerations'](https://datatracker.ietf.org/doc/html/rfc7748#section-7) sections.

### Be aware of dodgy design
If you're using a recommended library, then this probably won't be a problem. If you're not, some libraries do bad things like releasing unauthenticated plaintext, which shouldn't be touched, when using AEADs. For example, OpenSSL and BouncyCastle [apparently do](https://gotchas.salusa.dev/).

### Speed matters
It can make a noticeable difference for the user. For instance, a [C# Argon2 library](https://github.com/kmaragon/Konscious.Security.Cryptography) is going to be significantly slower than Argon2 in [libsodium](https://doc.libsodium.org/), meaning unnecessary and unwanted extra delay during key derivation.

Libsodium is the go-to for speed on desktops/servers, and Monocypher is the go-to for constrained environments (e.g. microcontrollers).