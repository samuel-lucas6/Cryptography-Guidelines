
## Key Exchange/Hybrid Encryption


#### Use (in order):

1. [Curve25519/X25519](https://en.wikipedia.org/wiki/Curve25519): popular, fast, easy to implement, fixes some issues with NIST curves, not designed by NIST, and offers ~128-bit security.

2. [Curve448/X448](https://en.wikipedia.org/wiki/Curve448): less popular and slower than X25519 but provides a 224-bit security level and is also not made by NIST.

3. [Pre-shared symmetric keys](https://en.wikipedia.org/wiki/Pre-shared_key): this approach allows for [post-quantum security](https://media.defense.gov/2021/Aug/04/2002821837/-1/-1/1/Quantum_FAQs_20210804.PDF) and can be combined [alongside](https://www.wireguard.com/protocol/#key-exchange-and-data-packets) an asymmetric key exchange. However, using pre-shared keys can be difficult since the key must be kept secret, whereas public keys are meant to be public and can therefore be easily shared.


---

#### Avoid (not in order because they’re all bad):

- [Plain RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)#Attacks_against_plain_RSA), [RSA PKCS#1 v1.5](https://en.wikipedia.org/wiki/RSA_(cryptosystem)#Padding_schemes), [RSA-KEM](https://en.wikipedia.org/wiki/Key_encapsulation), and [RSA-OAEP](https://en.wikipedia.org/wiki/Optimal_asymmetric_encryption_padding): plain/textbook RSA is **insecure** for [several reasons](https://en.wikipedia.org/wiki/RSA_(cryptosystem)#Attacks_against_plain_RSA), RSA PKCS#1 v1.5 is also **vulnerable** to some [attacks](https://en.wikipedia.org/wiki/RSA_(cryptosystem)#Padding_schemes), and RSA-KEM and RSA-OAEP, whilst both secure *when* [implemented correctly](https://paragonie.com/blog/2018/04/protecting-rsa-based-protocols-against-adaptive-chosen-ciphertext-attacks), are still **worse than using hybrid encryption** because asymmetric encryption is slower, designed for small messages, doesn’t provide sender authentication without signatures, and requires larger keys. RSA-KEM is also never used and very rarely available in cryptographic libraries.

- [ElGamal](https://en.wikipedia.org/wiki/ElGamal_encryption): old, very rarely used, can only be used on small messages, produces a ciphertext that’s larger than the plaintext, the design is [malleable](https://en.wikipedia.org/wiki/Malleability_%28cryptography)), it's slower than hybrid encryption, and it doesn’t provide sender authentication without signatures.

- [NIST curves](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography#Fast_reduction_(NIST_curves)) (e.g. P-256, P-384, and P-512): although P-256 is probably the most popular curve, the seeds for these curves [haven’t been explained](https://datatracker.ietf.org/doc/html/rfc8031#section-4), which is [not a good look](https://safecurves.cr.yp.to/rigid.html) considering that [Dual_EC_DRBG](https://en.wikipedia.org/wiki/Dual_EC_DRBG) was a NIST standard despite containing an [NSA backdoor](https://en.wikipedia.org/wiki/Dual_EC_DRBG#Weakness:_a_potential_backdoor). Furthermore, these curves require [point validation](https://crypto.stackexchange.com/questions/51320/ecdh-check-points), are [harder to write implementations for](https://safecurves.cr.yp.to/), meaning libraries are more likely to contain vulnerabilities, and are slower than Curve25519/X25519, which has become increasingly popular over recent years (e.g. it's used in [TLS 1.3](https://en.wikipedia.org/wiki/Transport_Layer_Security#TLS_1.3)). **These should only be used for interoperability reasons**.

- Other curves (e.g. [Curve41417](https://eprint.iacr.org/2014/526.pdf)): these are often [rarely used/available](https://en.wikipedia.org/wiki/Comparison_of_TLS_implementations#Supported_elliptic_curves) compared to Curve25519/X25519, P-256, P-384, and P-512. Please see the [SafeCurves](https://safecurves.cr.yp.to/index.html) tables for a security comparison of most curves.

- [SRP](https://en.wikipedia.org/wiki/Secure_Remote_Password_protocol), [J-PAKE](https://en.wikipedia.org/wiki/Password_Authenticated_Key_Exchange_by_Juggling), and other [PAKE](https://en.wikipedia.org/wiki/Password-authenticated_key_agreement) protocols: note that these are only for password-based authenticated key exchange. SRP has an [odd design](https://blog.cryptographyengineering.com/should-you-use-srp/), no security proof, leaks the salt to untrusted users, requires more bandwidth and computation than [ECDH](https://en.wikipedia.org/wiki/Elliptic-curve_Diffie%E2%80%93Hellman), and old versions contained vulnerabilities. Some PAKEs require the user to store the password on the server or reveal the salt to an attacker, allowing for [pre-computation attacks](https://blog.cryptographyengineering.com/2018/10/19/lets-talk-about-pake/). Furthermore, very few cryptographic libraries include PAKEs, which makes good ones, like [OPAQUE](https://blog.cryptographyengineering.com/2018/10/19/lets-talk-about-pake/), difficult to recommend [until they receive more adoption](https://blog.cloudflare.com/opaque-oblivious-passwords/).

- [Post-quantum algorithms](https://csrc.nist.gov/projects/post-quantum-cryptography): these are still being researched, aren’t implemented in mainstream libraries, are much slower than existing algorithms, and typically have very large key sizes. However, it will eventually make sense to switch to one in the future. For now, if post-quantum security is a goal, then use a pre-shared symmetric key if possible.


---

#### Notes:

1. Public keys should be shared, and **private keys must be kept secret**: **never** share private keys. Please see point 9 below for details about secure storage of private keys.

2. **Never hard-code private keys into source code**: these can be easily retrieved.

3. For hybrid encryption, use one of the recommended key exchange algorithms above with one of the recommended [symmetric encryption algorithms](#symmetric-encryption): for example, use X25519 with ChaCha20-Poly1305.

4. Use one of the recommended (non-password-based) KDFs on the shared secret: **shared secrets are not suitable for use as secret keys directly** because they’re not uniformly random. Moreover, you should derive unique keys each time by using the salt and context parameters, as explained in the [(Non-Password-Based) Key Derivation Functions](#non-password-based-key-derivation-functions) section.

5. When using counter nonces for encryption, use different keys for different directions in a client-server scenario: after computing the shared secret, you can use a non-password-based KDF to derive two 256-bit keys as follows: `HKDF-SHA512(inputKeyingMaterial: sharedSecret, outputLength: 64, salt: null, info: clientPublicKey || serverPublicKey)`, splitting the output in two. One key should be used by the client for sending data to the server, and the other should be used by the server for sending data to the client. Both keys need to be calculated by the client and server. This approach allows counter nonces to be used [safely](https://doc.libsodium.org/key_exchange#notes) for encryption without having to wait for an acknowledgement after every message.

6. X25519 and X448 public keys can be distinguished from random data: if you need to obfuscate public keys so that they’re indistinguishable from random noise, then you need to use [Elligator 2](https://elligator.cr.yp.to/). Note that other metadata (e.g. the number of bytes in a packet) can reveal the use of cryptography too, so you should consider padding such information using a scheme like [PADME](https://bford.info/pub/sec/purb.pdf).

7. Use an **authenticated key exchange** in most non-interactive/offline protocols: the Noise protocol framework K and X one-way handshake [patterns](http://noiseprotocol.org/noise.html#one-way-handshake-patterns), as explained [here](https://neilmadden.blog/2018/11/26/public-key-authenticated-encryption-and-why-you-want-it-part-ii/), are perfect for non-interactive/offline protocols. These achieve sender and recipient authentication whilst preventing a compromise of the sender’s private key leading to an attacker being able to decrypt the ciphertext.

8. Opt for **forward secrecy** when possible in interactive/online protocols: this prevents a compromise of a long-term private key leading to a compromise of a session key, which is the strongest security guarantee you can achieve. This can be implemented using the Noise KK or IK interactive [handshakes](https://noiseprotocol.org/noise.html#interactive-handshake-patterns-fundamental).

9. **Store private keys encrypted**: when storing a private key in a file, you should always encrypt it with a strong password for protection at rest. Things become more complicated for interactive/online scenarios, with physical or virtual [hardware security modules (HSMs)](https://en.wikipedia.org/wiki/Hardware_security_module) and key vaults, such as [AWS Key Management Service (KMS)](https://aws.amazon.com/kms/), sometimes being used. These types of solutions are generally regarded as more secure than storing keys in encrypted configuration files and allow for easy key rotation, but using a KMS requires trusting a third party.

10. Key pairs should be rotated: if a private key has or may have been compromised, then a new key pair should be generated. Similarly, you should consider rotating your keys after a set period of time (a [cryptoperiod](https://en.wikipedia.org/wiki/Cryptoperiod)) has elapsed.
