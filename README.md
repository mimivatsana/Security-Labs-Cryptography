# Cryptographic Integrity Verification & Hash Analysis

**Author:** Mimi Vatsana | Cybersecurity Analyst  
**Environment:** Kali Linux (`kali@mimi-vatsana`) on UTM (Apple Silicon ARM64)  
**Standards Aligned:** CompTIA Security+ SY0-701 Domain 1.0 | NIST SP 800-131A  

---

## 1. Executive Summary
This project analyzes cryptographic integrity controls and evaluates the vulnerability of one-way hash digests against offline credential recovery. SHA-256 was implemented to demonstrate fixed-length output and the avalanche effect, while John the Ripper was used to assess the exposure of legacy, unsalted MD5 digests against dictionary attacks.

---

## 2. Environment Architecture & Identity
- **Operating System:** Kali Linux (Rolling 2026.2 ARM64)
- **Virtualization Layer:** UTM / QEMU
- **Host / User Identity:** `kali@mimi-vatsana`
- **Core Utilities:** `sha256sum`, `john`, `coreutils`

---

## 3. Technical Execution & Artifacts

### Phase 1: Cryptographic Integrity & Avalanche Effect
A baseline data file was created and hashed using SHA-256. A minor modification was introduced to evaluate the avalanche effect—verifying that any change to the input entirely scrambles the resulting digest.


```bash
# Baseline Hash Generation
echo "SecurityPlus Integrity Test" > sample.txt
sha256sum sample.txt

# Modification Verification (Avalanche Effect)
echo "SecurityPlus Integrity Test." > sample.txt
sha256sum sample.txt
 ```

<img width="2542" height="1664" alt="Image 10-1-26 at 2 44 PM" src="https://github.com/user-attachments/assets/bf91763e-c188-4137-924b-2eab0ba6d460" />


### Phase 2: Offline Credential Recovery (MD5 Analysis)
A simulated MD5 hash digest (5f4dcc3b5aa765d61d8327deb882cf99) was isolated into a target file. John the Ripper was executed with a standard wordlist to simulate an offline dictionary attack.

```bash
# Target Identification and Execution

echo "5f4dcc3b5aa765d61d8327deb882cf99" > target_hash.txt
john --format=raw-md5 target_hash.txt

```


*Results:*
- **Target Hash:** `5f4dcc3b5aa765d61d8327deb882cf99`
- **Recovered Plaintext:** `password`
- **Time to Recover:** < 1 second



## 4. Observations & Security Analysis

- **Integrity Enforcement (SHA-256):** Generating a hash for sample.txt produced a fixed 64-character hexadecimal digest. Modifying the file by a single character completely transformed the digest output (the avalanche effect). This confirms that cryptographic hashing functions serve as reliable integrity controls to detect tampering or transmission corruption, rather than providing data confidentiality.

- **Algorithm Obsolescence (MD5):** Executing John the Ripper recovered the plaintext (password) almost instantly. MD5 lacks computational work factors and salting, making it vulnerable to precomputation (rainbow tables) and offline dictionary attacks.

- **Remediation:** Production authentication systems must deprecate legacy digests and implement adaptive, salted key-derivation functions such as bcrypt, PBKDF2, or Argon2id to resist offline brute-forcing. SHA-256 or SHA-3 should remain standard for data integrity verification and binary baseline audits.





