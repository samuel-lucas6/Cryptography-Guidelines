# General Guidance
It’s your responsibility to get things right the first time around to the best of your ability rather than relying on peer review that may never happen.

### Use existing, analysed cryptographic algorithms
**Please don't create your own custom cryptographic algorithms (e.g. a custom cipher or hash function)**.

This is like flying a Boeing 747 without a pilot license but worse because **even experienced cryptographers design [insecure](https://competitions.cr.yp.to/sha3.html) algorithms**, which is why cryptographic algorithms are thoroughly analysed by a large number of cryptanalysts, usually as part of a [competition](https://competitions.cr.yp.to/index.html). By contrast, you rarely see experienced airline pilots crashing planes.

The only *exception* to this rule is implementing something like Encrypt-then-MAC with secure, **existing** cryptographic algorithms **when you know what you're doing**.

### Use popular cryptographic libraries
**Please avoid coding existing cryptographic algorithms yourself (e.g. coding AES yourself)**.

Cryptographic libraries provide access to these algorithms for you to prevent people from making mistakes that cause vulnerabilities and to offer good performance.

Whilst a select few algorithms are relatively simple to implement, like [HKDF](https://datatracker.ietf.org/doc/html/rfc5869), [many aren't](https://loup-vaillant.fr/articles/implementing-elligator) and require a great deal of experience to implement correctly.

Another reason to avoid doing this is that it's not much fun since academic papers and reference implementations can be very difficult to understand.

### Read
You often don’t need to know how cryptographic algorithms work under the hood to implement them correctly, just like how you don’t need to know how a car works to drive.

However, you need to know enough about what you’re trying to do, which requires reading:
- The [documentation](https://doc.libsodium.org/) for the cryptographic library you’re using
- [RFC standards](https://datatracker.ietf.org/doc/html/rfc2104) for algorithms you're using, particularly the ['Security Considerations'](https://datatracker.ietf.org/doc/html/rfc7748#section-7) sections
- Helpful [blog](https://neilmadden.blog/2018/11/14/public-key-authenticated-encryption-and-why-you-want-it-part-i/) [posts](https://soatok.blog/2021/11/17/understanding-hkdf/) and [Cryptography Stack Exchange](https://crypto.stackexchange.com/) answers
- [Guidelines](https://gotchas.salusa.dev/) like this one
- Relevant information in [books](https://www.manning.com/books/real-world-cryptography)

Ideally, **consult multiple resources** to ensure the information is accurate. For instance, some information on Cryptography Stack Exchange is misleading. RFCs, blogs, and books offer higher quality information.

Furthermore, reading books about the subject in general will be beneficial, again like how knowing about cars can help if you break down. For a list of great resources, check out my [How to Learn About Cryptography](https://samuellucas.com/blog/how-to-learn-about-cryptography.html) blog post. Soatok also has a [great](https://soatok.blog/2020/06/10/how-to-learn-cryptography-as-a-programmer/) blog post.

### Double check
To prevent code-related mistakes, you should:
- Read security sensitive code twice
- Test your code to ensure that it’s operating as expected (e.g. using test vectors, unit tests, debugging, etc)

### Peer review
Unless your project is popular, you have a bug bounty program with cash rewards, or what you’re developing is for an organisation, very few people, perhaps none, will look through the code to find and report vulnerabilities.

Similarly, receiving funding for a code audit will probably be impossible. Organisations providing funding typically dish it out to large projects like [Tor](https://www.opentech.fund/results/supported-projects/). If you want to fund it yourself, it'll probably cost you $5,000-$10,000 for a small project, which is not worth the money.