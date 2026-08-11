# LexVerify 
**Offline-first, multilingual document validation pipeline with local LLM semantic verification**

**1. Problem Statement**

First-time buyers of residential land face fragmented and opaque documentation processes.  
Challenges include:
- Frequent changes in mutation records, encumbrance certificates, and zoning classifications.
- Difficulty verifying authenticity across multiple government portals.
- Risk of fraud due to duplicate survey numbers or hidden encumbrances.
- Limited internet access in small towns, making online verification unreliable.

**2. Impact** 

Buyers struggle to confidently establish ownership, track changes, and ensure compliance with government regulations, leading to disputes or financial loss.

**3. LexVerify Overview** 

LexVerify validates Indian property **Sale Deeds** against the essential legal elements a valid deed must contain. It accepts `.docx`, `.pdf`, `.png`, and `.jpg`, extracts text (structured parse or on-device OCR), checks every mandatory
clause, and produces three outputs: a console summary, a structured JSON report, and an HTML report.

The system currently reads **English and 20 Indian Languages**, detecting the language automatically and applying the right vocabulary without user configuration.

Two constraints shape the entire architecture:-

**Nothing leaves the machine.** No cloud service, no API key, no telemetry. OCR runs locally through Tesseract; semantic verification runs locally through Ollama. The tool works on an air-gapped laptop which matters for documents carrying PAN
numbers, Aadhaar numbers, and residential addresses.

**Deterministic parsing is authoritative; the language model assists but does not arbitrate.** This principle was learned from a concrete failure. It is the single most important design decision in the project.

**4. Tech Stack**

**4.1 Python packages**

🔎 Document & OCR Stack
- **python-docx** → `.docx` parsing (paragraphs and tables)
- **PyMuPDF** → PDF text-layer detection (scanned vs native)
- **pdf2image** → PDF page rasterization (requires Poppler)
- **pytesseract** → Tesseract binding for OCR
- **Pillow** → Image preprocessing utilities

🌐 Web Application Layer
- **fastapi** → Web UI framework
- **uvicorn** → ASGI server for FastAPI
- **jinja2** → HTML templating for the web UI
- **python-multipart** → File upload handling

🤖 AI Integration
- **requests** → HTTP calls to Ollama API

**4.2 External Software**

These are not pip-installable and must be installed separately.

| Software          | Purpose                          | Notes                                                                 |
|-------------------|----------------------------------|----------------------------------------------------------------------|
| **Tesseract OCR** | Text extraction from scanned pages | Windows: use the UB Mannheim build. Add to PATH, or set `pytesseract.pytesseract.tesseract_cmd` explicitly. |
| **Tamil trained data** | Tamil OCR                     | Required only for SCANNED Tamil documents. Place `tam.traineddata` in Tesseract’s `tessdata` folder. |
| **Poppler**       | PDF to image conversion           | Windows: download binaries, add the `bin` folder to PATH.             |
| **Ollama**        | Local LLM runtime                 | Listens on `127.0.0.1:11434`. Often auto-starts; a "port already in use" error means it is already running. |
| **llama3.1:8b**   | The model itself                  | Pull once with: `ollama pull llama3.1:8b`                            |


**5. Implementation Status** 

| Component | Status |
|---|---|
| Ingestion (loader, preprocessing, OCR, confidence) | ✅ Built |
| Clause validation (12 clauses, sub-items, tiering) | ✅ Built |
| Computed checks (amount, boundaries, stamp duty, cheque) | ✅ Built |
| English support | ✅ Built |
| Tamil support (packs, numbers, directions, warnings) | ✅ Built |
| HTML report + complex-script handling | ✅ Built |
| Orchestration, JSON report, console output | ✅ Built |
| CLI + FastAPI web UI | ✅ Built |
| Extraction / Title & Zoning as separate agents | 🔀 Merged into `validation_agent.py` |
| Online-Verification agent | 🟡 Designed |
| Reporting agent | 🟡 Designed |
| Evaluation harness | 🚧 In-progress `golden_dataset.json |
| Cross-document verification (Deed vs EC vs RTC) | 🟡 Designed |


**>>Source Code following soon ........<<**
 
