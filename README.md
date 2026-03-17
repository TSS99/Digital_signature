# 🔐 Digital Signatures Step by Step

**From Bitstrings to RSA Signing and Verification — A Mathematical and Practical Walkthrough in Python**

---

## 📖 What Is This Project?

This repository contains a single, self-contained Jupyter Notebook that teaches you **how digital signatures work** from the absolute ground up.

If you've ever wondered:

- *"What actually happens when I digitally sign a document?"*
- *"What do public keys and private keys really do?"*
- *"Why can't someone just fake my signature?"*

…then this notebook is for you.

No prior knowledge of cryptography is needed. If you know basic Python and school-level math, you're ready to start.

---

## 🎯 What You Will Learn

By working through the notebook, you will understand:

| # | Topic |
|---|-------|
| 1 | What problem digital signatures solve (authenticity, integrity, non-repudiation) |
| 2 | How text becomes bytes and bitstrings inside a computer |
| 3 | What hashing is (SHA-256) and why we hash before signing |
| 4 | The intuition behind public keys and private keys |
| 5 | The mathematics of RSA — step by step with small numbers |
| 6 | How to convert a hash into an integer for RSA math |
| 7 | How **signing** ($s = h^d \bmod n$) and **verification** ($v = s^e \bmod n$) actually work |
| 8 | Why verification catches tampering |
| 9 | Why textbook RSA is insecure and must never be used in production |
| 10 | How to generate real 2048-bit RSA keys using Python's `cryptography` library |
| 11 | How to sign messages securely with **RSA-PSS** and SHA-256 |
| 12 | How to verify signatures and detect tampering in real code |
| 13 | How the toy math maps to the real-world implementation |
| 14 | A complete end-to-end sender → attacker → receiver demonstration |
| 15 | Common misconceptions about digital signatures |

---

## 🗂️ Repository Structure

```
Digital signature/
├── Digital_Signatures_Step_by_Step.ipynb   # The main tutorial notebook
├── README.md                               # You are here
├── .gitignore                              # Git ignore rules
└── venv/                                   # Python virtual environment (not tracked by Git)
```

---

## 🚀 Getting Started

### Prerequisites

- **Python 3.8 or later** installed on your machine.
- A way to open Jupyter Notebooks (Jupyter Lab, Jupyter Notebook, or VS Code with the Jupyter extension).

### Step-by-Step Setup

1. **Clone this repository** (or download the ZIP):

   ```bash
   git clone https://github.com/<your-username>/Digital_Signature.git
   cd "Digital signature"
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
   - **Windows (Command Prompt)**:
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
| `hashlib` (built-in) | Computing SHA-256 hashes |
| `math` (built-in) | GCD and modular arithmetic for toy RSA |
| [`cryptography`](https://cryptography.io/) | Real-world RSA key generation, PSS signing, and verification |
| `jupyter` | Running the interactive notebook |

> **Note:** The notebook also includes a `%pip install cryptography` cell, so even if you forget to install it beforehand, the notebook will handle it for you.

---

## 🧭 How the Notebook Is Structured

The notebook is divided into **17 clearly labeled sections**. Each section follows a consistent pattern:

1. **A Markdown cell** explains *what* you're about to do, *why* it matters, and the math behind it.
2. **A Code cell** implements the idea with clear variable names, comments, and printed output.

### Two Layers of Understanding

| Layer | Description |
|-------|-------------|
| **Toy Mathematical RSA** (Sections 5–8) | Uses tiny primes ($p = 61$, $q = 53$) so you can trace every number by hand. This builds your mathematical intuition. |
| **Real-World RSA-PSS** (Sections 10–14) | Uses Python's `cryptography` library with 2048-bit keys, PSS padding, and SHA-256 — the same standard used in production systems. |

Section 13 explicitly **bridges the gap** between the toy math and the real implementation, so you know exactly which parts are educational abstraction and which parts are production reality.

---

## 🧪 Practice Exercises (from the Notebook)

After completing the tutorial, try these exercises to solidify your understanding:

1. **Change the message** and observe that the old signature no longer verifies.
2. **Generate a second RSA key pair** and try verifying an existing signature with the wrong public key.
3. **Corrupt the signature** (e.g., `secure_signature[:-5]`) and see what happens.
4. **Sign a text file** instead of a short string by reading it with `open('sample.txt', 'rb')`.

---

## ❓ Frequently Asked Questions

### Is a digital signature the same as encryption?
**No!** Encryption hides the message contents. A digital signature proves who sent the message and that it wasn't tampered with. The message itself can remain completely public.

### Can I use this code in production?
The **toy RSA math** (Sections 5–8) is for learning only — never use it in real systems. The **`cryptography` library code** (Sections 10–14) follows industry-standard practices (RSA-PSS + SHA-256) and is safe for production use.

### Why do we hash before signing?
RSA can only process numbers smaller than $n$. A full document could be millions of bytes. Hashing compresses it to a fixed 256-bit fingerprint. Additionally, hashing provides security benefits that prevent certain mathematical attacks on raw RSA.

### What is PSS padding?
PSS (Probabilistic Signature Scheme) adds random "salt" to the signing process. This means signing the same message twice produces two completely different signatures, which blocks attackers from building forgery patterns.

---

## 📚 Further Reading

- [RSA (Wikipedia)](https://en.wikipedia.org/wiki/RSA_(cryptosystem))
- [Digital Signature (Wikipedia)](https://en.wikipedia.org/wiki/Digital_signature)
- [Python `cryptography` Library Documentation](https://cryptography.io/en/latest/)
- [PKCS#1 RSA-PSS Standard (RFC 8017)](https://datatracker.ietf.org/doc/html/rfc8017)

---

## 📝 License

This project is intended for **educational purposes**. Feel free to use, modify, and share it.

---

> *"A digital signature is a mathematical scheme for verifying the authenticity and integrity of a message. It is the cryptographic equivalent of a handwritten signature — but far harder to forge."*
