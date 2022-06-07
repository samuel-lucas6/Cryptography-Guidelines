# Cryptography Guidelines
Are you a developer in need of some crypto? If so, you've come to the right place!

These guidelines outline:
- Cryptographic library recommendations
- Cryptographic algorithm recommendations
- Parameter recommendations
- Important implementation details

Parts are opinion-based, but most of this information is derived from expert recommendations alongside real-world protocols and applications designed by cryptographers and cryptography engineers.

Importantly, unlike some other guidelines online, justification is provided for why certain libraries and algorithms are preferable. This helps with learning and enables fact checking, allowing you to ultimately come to your own conclusions.

In general, **boring is better, whereas complexity risks catastrophe**. With more complicated designs, contacting a cryptography engineer is strongly recommended.

Note that some knowledge of cryptography is required to understand the terminology used in these guidelines. For learning resources, check out [this](https://samuellucas.com/blog/how-to-learn-about-cryptography.html) and [this](https://soatok.blog/2020/06/10/how-to-learn-cryptography-as-a-programmer/) blog post.

## Contents
1. [General Guidance](sections/general-guidance.md)
2. [Cryptographic Libraries](sections/cryptographic-libraries.md)
3. [Symmetric Encryption](sections/symmetric-encryption.md)
4. [Message Authentication Codes](sections/message-authentication-codes.md)
5. [Symmetric Key Size](sections/symmetric-key-size.md)
6. [Random Numbers](sections/random-numbers.md)
7. [Hashing](sections/hashing.md)
8. [Password Hashing/Password-Based Key Derivation](sections/password-hashing-password-based-key-derivation.md)
9. [(Non-Password-Based) Key Derivation Functions](sections/non-password-based-key-derivation-functions.md)
10. [Key Exchange/Hybrid Encryption](sections/key-exchange-hybrid-encryption.md)
11. [Digital Signatures](sections/digital-signatures.md)
12. [Asymmetric Key Size](sections/asymmetric-key-size.md)
13. [Concluding Remarks](sections/concluding-remarks.md)
14. [Acknowledgements](sections/acknowledgements.md)

## Contribute
If you find these guidelines helpful, please **star** this repository and **share** the link around. Doing so might just prevent someone from making a catastrophic mistake.

If you have any **feedback or corrections**, please **contact me** privately [here](https://samuellucas.com/) or publicly [here](https://github.com/samuel-lucas6/Cryptography-Guidelines/discussions) to help improve these guidelines. [Pull requests](https://github.com/samuel-lucas6/Cryptography-Guidelines/pulls) are also welcome but please be prepared for things to be reworded.

## License
![Creative Commons License Icon](https://i.creativecommons.org/l/by-sa/4.0/88x31.png) This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-sa/4.0/) because it took bloody ages to write.