
## Random Numbers


#### Use 「 Ordered 」

1. The **cryptographically secure** pseudorandom number generator (CSPRNG) in your programming language or cryptographic library: these should use the operating system’s CSPRNG. For example, [RNGCryptoServiceProvider](https://docs.microsoft.com/en-us/dotnet/api/system.security.cryptography.rngcryptoserviceprovider?view=net-5.0) in C#.

2. [Fast key erasure](https://blog.cr.yp.to/20170723-random.html) **on embedded systems**: this should be **a last resort** because it’s hard to erase keys properly. **A lot can go wrong if you don’t know what you’re doing**. [Here's](https://github.com/WebAssembly/wasi-libc/blob/main/libc-top-half/sources/arc4random.c) an example ChaCha20 RNG implementation.


---

#### Avoid 「 Unordered | All Unsuitable 」

- A **non-cryptographically secure** pseudorandom number generator: for example, [Math.random()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/random) in JavaScript, [Random.Next()](https://docs.microsoft.com/en-us/dotnet/api/system.random.next?view=net-5.0) in C#, [Random()](https://docs.oracle.com/javase/8/docs/api/java/util/Random.html) in Java, and so on. **These are not secure and should not be used for anything related to security**.

- A custom RNG: this is **likely** going to be **insecure** because it’s harder to do properly than you’d think. **Just trust the operating system’s CSPRNG**.
