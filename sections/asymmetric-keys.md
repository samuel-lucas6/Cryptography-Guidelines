# Asymmetric Keys
## Use
### 256-bit ECC keys
This is the key size for [X25519](https://datatracker.ietf.org/doc/html/rfc7748), which provides a [~128-bit security level](https://crypto.stackexchange.com/questions/27771/does-curve25519-only-provide-112-bit-security).

Why am I recommending this when I recommend 256-bit keys (a 256-bit security level) for symmetric encryption? Because 128-bit security means [something different](https://github.com/LoupVaillant/Monocypher/issues/127#issuecomment-536200435) in the [case](https://loup-vaillant.fr/tutorials/128-bits-of-security) of these asymmetric algorithms.

Furthermore, [X25519](https://datatracker.ietf.org/doc/html/rfc7748) is faster, [more common](https://ianix.com/pub/curve25519-deployment.html), and [more accessible](https://en.wikipedia.org/wiki/Comparison_of_TLS_implementations#Supported_elliptic_curves) than [X448](https://datatracker.ietf.org/doc/html/rfc7748). Finally, when quantum computers do come along, [ECC](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography) and [RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)) ([with usable key sizes](https://crypto.stackexchange.com/a/88303)) will be broken regardless of the key size anyway, so many people feel [less of a need](https://github.com/LoupVaillant/Monocypher/issues/127#issuecomment-536200435) to use a higher security level curve.

### 448-bit ECC keys
If you insist on a higher security curve, this is the key size for [X448](https://datatracker.ietf.org/doc/html/rfc7748), which provides a [~224-bit security level](https://datatracker.ietf.org/doc/html/rfc7748#section-4.2). It's still [not](https://csrc.nist.gov/publications/detail/nistir/8105/final) post-quantum secure though.

### 3072-bit RSA keys
**If you’re forced to use [RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem))**, then you should use 3072-bit keys, which is the key size [currently used by the NSA](https://www.keylength.com/en/6/) and recommended by NIST, ECRYPT, and BSI for [near-term protection](https://www.keylength.com/en/3/).

The maximum should be 4096-bit because the performance is really bad after that. However, 4096-bit provides [little benefit](https://crypto.stackexchange.com/a/99235) over 3072-bit. 

**Just don’t use RSA.**

## Avoid
### 512-bit RSA keys
This was [broken ages ago](https://crypto.stackexchange.com/a/3933).

### 1024-bit RSA keys
These should be considered [**no longer](https://crypto.stackexchange.com/a/1982) [secure**](https://crypto.stackexchange.com/questions/2612/difficulty-of-breaking-rsa-for-a-given-key-size). 2048-bit ([112-bit security](https://crypto.stackexchange.com/a/1980)) is the recommended [minimum](https://www.keylength.com/en/8/) and 3072-bit gets you up to the [128-bit security level](https://crypto.stackexchange.com/questions/8687/security-strength-of-rsa-in-relation-with-the-modulus-size?noredirect=1&lq=1).

### 8192-bit RSA keys
These are [slow](https://www.javamex.com/tutorials/cryptography/rsa_key_length.shtml) to use and excessive to store.

### 2048-bit RSA keys
These only provide a [112-bit security level](https://crypto.stackexchange.com/questions/8687/security-strength-of-rsa-in-relation-with-the-modulus-size?noredirect=1&lq=1), which is below the standard [128-bit security level](https://loup-vaillant.fr/tutorials/128-bits-of-security).

Therefore, whilst still [commonly used](https://swiftsilentdeadly.com/protonmail-five-years-later-part-iii-security-features/) and safe as a **minimum** RSA key size, it makes sense to use 3072-bit keys instead.

### Post-quantum key sizes
These algorithms are [still being researched](https://csrc.nist.gov/projects/post-quantum-cryptography), and the key sizes can be [very large](https://www.bsi.bund.de/SharedDocs/Downloads/EN/BSI/Publications/Brochure/quantum-safe-cryptography.html?nn=433196) compared to those for [X25519](https://ianix.com/pub/curve25519-deployment.html)/[Ed25519](https://ianix.com/pub/ed25519-deployment.html).