# Secure Document Authentication Framework

A cryptographic framework for **authenticating scanned document images**, combining digital signatures, hybrid encryption, integrity hashing, and an invisible watermark into a single sign-and-verify pipeline. The notebook implements the scheme end-to-end and runs a full experimental evaluation on a document-image dataset.

> **Naming note:** The notebook file is called `ECDSA`, but the implementation actually uses **Ed25519** signatures and **X25519** key exchange. ECDSA (P-256) appears only as one baseline in the comparison table.

> **Note on scope:** This is a research / experimental evaluation framework, not an audited or production-ready security system. Do not use it to protect real documents without an independent security review.

## What it does

The pipeline signs a document image and produces an encrypted, verifiable package:

1. **Key generation** — generates `N` signatory Ed25519 keypairs and aggregates them into one master key via byte-wise XOR; also sets up an X25519 recipient keypair.
2. **Watermarking** — embeds a blind, DWT-domain (Haar) watermark into the image, detectable by correlation.
3. **Hashing & signing** — hashes the watermarked image with SHA-512 and signs the digest with the aggregated Ed25519 master key.
4. **Encryption** — wraps the package with AES-128-GCM, with the AES key encapsulated via X25519 + HKDF (hybrid encryption).
5. **Verification** — decrypts, checks hash integrity, signature validity, watermark presence, and a replay-window timestamp.

## Evaluation

The notebook runs a journal-style experimental suite:

- **Security tests** — original, tampered-ciphertext, and replay-attack cases
- **Watermark robustness** — detection and PSNR/SSIM under JPEG compression, Gaussian noise, cropping, scaling, and rotation
- **Benchmark** — signing/verification throughput over the full dataset
- **Scalability** — timing across 10–2000 signatories
- **Fair comparison** — against RSA-2048, ECDSA-P256, and Ed25519 baselines
- **Statistical analysis** — means, variances, and 95% confidence intervals
- **Computational complexity** — per-operation complexity table

## Dataset

Benchmarking uses the **Tobacco3482** document-image dataset (~3,482 labelled document scans), downloaded automatically from Kaggle via `kagglehub`:

- `patrickaudriaz/tobacco3482jpg`

The dataset is **not** included in this repository; you'll need your own Kaggle credentials configured.

## Requirements

Developed in Google Colab. Main dependencies:

- `cryptography`
- `opencv-python`
- `pywavelets`
- `scikit-image`
- `numpy`
- `scipy`
- `pandas`
- `matplotlib`
- `psutil`
- `tqdm`
- `kagglehub`

Install everything with:

```bash
pip install -r requirements.txt
```

## Getting started

1. Open the notebook in Google Colab or Jupyter.
2. Ensure Kaggle credentials are configured so `kagglehub` can download Tobacco3482.
3. Run the cells top to bottom; the final cell, `run_all_experiments()`, executes the complete evaluation and prints the summary.

## Repository structure

```
.
├── ECDSA_Code.ipynb
└── README.md
```
