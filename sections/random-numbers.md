# Random Numbers
## Use
### An operating system CSPRNG
[CSPRNG](https://en.wikipedia.org/wiki/Cryptographically-secure_pseudorandom_number_generator) stands for **cryptographically secure** pseudorandom number generator. Your [programming language](https://docs.microsoft.com/en-us/dotnet/api/system.security.cryptography.randomnumbergenerator?view=net-6.0) or [cryptographic library](https://doc.libsodium.org/generating_random_data) should call the operating system CSPRNG for you.

Here's a list of functions/classes for some major programming languages:
- Python: [secrets](https://docs.python.org/3/library/secrets.html)
- JavaScript: [Crypto.getRandomValues()](https://developer.mozilla.org/en-US/docs/Web/API/Crypto/getRandomValues)
- C#: [RandomNumberGenerator()](https://docs.microsoft.com/en-us/dotnet/api/system.security.cryptography.randomnumbergenerator?view=net-6.0)
- Java: [SecureRandom()](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/security/SecureRandom.html)
- Go: [crypto/rand](https://pkg.go.dev/crypto/rand)
- Rust: [rand](https://crates.io/crates/rand)
- PHP: [random](https://www.php.net/manual/en/ref.csprng.php)
- Swift: [SystemRandomNumberGenerator](https://developer.apple.com/documentation/swift/systemrandomnumbergenerator)

You may also find [this](https://paragonie.com/blog/2016/05/how-generate-secure-random-numbers-in-various-programming-languages) blog post useful, which mentions some other languages and goes into greater detail.

Avoid calling the operating system CSPRNG yourself if you can help it. If you need to for some reason, here's a list:
- Windows: [BCryptGenRandom](https://docs.microsoft.com/en-us/windows/win32/api/bcrypt/nf-bcrypt-bcryptgenrandom)
- Linux/macOS: [getrandom()](https://man7.org/linux/man-pages/man2/getrandom.2.html) if available or <sup>*</sup>[/dev/urandom](https://linux.die.net/man/4/urandom) otherwise
- OpenBSD: [arc4random()](https://man.openbsd.org/arc4random.3)

<sup>*</sup>[/dev/random](https://en.wikipedia.org/wiki//dev/random) 'creates more problems than it solves' - [Jean-Philippe Aumasson](https://www.aumasson.jp/) in [Serious Cryptography](https://nostarch.com/seriouscrypto). Specifically, it causes performance/denial-of-service issues. See [this](https://www.2uo.de/myths-about-urandom/) if you don't have a copy.

On embedded devices, allow a library like [LibHydrogen](https://github.com/jedisct1/libhydrogen) to handle random number generation for you.

## Avoid
### A regular PRNG
This means a **non-cryptographically secure** pseudorandom number generator. **These are not secure and should not be used for anything related to security**.

For example, avoid the following:
- Python: [random](https://docs.python.org/3/library/random.html)
- JavaScript: [Math.random()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/random)
- C#: [Random.Next()](https://docs.microsoft.com/en-us/dotnet/api/system.random.next?view=net-6.0)
- Java: [Random()](https://docs.oracle.com/javase/8/docs/api/java/util/Random.html) or [Math.random()](https://docs.oracle.com/javase/8/docs/api/java/lang/Math.html#random--)
- Go: [math/rand](https://pkg.go.dev/math/rand)
- PHP: [rand()](https://www.php.net/manual/en/function.rand.php)

### A custom/userspace PRNG
This is [**very](https://nakedsecurity.sophos.com/2013/07/09/anatomy-of-a-pseudorandom-number-generator-visualising-cryptocats-buggy-prng/) [likely**](https://www.cryptofails.com/post/72902772336/how-not-to-csprng) going to be [**insecure**](https://hdm.io/tools/debian-openssl/) because it’s [harder](https://monocypher.org/manual/#Random_number_generation) to do properly than you’d think. For instance:
- You need to mix different entropy sources (e.g. mouse movements, temperature readings, RDRAND, etc) together to produce a seed
- However, entropy is difficult to acquire at boot since some devices will result in the same noise
- You want to ensure forward secrecy to prevent an attacker retrieving previously generated random numbers
- You want to reseed periodically. Reinjecting new entropy provides backward/future secrecy to prevent a state compromise allowing future random numbers to be predicted
- Program forks result in a child process that shares the state with the parent process, meaning identical output unless forks use different seeds

**Just trust the operating system CSPRNG**. Chances are you'd seed a custom PRNG with it anyway.

If you know what you're doing and you're forced to implement your own, make sure it's a [fast-key-erasure](https://blog.cr.yp.to/20170723-random.html) one. If you don't understand everything on that page, you probably shouldn't risk it as good randomness is the foundation for lots of other cryptography.

## Notes
### Output size
Salts are [typically](https://www.rfc-editor.org/rfc/rfc9106.html#section-3.1) 128 bits long. This length ensures a collision is very [unlikely](https://crypto.stackexchange.com/a/56132). [Some](https://doc.libsodium.org/password_hashing/default_phf#key-derivation) cryptographic libraries won't let you change this for things like [password hashing](password-hashing-password-based-key-derivation.md).

However, if you want to be conservative, then you can use 256-bit random values for IDs, salts, and so on. This reduces the chances of a collision into the realm of [never having anything to worry about](https://crypto.stackexchange.com/a/27828).

### Virtual machines
If you generate random numbers inside a virtual machine (VM) and the VM state is saved and later restored, the same random numbers may be generated.