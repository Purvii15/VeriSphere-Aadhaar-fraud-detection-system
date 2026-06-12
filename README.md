# VeriSphere — AI-Powered Document Fraud Detection System

An intelligent, multi-layer fraud detection platform that verifies identity documents, detects tampering, and flags suspicious banking applications in real time — powered by computer vision, cryptographic validation, and Google Gemini AI.

![Python](https://img.shields.io/badge/Python-3.11-blue)
![Flask](https://img.shields.io/badge/Flask-3.x-lightgrey)
![YOLOv8](https://img.shields.io/badge/YOLOv8-Ultralytics-purple)
![Gemini](https://img.shields.io/badge/Gemini-AI-orange)
![YOLO26](https://img.shields.io/badge/YOLO26-SISA-green)

---

## What It Does

VeriSphere runs uploaded documents through five independent verification layers simultaneously — catching forgeries that pass visual inspection:

1. **YOLOv8 Field Detection** — locates and crops every field on the card (name, DOB, ID number, face, QR code)
2. **EasyOCR Extraction** — reads all text with multi-variant preprocessing for low-quality scans
3. **Verhoeff Checksum** — mathematically validates the 12-digit Aadhaar number (catches 100% of fabricated numbers)
4. **QR Cross-Verification** — decodes the embedded QR (old XML + secure binary formats) and compares every field against OCR output
5. **Digital Forensics** — ELA, noise uniformity, and sharpness analysis detect pixel-level tampering, photo swaps, and text edits

All five signals combine into a weighted fraud score: **Genuine / Suspicious / Fake**

---

## Features

- **Multi-Document Support** — Aadhaar, PAN, Driving Licence, Passport, Voter ID detected via SISA YOLO26 classifier (94.8% mAP50)
- **Live QR Decoder** — paste raw QR string from any handheld scanner to decode all fields and embedded photo instantly
- **Gemini AI Chat** — floating ✨ chat powered by Google Gemini 2.0 Flash with automatic Llama 3.2 fallback (never breaks)
- **Animated Processing UI** — radar rings, stage progress indicators, particle canvas during analysis
- **Auto-analysis** — starts the moment a file is dropped, no button press needed
- **Passwordless Architecture** — document verification as the authentication layer, no passwords stored

---

## Tech Stack

| Component | Technology |
|---|---|
| Document Type Detection | YOLO26 (SISA dataset, 34 classes) |
| Field Detection | YOLOv8 (Ultralytics) |
| OCR | EasyOCR |
| QR Decoding | zxing-cpp / pyzbar |
| Face Comparison | SSIM (scikit-image) + Haar cascade |
| Checksum Validation | Verhoeff Algorithm |
| Forensics | OpenCV (ELA, noise, sharpness) |
| AI Assistant | Google Gemini 2.0 Flash + Llama 3.2 fallback |
| Web Server | Flask + Gunicorn |
| Frontend | Tailwind CSS (dark theme) |

---

## Project Structure

```
verisphere/
├── aadhaar_pipeline/
│   ├── pipeline.py        # Main orchestration — runs all 5 stages
│   ├── detector.py        # YOLOv8 inference and crop extraction
│   ├── ocr.py             # EasyOCR with multi-variant preprocessing
│   ├── qr_validation.py   # QR decode, field comparison, photo extraction
│   ├── photo_compare.py   # SSIM face matching with Haar alignment
│   ├── tampering.py       # ELA, noise, sharpness forensics
│   ├── validator.py       # Verhoeff checksum
│   ├── consistency.py     # Cross-field consistency checks
│   └── decision.py        # Fraud scoring and verdict
├── flask_app.py           # Flask server, all routes, HTML templates
├── requirements.txt       # Python dependencies
├── aadhaar_best.pt        # YOLOv8 model weights (not in git — large file)
└── resnet_aadhaar.pth     # ResNet tampering model (not in git — large file)
```

---

## Getting Started

```bash
# 1. Clone the repo
git clone https://github.com/Purvii15/VeriSphere-Aadhaar-fraud-detection-system.git
cd VeriSphere-Aadhaar-fraud-detection-system

# 2. Create and activate a virtual environment
python -m venv .venv
.venv\Scripts\activate        # Windows
source .venv/bin/activate     # macOS / Linux

# 3. Install dependencies
pip install -r requirements.txt

# 4. Add your Gemini API key (optional — app works without it)
echo GEMINI_API_KEY=your_key_here > .env
# Get a free key at: https://aistudio.google.com/app/apikey

# 5. Run
python flask_app.py
```

Open `http://localhost:5000` in your browser.

> **Note:** Model weights (`aadhaar_best.pt`, `resnet_aadhaar.pth`) are not in the repo due to file size. Contact the team to get them.

---

## Fraud Score

| Signal | Penalty |
|---|---|
| Verhoeff checksum fail | +0.30 |
| Aadhaar number mismatch (QR vs OCR) | +0.25 |
| Name mismatch (QR vs OCR) | +0.20 |
| DOB mismatch (QR vs OCR) | +0.20 |
| Face photo doesn't match | +0.50 |
| Tampering detected | up to +0.30 |

**Verdicts:** `< 0.15` → Genuine · `0.15–0.40` → Suspicious · `> 0.40` → Fake

---

## AI Assistant

The floating **✨ button** (bottom-right) uses Google Gemini 2.0 Flash to explain every result in plain language. Automatically falls back to Llama 3.2 via Hugging Face if Gemini quota runs out — the chat is always available.
