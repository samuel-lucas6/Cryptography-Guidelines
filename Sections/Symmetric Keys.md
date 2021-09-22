
## Symmetric Key Size


#### Use (not in order because they have different use cases):

1. 256-bit keys: there’s essentially no reason not to use 256-bit keys for symmetric encryption. This is the only available key size for most (X)ChaCha20 and (X)Salsa20 implementations, it’s the key size that’s used for [top secret material](https://www.keylength.com/en/6/) by intelligence agencies and governments, and it’s [now recommended](https://www.keylength.com/en/3/) for long-term storage due to concerns surrounding quantum computers being able to bruteforce 128-bit keys.

2. 512-bit keys: if you’re using a MAC like HMAC-SHA512 or keyed BLAKE2b-512, then you should use a 512-bit key. This helps with domain separation when deriving keys, and it’s recommended to always use a key size as large as the output length for HMAC (e.g. a 256-bit key for HMAC-SHA256). This ensures that the key size doesn't decrease the security provided by the MAC.


---

#### Avoid (in order):

1. Smaller than 128-bit keys: this won’t stand the test of time and in some cases can already be bruteforced.

2. Symmetric encryption algorithms with large key sizes (e.g. [Threefish](https://en.wikipedia.org/wiki/Threefish)): anything over 256-bit is widely regarded as unnecessary. Furthermore, encryption algorithms supporting such key sizes are very unpopular in practice. Note that the situation is different for MACs, as explained in point 2 of the Use section above.

3. 128-bit keys: **this is the minimum**, but please just use 256-bit keys because they provide a higher security margin for an insignificant cost. The [argument](https://blog.1password.com/why-we-moved-to-256-bit-aes-keys/) that AES-128 is more secure than AES-256 due to certain attacks being more effective on AES-256 is incorrect because such attacks are **not** practical in the real world. You should ideally use ChaCha20 instead of AES anyway since it has a higher security margin and runs in constant time, avoiding timing attacks, as explained in the [Symmetric Encryption](#symmetric-encryption) Use section.
