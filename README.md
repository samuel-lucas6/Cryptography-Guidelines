# Cryptography Guidelines
![Creative Commons License Icon](https://i.creativecommons.org/l/by-sa/4.0/88x31.png) This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-sa/4.0/) because it took bloody ages to write.


---

## Background
This document outlines recommendations for cryptographic algorithm choices and parameters as well as important implementation details based on what I have learned from reading about the subject and the consensus I have observed online. Note that *some* knowledge of cryptography may be required to understand the terminology used in these guidelines.

My goal with these guidelines is to provide a resource that I wish I had access to when I first started writing programs related to cryptography. If this information helps prevent even just one vulnerability, then I consider it time well spent.

---

## Acknowledgments
These guidelines were inspired by [this](https://gist.github.com/atoponce/07d8d4c833873be2f68c34f9afc5a78a#file-gistfile1-md) Cryptographic Best Practices gist, Latacora's [Cryptographic Right Answers](https://latacora.singles/2018/04/03/cryptographic-right-answers.html), and [Crypto Gotchas](https://github.com/SalusaSecondus/CryptoGotchas), which is licensed under the [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/). The difference is that I mention newer algorithms and have tried to justify my algorithm recommendations whilst also offering important notes about using them correctly.

---

## Contribute
If you find these guidelines helpful, please **star** this repository and **share** the link around. Doing so might just prevent someone from making a catastrophic mistake.

If you have any **feedback**, please **contact me** privately [here](https://samuellucas.com/) or publicly [here](https://github.com/samuel-lucas6/Cryptography-Guidelines/discussions) to help improve these guidelines. [Pull requests](https://github.com/samuel-lucas6/Cryptography-Guidelines/pulls) are also welcome but please be prepared for things to be reworded.

---

## Disclaimer
I’m a psychology undergraduate with an interest in applied cryptography, not an experienced cryptographer. I primarily have experience with the [libsodium](https://doc.libsodium.org/) library since that’s what I’ve used for my projects, but I've also reported some security vulnerabilities related to cryptography.

Most experienced cryptographers don't have the time to write things like this, and the following information is freely available online or in books, so whilst more experience would be beneficial, I’m trying my best to provide accurate information that can be fact checked. **If I've made a mistake, please contact me to get it fixed**.

Note that the rankings are based on my opinion, algorithm availability in cryptographic libraries, and which algorithms are typically used in modern protocols, such as [TLS 1.3](https://www.davidwong.fr/tls13/), [Noise Protocol Framework](https://noiseprotocol.org/noise.html), [WireGuard](https://www.wireguard.com/protocol/), and so on. Such protocols and recommended practices make for the best guidelines because they’ve been approved by experienced professionals.

---

## General Guidance
1. Research, research, research: you often don’t need to know how cryptographic algorithms work under the hood to implement them correctly, just like how you don’t need to know how a car works to drive. However, you need to know enough about what you’re trying to do, which requires looking up relevant information online or in books, reading the documentation for the cryptographic library you’re using, reading RFC standards, reading helpful blog posts, and reading guidelines like this one. Furthermore, reading books about the subject in general will be beneficial, again like how knowing about cars can help if you break down. For a list of great resources, check out my [How to Learn About Cryptography](https://samuellucas.com/blog/how-to-learn-about-cryptography.html) blog post.

2. Check and check again: it’s your responsibility to get things right the first time around to the best of your ability rather than relying on peer review. Therefore, I **strongly** recommend always reading over security sensitive code at least twice and testing it to ensure that it’s operating as expected (e.g. checking the value of variables line by line using a debugger, using test vectors, etc).

3. Peer review is great but often doesn’t happen: unless your project is popular, you have a bug bounty program with cash rewards, or what you’re developing is for an organization, very few people, perhaps none at all, will look through the code to find and report vulnerabilities. Similarly, receiving funding for a code audit will probably be near impossible.

4. **Please don't create your own custom cryptographic algorithms (e.g. a custom cipher or hash function)**: this is like flying a Boeing 747 without a pilot license but worse because even experienced cryptographers design [insecure](https://competitions.cr.yp.to/sha3.html) algorithms, which is why cryptographic algorithms are thoroughly analyzed by a large number of cryptanalysts, usually as part of a [competition](https://competitions.cr.yp.to/index.html). By contrast, you rarely see experienced airline pilots crashing planes. The only *exception* to this rule is implementing something like Encrypt-then-MAC with secure, **existing** cryptographic algorithms **when you know what you're doing**.

5. **Please avoid coding existing cryptographic algorithms yourself (e.g. coding AES yourself)**: cryptographic libraries provide access to these algorithms for you to prevent people from making mistakes that cause vulnerabilities and to offer good performance. Whilst a select few algorithms are relatively simple to implement, like [HKDF](https://datatracker.ietf.org/doc/html/rfc5869), [many aren't](https://loup-vaillant.fr/articles/implementing-elligator) and require a great deal of experience to implement correctly. Lastly, another reason to avoid doing this is that it's really not fun since academic papers and reference implementations can be very difficult to understand.

---
