## Asymmetric Key Size
#### Use (in order):
1. 256-bit keys: the key size for X25519, which provides a ~128-bit security level. Why am I recommending this when I recommend 256-bit keys (a 256-bit security level) for symmetric encryption? Because X25519 is faster, more common, and [more accessible](https://en.wikipedia.org/wiki/Comparison_of_TLS_implementations#Supported_elliptic_curves) than X448. If quantum computers do come along, then [ECC](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography) and RSA will be broken regardless of the key size anyway, so many people feel less of a need to use a higher security level curve considering that 128-bit security is currently enough.

2. 456-bit keys: the key size for X448, which provides a 224-bit security level.

3. 3072-bit or 4096-bit keys: **if you’re forced to use RSA**, then the minimum key size should be 3072-bit, which is the key size [currently used by the NSA](https://www.keylength.com/en/6/) and recommended by ECRYPT for [near term protection](https://www.keylength.com/en/3/). The maximum should be 4096-bit because the performance is really bad after that. However, **seriously don’t use RSA!**

#### Avoid (not in order because they’re all bad):
1. 1024-bit keys: these are **no longer secure**.

2. 2048-bit keys: these only provide a 112-bit security level, which is below the standard 128-bit security level. Therefore, whilst commonly used and still safe as a **minimum** RSA key size, it makes sense to use 3072-bit keys instead.

3. 8192-bit keys: these are slow to generate and excessive to store.

4. Post-quantum algorithm key sizes: these algorithms are still being researched, and the key sizes are very large compared to those for [ECDH](https://en.wikipedia.org/wiki/Elliptic-curve_Diffie%E2%80%93Hellman).

---

## Concluding Remarks
I believe there are three main areas of improvement when it comes to individuals with experience in cryptography helping developers:

1. Cryptographic libraries **should** be better: most don’t make it easy to use cryptography safely (e.g. they support insecure algorithms, require nonces, etc) and have [horrible documentation](https://www.openssl.org/docs/). This shouldn’t be the case, and there really should be greater uproar about this. Things need to be secure by default (e.g. insecure algorithms should never be implemented or get removed), and the documentation needs to be readable, as in concise, helpful to people of all skill levels, presented on a modern looking website rather than using [basic HTML](https://nacl.cr.yp.to/index.html) or [a bunch of files on GitHub](https://github.com/google/tink/tree/master/docs), and easy to navigate (e.g. supporting search functionality like [GitBook](https://doc.libsodium.org/) does).

2. People **should** stop saying 'don't roll your own crypto': **repeating this phrase doesn't help anyone**. Instead, educate developers about how to do things properly, whether that be by answering questions on [Cryptography Stack Exchange](https://crypto.stackexchange.com/) in an understandable manner, writing a [blog](https://soatok.blog/), or replying to emails from people asking for help. It's not a crime to implement Encrypt-then-MAC, and even when someone writes a custom cipher, **you should explain why that's not a good idea** (e.g. 'professional cryptographers still design insecure algorithms').

3. There **should** be more peer review: it's often difficult to receive peer review, impossible to fund a bug bounty program with cash rewards, and extremely unlikely for projects to get funding for a code audit. Whilst developers who fail to do any reading related to cryptography obviously deserve criticism, [even experienced professionals make mistakes](https://github.com/agl/ed25519/issues/27). Simple peer review (e.g. using the search on GitHub for things like 'HMAC' and 'ECB') helps catch things that are easy to spot, and more thorough peer review helps catch things that even someone experienced [might have missed](https://github.com/str4d/rage/issues/195). If something seems dodgy, then you should investigate if possible.
