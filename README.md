# PDF & Web Document Analysis API

A FastAPI-based intelligent document analysis service that processes PDFs and web pages, answers questions using LLMs (Mistral & Groq), supports OCR for scanned PDFs, multilingual responses, and puzzle/instruction execution workflows.

## Features

* 📄 PDF document processing
* 🌐 Web page content extraction
* 🔍 OCR support for scanned PDFs using Tesseract
* 🤖 Question answering with Mistral AI
* 🔄 Automatic fallback to Groq (Llama 4 Scout)
* 🌍 Multi-language answer translation
* 🧩 Puzzle solving from embedded document instructions
* ⚡ Async processing for large documents
* 🔐 Token-based API authentication
* 📚 Chunked processing for large PDFs
* 🕸️ HTML content extraction using BeautifulSoup

---

## Architecture

```
Client
   │
   ▼
FastAPI Server
   │
   ├── PDF Extraction (PyMuPDF)
   ├── OCR (Tesseract)
   ├── Web Scraping (BeautifulSoup)
   ├── Instruction Detection & Execution
   ├── Language Detection & Translation
   │
   ├── Mistral AI
   └── Groq Fallback
```

---

## Requirements

### Python Version

* Python 3.10+

### Dependencies

```bash
pip install fastapi uvicorn requests pymupdf pillow pytesseract \
httpx python-dotenv beautifulsoup4 googletrans==4.0.0rc1 \
langdetect pydantic urllib3
```

### System Dependency

Install Tesseract OCR:

#### Ubuntu

```bash
sudo apt update
sudo apt install tesseract-ocr
```

#### Windows

Download from:

https://github.com/tesseract-ocr/tesseract

Configure:

```python
pytesseract.pytesseract.tesseract_cmd = \
r"C:\Program Files\Tesseract-OCR\tesseract.exe"
```

---

## Environment Variables

Create a `.env` file:

```env
MISTRAL_API_KEY=your_mistral_api_key
GROQ_API_KEY=your_groq_api_key
API_TOKEN=your_api_token
```

---

## Running the Application

```bash
uvicorn main:app --host 0.0.0.0 --port 8000
```

Server:

```text
http://localhost:8000
```

---

## API Endpoints

### Health Check

#### GET /

Response

```json
{
  "message": "PDF API is running"
}
```

---

### Document Analysis

#### POST /api/v1/hackrx/run

Headers

```http
Authorization: Bearer <API_TOKEN>
Content-Type: application/json
```

Request Body

```json
{
  "documents": "https://example.com/document.pdf",
  "questions": [
    "What is the waiting period?",
    "Does the policy cover hospitalization?"
  ]
}
```

---

## Processing Logic

### PDF ≤ 100 Pages

* Full text extraction
* Single LLM prompt
* Mistral response

### PDF 101–200 Pages

* Chunked extraction
* Context aggregation
* Mistral → Groq fallback

### PDF > 200 Pages

* Title extraction only
* Public knowledge fallback

### Web URLs

* HTML extraction
* Content cleanup
* Direct QA

---

## OCR Support

If a PDF page contains no extractable text:

1. Convert page to image
2. Run Tesseract OCR
3. Merge OCR text into document context

---

## Puzzle Detection

The API detects embedded instructions such as:

```text
GET https://example.com/api
POST https://example.com/api {"id":1}
```

Supported methods:

* GET
* POST
* PUT
* DELETE

Execution results are passed to the LLM to solve hidden puzzles.

---

## Authentication

All analysis requests require:

```http
Authorization: Bearer <API_TOKEN>
```

Unauthorized requests return:

```json
{
  "detail": "Unauthorized"
}
```

---

## Example cURL

```bash
curl -X POST "http://localhost:8000/api/v1/hackrx/run" \
-H "Authorization: Bearer YOUR_TOKEN" \
-H "Content-Type: application/json" \
-d '{
  "documents":"https://example.com/policy.pdf",
  "questions":[
    "What is covered?",
    "What is the waiting period?"
  ]
}'
```

---

## Response Example

```json
{
  "answers": [
    "The policy covers hospitalization expenses.",
    "The waiting period is 30 days."
  ]
}
```

Puzzle mode:

```json
{
  "answer": "FINAL_PUZZLE_SOLUTION"
}
```

---

## Project Structure

```text
.
├── main.py
├── .env
├── requirements.txt
├── README.md
└── assets/
```

---

## Technologies Used

* FastAPI
* PyMuPDF (fitz)
* Tesseract OCR
* Mistral AI
* Groq API
* BeautifulSoup
* Google Translate
* LangDetect
* HTTPX
* Requests

---

## Future Improvements

* Vector database retrieval (RAG)
* Embedding-based semantic search
* Streaming responses
* Batch document processing
* Caching layer (Redis)
* Docker deployment
* Kubernetes support
* Rate limiting
* OpenTelemetry monitoring

---

## License

MIT License

---

## Author

Document Analysis API built with FastAPI, OCR, and LLM-powered question answering.
