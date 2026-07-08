# Text-based PDF → Annotatable HTML Viewer

> As long as your PDF is **text-based** (embedded selectable text, not a pure scanned image), you can turn it into
> **a web page where the text is selectable, highlightable, and annotatable** — fully local, no third-party service, no CDN.

Drop in a textbook / paper / document, then **select text right on the page just like in a PDF**, highlight or underline it with one click, and write notes. Annotations survive refresh and can be **exported as a self-contained HTML file** (a single file you can send to others and they can keep annotating).

---

## ✨ What it solves

| Traditional approach | This tool |
|----------------------|-----------|
| Screenshot OCR, PNGs everywhere, file count explodes | Extracts only **text + coordinates**; one book ≈ 2 JSON files, almost zero disk cost |
| Annotations locked inside a PDF reader | Annotations live in the web page — exportable, shareable, editable |
| Relies on cloud drives / online SaaS, privacy leaks | Fully local; the source PDF is deleted right after extraction |
| Text not selectable, copy is a pain | Text is natively selectable and copyable |

> 💡 **Design philosophy**: don't dump junk on your computer, don't let the file count explode. The source PDF is extracted and discarded immediately after upload; only tiny structured data stays on disk.

---

## 🚀 Quick start

```bash
# 1) Install dependencies (just one)
pip install -r requirements.txt

# 2) Launch (pure standard library, no Flask / database needed)
python app.py
# default http://127.0.0.1:8000
# to share on the local network: python app.py --host 0.0.0.0 --port 9000

# 3) Open the browser, upload a text-based PDF, and start annotating
```

Requires Python 3.8+. Other than `pymupdf` there are no other runtime dependencies.

---

## 🖱 How to use

1. **Upload**: drag / pick any text-based PDF on the home page.
2. **Read + annotate**:
   - Simply **select a passage with the mouse**; a toolbar pops up → choose "Highlight / Underline / Note".
   - "Note" opens an input box for your comment; notes land in the right sidebar and can be edited or deleted anytime.
   - The top slider zooms the page (0.5×–3×); long documents lazy-render automatically, no lag.
3. **Annotation persistence**: in the online version, annotations are stored on the server in `data/` and survive refresh and browser restart.
4. **Export**: click "Export HTML" → get a **single `.html` file** carrying all text, layout, and annotations;
   double-click to open, **annotate offline**, and send it directly to classmates / colleagues to keep annotating.

> ⚠️ Only **text-based** PDFs are supported. A pure scan (no embedded text) will prompt "no extractable text" —
> run such files through an OCR tool first to make a text PDF, then upload.

---

## 📂 Project structure

```
.
├── app.py            # Backend: pure standard-library HTTP service (upload / read / annotate / export)
├── pdftext.py        # PDF text + layout extraction (PyMuPDF), outputs compact JSON
├── static/
│   ├── index.html    # Upload page + read/annotate page (dual view, same JS)
│   ├── style.css     # Styles
│   └── app.js        # Frontend logic: rendering / selection→annotation mapping / sidebar / export
├── requirements.txt  # Only pymupdf
├── poster.pdf        # A small sample (demo only, tracked)
└── data/             # Generated at runtime: extraction result + annotations per doc (gitignored, not in repo)
```

---

## 🔌 API (HTTP)

| Method | Path | Description |
|--------|------|-------------|
| GET  | `/api/info` | Service info |
| GET  | `/` or `/index.html` | Frontend page |
| POST | `/api/upload` | Raw PDF bytes + header `X-Filename`; returns `{id, title, page_count}` after extraction |
| GET  | `/api/doc/<id>` | Extraction result (text spans / images / metadata) |
| GET  | `/api/docs` | List of uploaded documents |
| GET/POST | `/api/annotations/<id>` | Read / overwrite-save annotations (JSON array) |
| GET  | `/api/export/<id>` | Export **self-contained annotatable HTML** (single file) |
| DELETE | `/api/doc/<id>` | Delete a document and its annotations |

> The exported standalone HTML stores annotations in `localStorage` and needs no backend.

---

## 🧱 Tech stack

- **Backend**: Python standard library `http.server` (thread-safe), no framework, no database.
- **Extraction**: PyMuPDF 1.24+ (`fitz`), extracts per-span text, font size, weight, color, and coordinates.
- **Frontend**: vanilla HTML / CSS / JS, no build step, no npm dependencies.
- **Storage**: one directory per document `data/<id>/` containing `doc.json` and `annotations.json`.

---

## 📄 License

MIT License.
