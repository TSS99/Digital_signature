# 🔐 Digital Signatures Step by Step

**From Bitstrings to RSA Signing and Verification — A Mathematical and Practical Walkthrough in Python**

---

## 📖 What Is This Project?

This repository contains a single, self-contained Jupyter Notebook that teaches you **how digital signatures work** from the absolute ground up — both the **mathematics** and the **Python implementation**.

If you've ever wondered:

- *"What actually happens when I digitally sign a document?"*
- *"What do public keys and private keys really do, mathematically?"*
- *"Why can't someone just fake my signature?"*

…then this notebook answers every question with equations, code, and printed output.

---

## 🎯 What You Will Learn

| # | Topic |
|---|-------|
| 1 | What problem digital signatures solve (authenticity, integrity, non-repudiation) |
| 2 | How text becomes bytes and bitstrings inside a computer |
| 3 | What hashing is (SHA-256) and why we hash before signing |
| 4 | The intuition behind public keys and private keys |
| 5 | The full mathematics of RSA key generation |
| 6 | How to convert a hash digest into an integer for RSA math |
| 7 | How signing and verification work as modular exponentiation |
| 8 | Why verification catches tampering — proven with numbers |
| 9 | Why textbook RSA is insecure and must never be used in production |
| 10 | How to generate real 2048-bit RSA keys in Python |
| 11 | How to sign messages securely with RSA-PSS and SHA-256 |
| 12 | How to verify signatures and detect tampering in real code |
| 13 | How the toy math maps to the real-world implementation |
| 14 | A complete end-to-end sender → attacker → receiver demo |
| 15 | Common misconceptions about digital signatures |

---

## 🔢 The Mathematics Explained

### Step 1 — Message to Bytes to Hash

A human-readable message like `"Approve payment of ₹500"` is first encoded into raw **bytes** (UTF-8), then passed through a cryptographic hash function:

$$
h = \text{SHA-256}(m)
$$

This produces a **fixed-length 256-bit fingerprint** regardless of message size. Key properties:

- **Deterministic**: Same input always gives the same hash.
- **Avalanche Effect**: Changing even one bit in $m$ flips ~50% of the bits in $h$.
- **One-way**: Given $h$, it is computationally infeasible to recover $m$.

> A hash is **not** compression. You cannot "decompress" it to get the original message back.

---

### Step 2 — RSA Key Generation

We generate a mathematically linked key pair:

1. Choose two prime numbers $p$ and $q$.

2. Compute the modulus:

$$
n = p \times q
$$

3. Compute Euler's Totient:

$$
\phi(n) = (p - 1)(q - 1)
$$

4. Choose a public exponent $e$ such that:

$$
\gcd(e,\ \phi(n)) = 1
$$

5. Compute the private exponent $d$ as the modular multiplicative inverse:

$$
e \times d \equiv 1 \pmod{\phi(n)}
$$

This gives us:

| Key | Components | Who holds it |
|-----|-----------|--------------|
| **Public Key** | $(n, e)$ | Shared with everyone |
| **Private Key** | $d$ (with $n$) | Kept secret by the signer |

**Toy example used in the notebook:**

| Symbol | Value |
|--------|-------|
| $p$ | $61$ |
| $q$ | $53$ |
| $n = p \times q$ | $3233$ |
| $\phi(n) = (p-1)(q-1)$ | $3120$ |
| $e$ | $17$ |
| $d = e^{-1} \bmod \phi(n)$ | $2753$ |

You can verify: $17 \times 2753 = 46801 = 15 \times 3120 + 1$, so $e \times d \equiv 1 \pmod{3120}$ ✓.

---

### Step 3 — Signing (with Private Key)

The hash digest bytes are converted into an integer $h$. Signing is a single modular exponentiation:

$$
\boxed{s = h^{\,d} \bmod n}
$$

This is what *"signing with the private key"* literally means — raising the hash integer to the power of the secret exponent $d$, modulo $n$.

Only someone who knows $d$ can compute $s$.

---

### Step 4 — Verification (with Public Key)

The verifier takes the signature $s$ and raises it to the public exponent $e$:

$$
\boxed{v = s^{\,e} \bmod n}
$$

Then checks:

$$
v \stackrel{?}{=} h
$$

If equal, the signature is **valid**.

---

### Why Does This Work?

By Euler's theorem, for any integer $h$ coprime to $n$:

$$
h^{\,\phi(n)} \equiv 1 \pmod{n}
$$

Since $e \times d \equiv 1 \pmod{\phi(n)}$, we can write $e \times d = 1 + k \cdot \phi(n)$ for some integer $k$. Therefore:

$$
s^{\,e} = \left(h^{\,d}\right)^e = h^{\,d \cdot e} = h^{\,1 + k\,\phi(n)} = h \cdot \left(h^{\,\phi(n)}\right)^k \equiv h \cdot 1^k = h \pmod{n}
$$

This is why the public key **recovers** the original hash from the signature — pure number theory!

---

### Step 5 — Why Tampering Is Detected

If an attacker modifies the message $m$ to $m'$, the receiver computes a new hash:

$$
h' = \text{SHA-256}(m')
$$

But the old signature $s$ was computed from $h$, not $h'$. The verifier gets:

$$
v = s^{\,e} \bmod n = h \neq h'
$$

Since $h \neq h'$ (due to the avalanche effect of SHA-256), verification **fails**. The tampering is caught.

---

### Step 6 — From Textbook RSA to Secure RSA-PSS

Textbook RSA ($s = h^d \bmod n$) is **insecure** in practice because:

- It is **deterministic**: same message → same signature, enabling pattern analysis.
- It is **malleable**: $\text{sig}(m_1) \cdot \text{sig}(m_2) = \text{sig}(m_1 \cdot m_2)$, allowing signature forgery.

**RSA-PSS** (Probabilistic Signature Scheme) fixes this by:

1. Adding a random **salt** $r$ before signing.
2. Applying a strict **encoding** format (MGF1 mask generation).
3. Producing different signatures for the same message on each signing.

The real signing flow becomes:

$$
m \xrightarrow{\text{SHA-256}} h \xrightarrow{\text{PSS-Encode}(h,\, r)} \text{EM} \xrightarrow{s\, =\, \text{EM}^d \bmod n} s
$$

This is exactly what the `cryptography` library does internally when you call `private_key.sign()`.

---

## 🗂️ Repository Structure

```
Digital signature/
├── Digital_Signatures_Step_by_Step.ipynb   # The main tutorial notebook
├── README.md                               # You are here
├── .gitignore                              # Git ignore rules
└── venv/                                   # Python virtual environment (not tracked)
```

---

## 🚀 Getting Started

### Prerequisites

- **Python 3.8 or later** installed on your machine.
- A way to open Jupyter Notebooks (Jupyter Lab, Jupyter Notebook, or VS Code with the Jupyter extension).

### Setup Instructions

1. **Clone this repository**:

   ```bash
   git clone https://github.com/TSS99/Digital_signature.git
   cd Digital_signature
   ```

2. **Create a virtual environment**:

   ```bash
   python -m venv venv
   ```

3. **Activate the virtual environment**:

   - **Windows (PowerShell)**:
     ```powershell
     .\venv\Scripts\Activate
     ```
   - **Windows (CMD)**:
     ```cmd
     venv\Scripts\activate.bat
     ```
   - **macOS / Linux**:
     ```bash
     source venv/bin/activate
     ```

4. **Install the required libraries**:

   ```bash
   pip install cryptography jupyter
   ```

5. **Launch the notebook**:

   ```bash
   jupyter notebook Digital_Signatures_Step_by_Step.ipynb
   ```

6. **Run every cell from top to bottom** — the notebook is designed to be executed sequentially.

---

## 📦 Dependencies

| Library | Purpose |
|---------|---------|
| `hashlib` (built-in) | Computing $h = \text{SHA-256}(m)$ |
| `math` (built-in) | $\gcd$ and modular arithmetic for toy RSA |
| [`cryptography`](https://cryptography.io/) | Real 2048-bit RSA key generation, PSS signing, and verification |
| `jupyter` | Running the interactive notebook |

---

## 🧭 Notebook Structure — Two Layers

| Layer | Sections | What It Does |
|-------|----------|--------------|
| **Toy Mathematical RSA** | 5 – 8 | Uses small primes ($p = 61$, $q = 53$, $n = 3233$). You trace every number: $h \to s = h^d \bmod n \to v = s^e \bmod n \to v = h$ ✓ |
| **Real-World RSA-PSS** | 10 – 14 | Uses `cryptography` with 2048-bit keys, PSS padding, and SHA-256 — production-grade security |

**Section 13** explicitly bridges the two layers, mapping the toy formula to what the library does internally.

---

## 🧪 Practice Exercises

1. Change the message and observe that verification fails.
2. Generate a second key pair and try verifying with the wrong public key.
3. Corrupt the signature (`secure_signature[:-5]`) and see the exception.
4. Sign a text file instead of a short string.

---

## ❓ FAQ

### Is a digital signature the same as encryption?
**No.** Encryption: $c = m^e \bmod n$ (hides message). Signature: $s = h^d \bmod n$ (proves origin). The message remains public.

### Can the public key create a valid signature?
**No.** That would require computing $d$ from $(n, e)$, which means factoring $n = p \times q$. For 2048-bit $n$, this is computationally infeasible.

### Why hash before signing?
RSA operates on integers $< n$. A large document exceeds $n$. SHA-256 reduces any input to a fixed 256-bit integer. Hashing also prevents mathematical attacks on raw RSA.

### What makes RSA-PSS better than textbook RSA?
PSS adds a random salt $r$, so $\text{Sign}(m)$ produces a different $s$ each time. This eliminates determinism and blocks forgery via signature multiplication.

---

## 📚 Further Reading

- [RSA Cryptosystem — Wikipedia](https://en.wikipedia.org/wiki/RSA_(cryptosystem))
- [Digital Signature — Wikipedia](https://en.wikipedia.org/wiki/Digital_signature)
- [Python `cryptography` Library Docs](https://cryptography.io/en/latest/)
- [RSA-PSS Standard — RFC 8017](https://datatracker.ietf.org/doc/html/rfc8017)
- [Euler's Theorem — Wikipedia](https://en.wikipedia.org/wiki/Euler%27s_theorem)

---

## 📝 License

This project is intended for **educational purposes**. Feel free to use, modify, and share.

---

## Quick Reference Card

```
Message:           m = "Approve payment of ₹500"
Hash:              h = SHA-256(m)                    → 256-bit fingerprint
Key Generation:    p, q primes → n = pq, ϕ(n) = (p−1)(q−1), ed ≡ 1 (mod ϕ(n))
Signing:           s = h^d mod n                     → only private key holder can compute
Verification:      v = s^e mod n  →  check v = h     → anyone with public key can verify
Tamper Detection:  m' ≠ m  →  h' ≠ h  →  v ≠ h'     → signature REJECTED ✗
```
