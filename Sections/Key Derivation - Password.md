[ Argon2id ]: https://en.wikipedia.org/wiki/Argon2
[ Password Hashing ]: https://www.password-hashing.net/
[ Easy To Use ]: https://doc.libsodium.org/password_hashing/default_phf
[ Scrypt ]: https://en.wikipedia.org/wiki/Scrypt
[ Bcrypt ]: https://en.wikipedia.org/wiki/Bcrypt
[ Cache Timing Attack ]: https://crypto.stanford.edu/cs359c/17sp/projects/MarkAnderson.pdf
[ Strong Algorithm  ]: https://www.tarsnap.com/scrypt/scrypt.pdf
[ Maximum Password Length ]: https://en.wikipedia.org/wiki/Bcrypt#Maximum_password_length
[ Base64 Pre Hash ]: https://paragonie.com/blog/2015/04/secure-authentication-php-with-long-term-persistence
[ Bcypt Collisions ]: https://blog.ircmaxell.com/2015/03/security-issue-combining-bcrypt-with.html
[ Password Shucking ]: https://www.youtube.com/watch?v=OQD3qDYMyYQ
[ Output Length Attack ]: https://blog.1password.com/1password-hashcat-strong-master-passwords/
[ MD5 ]: https://en.wikipedia.org/wiki/MD5
[ SHA1 ]: https://en.wikipedia.org/wiki/SHA-1
[ SHA2 ]: https://en.wikipedia.org/wiki/SHA-2
[ Length Extension Attack ]: https://en.wikipedia.org/wiki/Length_extension_attack
[ Password Derive Bytes ]: https://docs.microsoft.com/en-us/dotnet/api/system.security.cryptography.passwordderivebytes?view=net-5.0
[ PBKDF1 Broken ]: https://crypto.stackexchange.com/questions/1842/how-does-pbkdf1-work
[ SHAcrypt ]: https://en.wikipedia.org/wiki/Crypt_(C)#SHA2-based_scheme
[ SHAcrypt Weaker ]: https://www.akkadia.org/drepper/SHA-crypt.txt
[ PBKDF ]: https://en.wikipedia.org/wiki/PBKDF2
[ Many Iterations ]: https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html#pbkdf2
[ Argon2i ]: https://en.wikipedia.org/wiki/Argon2
[ Argon2 Analysis ]: https://en.wikipedia.org/wiki/Argon2#Cryptanalysis
[ Argon2 Iterations ]: https://mailarchive.ietf.org/arch/msg/cfrg/beOzPh41Hz3cjl5QD7MSRNTi3lA/
[ Argon2 11 Iterations ]: https://eprint.iacr.org/2016/759.pdf
[ Argon2 Weaker ]: https://www.rfc-editor.org/rfc/rfc9106.html#name-security-against-time-space
[ RFC9106 ]: https://www.rfc-editor.org/rfc/rfc9106.html
[ Password Cracking ]: https://en.wikipedia.org/wiki/Password_cracking
[ Rainbow Tables ]: https://en.wikipedia.org/wiki/Rainbow_table
[ Timing Attacks ]: https://en.wikipedia.org/wiki/Timing_attack
[ Done For You ]: https://doc.libsodium.org/password_hashing/default_phf#password-storage
[ Server Relief ]: https://doc.libsodium.org/password_hashing#server-relief
[ Encryption Usage ]: https://bitwarden.com/help/article/what-encryption-is-used/#pbkdf2
[ Keeping Passwords ]: https://about.fb.com/news/2019/03/keeping-passwords-secure/
[ Long Passwords ]: https://www.djangoproject.com/weblog/2013/sep/15/security/
[ Rate Limiting ]: https://www.cloudflare.com/en-gb/learning/bots/what-is-rate-limiting/
[ Hash Then Encrypt ]: https://github.com/paragonie/password_lock#readme
[ Pepper ]: https://en.wikipedia.org/wiki/Pepper_(cryptography)
[ Key File ]: https://www.kryptor.co.uk/tutorial#using-a-keyfile
[ Disk ]: https://veracrypt.fr/en/Keyfiles%20in%20VeraCrypt.html


## Password Hashing/Password-Based Key Derivation


#### Use 「 Ordered 」

1. [Argon2id][ Argon2id ] (64+ MiB of RAM, 3+ iterations, and 1+ parallelism): winner of the [Password Hashing Competition][ Password Hashing ] in 2015, widely used and recommended now, and very [easy to use][ Easy To Use ] in libraries like libsodium. Use as high of a memory size as possible and then as many iterations as possible to reach a suitable delay for your use case (e.g. a delay of 500 milliseconds for server authentication, 1 second for file encryption, 3-5 seconds for disk encryption, etc).

2. [scrypt][ Scrypt ] (N=32768, r=8, p=1 and higher): the parameters are more confusing and less scalable than Argon2, and it’s susceptible to [cache-timing attacks][ Cache Timing Attack ]. However, it’s still a [strong algorithm][ Strong Algorithm ] when configured correctly.

3. [bcrypt][ Bcrypt ] (12+ work factor): note that **this is not a KDF** because the output length cannot be adjusted. **Only use this for password hashing when none of the better algorithms are available**. It’s better than PBKDF2 in terms of [resisting GPU/ASIC attacks][ Strong Algorithm ], except for long passwords (please keep reading), but trickier to implement correctly and worse than Argon2 and scrypt in that it requires much less memory, with the amount of memory being fixed rather than adjustable. Unfortunately, it only uses the [first 55 characters of a password][ Strong Algorithm ] and has a stupid password length limit of [72 characters][ Maximum Password Length ], meaning people often prehash the password using something like SHA2 to support longer passwords. However, this can lead to [password shucking][ Password Shucking ] when using a weak hash function (e.g. MD5, which should **never** be used for anything anyway) and null bytes in the hash allowing an attacker to find [collisions][ Bcrypt Collisions ], speeding up attacks. Therefore, you should [Base64 encode the prehash][ Base64 Pre Hash ] before passing it to bcrypt.

4. [PBKDF2-SHA512][ PBKDF2 ] (120,000+ iterations): **only use this when none of the better algorithms are available** or due to compatibility restraints because it can be [efficiently bruteforced][ Strong Algorithm ] using GPUs and ASICs when not using a high iteration count. Note that it’s generally recommended not to ask for more than the output length of the underlying hash function because this can lead to [attacks][ Output Length Attack ]. Instead, if that’s required, use PBKDF2 first to get the output length of the underlying hash function (64 bytes with PBKDF2-SHA512) before calling a non-password-based KDF, like HKDF-Expand, with the PBKDF2 output as the input keying material (IKM) to derive more output.


---

#### Avoid 「 Unordered | All Unsuitable 」

- Storing passwords in plaintext: **this is a recipe for disaster**. If your password database is ever compromised, all your users are screwed, and your reputation in terms of security will go down the drain as well.

- Using a password as a key (e.g. `key = Encoding.UTF8.GetBytes(password)`): firstly, passwords are low in entropy, whereas **cryptographic keys need to be high in entropy**. Secondly, not using a password-based KDF with a random salt means **attackers can quickly bruteforce passwords** and users using the same password will end up using the same key.

- Using a regular/fast hash function (e.g. [MD5][ MD5 ], [SHA1][ SHA1 ], [SHA2][ SHA2 ], etc): **these are not suitable for password hashing** because they’re not slow, which allows for **fast bruteforce attacks**. Password hashing also requires using a salt to protect against attacks using precomputed hashes and to prevent the same password always having the same hash. However, adding a salt to certain regular hash functions, such as SHA2, can lead to [length extension attacks][ Length Extension Attack ], as discussed in point 3 of the Notes in the [Hashing](#hashing) section.

- Encrypting passwords: **encryption is reversible, whereas hashing is not**. If an attacker compromises a password database and obtains a password hash, then they don’t know the password without computing the hash. By contrast, if an attacker compromises a password database and the relevant encryption key(s), then they can easily obtain the plaintext passwords. Encryption would also reveal the password length unless you padded the input.

- [PBKDF1][ PBKDF ]: **never use this** as it was **superseded by PBKDF2** and can only derive keys up to 160-bits, which is basically not suitable for anything. Some implementations, such as [PasswordDeriveBytes()][ Password Derive Bytes ] in C#, are also [completely broken][ PBKDF1 Broken ].

- [SHAcrypt][ SHAcrypt ]: it's [weaker][ SHAcrypt Weaker ] than the recommended algorithms, nobody uses this, and I’ve never even seen it in a cryptographic library.

- [PBKDF2-MD5][ PBKDF ], [PBKDF2-SHA1][ PBKDF ], [PBKDF2-SHA256][ PBKDF ], and [PBKDF2-SHA384][ PBKDF ]: **use SHA512 if you must use PBKDF2**. MD5 and SHA1 are old hash functions that **should not be used anymore**. Then PBKDF2-SHA256 and PBKDF2-SHA384 require [significantly more iterations][ Many Iterations ] than PBKDF2-SHA512 to be secure and have a smaller block size, meaning long passwords may get prehashed.

- [Argon2i][ Argon2i ] with less than 3 iterations: unlike Argon2id and Argon2d, Argon2i has been [attacked][ Argon2 Analysis ], with [3+ iterations][ Argon2 Iterations ] being required for the attack to not be efficient and [11+ iterations][ Argon2 11 Iterations ] being required for the attack to completely fail. Argon2i is also [weaker][ Argon2 Weaker ] than both Argon2id and Argon2d when it comes to resistance against GPU/ASIC cracking. Therefore, as per the [RFC][ RFC9106 ], Argon2id should be used if you do not know the difference between the types or you consider side-channel attacks to be a viable threat because Argon2id offers the benefits of both Argon2i (side-channel resistance, albeit to a lesser extent) and Argon2d (GPU/ASIC resistance).

- Chaining password hashing functions (e.g. `scrypt(PBKDF2(password))`): **this just reduces the strength of the stronger algorithm** since it means having worse parameters to get the same total delay.


---

#### Notes

1. **Never hard-code passwords into source code**: these can be easily retrieved.

2. **Always use a random 128-bit or 256-bit salt**: salts ensure that each password hash is different, which prevents an attacker from identifying two identical passwords without cracking the hashes. Moreover, salting defends against [attacks][ Password Cracking ] that rely on [precomputed hashes][ Rainbow Tables ]. The typical salt size is 128-bits, but 256-bit is also fine for further reassurance that the salt won’t repeat. Anything above that is excessive, and short salts can lead to salt reuse and allow for precomputed attacks, which defeats the point of salting.

3. **Always use the highest parameters/delay you can afford**: ideally, use a delay of 250+ milliseconds. In many cases, that’s too small. For instance, PBKDF2 requires a high number of iterations because it’s not resistant to GPU/ASIC attacks, and if you’re performing a non-interactive operation (e.g. disk encryption), then you can afford longer delays like 3-5 seconds.

4. **Avoid** string password variables: strings are immutable (unchangeable) in many programming languages (e.g. C#, Java, JavaScript, Go, etc), meaning they can’t be zeroed out from memory. Instead, use a char array if possible and convert that into a byte array for password hashing/password-based key derivation. Then erase both arrays from memory after you’ve finished using them. Note that this is also difficult in many programming languages, as explained in point 7 of the [Symmetric Encryption](#symmetric-encryption) Notes section, but *attempting* to erase sensitive data from memory is better than doing nothing.

5. Compare passwords in **constant time**: if you ever need to compare passwords (e.g. for password re-entry in a console application), then you should use a constant time comparison function to prevent [timing attacks][ Timing Attacks ]. Sometimes these functions require both arrays to be equal in length, in which case you can hash both passwords using a regular hash function (e.g. BLAKE2b-512) for the comparison; just erase these values from memory afterwards and don’t use them for anything else.

6. Use a 256-bit and above output length: for password storage, a 128-bit hash is normally fine, but a 256-bit output provides a better security level for high entropy passwords. For key derivation, you should derive at least a 256-bit output and perhaps more, depending on whether you need to derive multiple keys (e.g. a 256-bit encryption key and a 512-bit MAC key).

7. **Always** store the parameters (e.g. memory size, iterations, and parallelism for Argon2) with the password hash: these values don't need to be secret and are required to derive the correct hash. When storing passwords in a database, you should store these values for each user in order to verify the hashes and transition to stronger parameters over time as hardware improves. In [some][ Done For You ] cryptographic libraries, this is done for you. By contrast, in a key derivation scenario, you can get away with using fixed parameters based on a version number stored as a header (e.g. file format v3 = 256 MiB of RAM and 12 iterations). Then if you want to change the parameters, you just increment the version number.

8. **Perform client-side password prehashing** for [server relief][ Server Relief ] or to [hide the plaintext password from the server][ Encryption Usage ]: when creating an account, the server can send a **random** salt to the client that’s used to perform password hashing on the client’s device. The server then performs server-side password hashing on the transmitted password hash using the same salt. Then the salt and final password hash are stored in the password database. When logging in, the server sends the stored salt to the client, the client performs client-side password hashing, the client transmits the password hash to the server, the server performs server-side password hashing using the stored salt, and then the server compares the result with the password hash stored in the database. In the event of a non-existent user, the salt that’s sent should always be the same for a given username, which involves using a MAC (e.g. keyed BLAKE2b-512), with the username as the message.

9. **Don’t** use padding to hide the length of a password when sending it to a server: instead, perform client-side password hashing if possible (please see point 8 above). If that’s not possible, then you should hash the password using a regular hash function, with the largest possible output length (e.g. BLAKE2b-512), on the client’s device, transmit the hash to the server, and perform server-side password hashing, using the transmitted hash as the password. Both techniques ensure that the amount of data transmitted is constant and prevent the server [effortlessly][ Keeping Passwords ] obtaining a copy of the password, but client-side password prehashing should be preferred as it allows for more secure password hashing parameters and provides additional security compared to if the server leaks/stores the client-side regular/fast hash of the password.

10. **Use** [rate limiting][ Rate Limiting ] to prevent denial of service (DOS) and bruteforce attacks: this involves blacklisting certain IP addresses and usernames from trying to log in temporarily to prevent the server being overwhelmed and to prevent attackers from bruteforcing passwords.

11. If a user can supply very long passwords, then this *can* lead to denial of service attacks: this happened to [Django][ Long Passwords ] in 2013. To fix this, either enforce a password length limit (e.g. 128 characters is the max) or prehash passwords using a regular/fast hashing algorithm, with the highest possible output length (e.g. BLAKE2b-512), before performing password hashing.

12. [Hash-then-Encrypt][ Hash Then Encrypt ] for additional security when storing passwords: you can use a **password hashing algorithm** on the password before encrypting the salt and password hash using an AEAD or Encrypt-then-MAC, with a secret key **stored separately from the password database**. This forces an attacker to decrypt the password hashes before trying to crack them. Furthermore, it means that if your secret key is ever compromised but the password hashes are not, then you can decrypt all the stored password hashes and re-encrypt them using a new key, which is easier than resetting every user’s password in the event of a pepper being compromised.

13. Use a [pepper][ Pepper ] for additional security when deriving keys: a pepper is essentially a secret key that’s mixed with the password using a MAC (e.g. `HMAC-SHA512(message: password, key: pepper)`) before password hashing. In the case of password storage, using Hash-then-Encrypt makes more sense for the reason I explained above. By contrast, for key derivation, using a pepper is a great idea if possible because it means an additional secret is required, making a bruteforce more difficult. For instance, a keyfile in [file][ Key File ]/[disk][ Disk ] encryption software acts as a pepper, which improves the security of the key derivation assuming that the keyfile is stored correctly (e.g. on an encrypted memory stick away from the encrypted file/disk).
