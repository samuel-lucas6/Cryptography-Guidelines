
[ TLS Implementation ]: https://en.wikipedia.org/wiki/Comparison_of_TLS_implementations#Supported_elliptic_curves
[ Elliptic Curves ]: https://en.wikipedia.org/wiki/Elliptic-curve_cryptography
[ Key Length 6 ]: https://www.keylength.com/en/6/
[ Key Length 6 ]: https://www.keylength.com/en/3/
[ Diffie Hellman ]: https://en.wikipedia.org/wiki/Elliptic-curve_Diffie%E2%80%93Hellman


## Asymmetric Key Size


#### Use 「 Ordered 」

1. 256-bit keys: the key size for X25519, which provides a ~128-bit security level. Why am I recommending this when I recommend 256-bit keys (a 256-bit security level) for symmetric encryption? Because X25519 is faster, more common, and [more accessible][ TLS Implementation ] than X448. If quantum computers do come along, then [ECC][ Elliptic Curves ] and RSA will be broken regardless of the key size anyway, so many people feel less of a need to use a higher security level curve considering that 128-bit security is currently enough.

2. 456-bit keys: the key size for X448, which provides a 224-bit security level.

3. 3072-bit or 4096-bit keys: **if you’re forced to use RSA**, then the minimum key size should be 3072-bit, which is the key size [currently used by the NSA][ Key Length 6 ] and recommended by ECRYPT for [near term protection][ Key Length 3 ]. The maximum should be 4096-bit because the performance is really bad after that. However, **seriously don’t use RSA!**


---

#### Avoid 「 Unordered | All Unsuitable 」

- 1024-bit keys: these are **no longer secure**.

- 2048-bit keys: these only provide a 112-bit security level, which is below the standard 128-bit security level. Therefore, whilst commonly used and still safe as a **minimum** RSA key size, it makes sense to use 3072-bit keys instead.

- 8192-bit keys: these are slow to generate and excessive to store.

- Post-quantum algorithm key sizes: these algorithms are still being researched, and the key sizes are very large compared to those for [ECDH][ Diffie Hellman ].
