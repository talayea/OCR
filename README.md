# Gemini 2.5 Flash OCR 🔍

An OCR pipeline that uses **Google Gemini 2.5 Flash** to detect and extract text from images, with bounding box visualization powered by **Ultralytics**.

---

## Overview

This notebook demonstrates a multimodal OCR workflow where Gemini 2.5 Flash acts as the vision-language backbone — it takes an image and a prompt, then returns detected text regions with their spatial coordinates (`box_2d`) and labels. The results are visualized directly on the image using Ultralytics' `Annotator`.

---

## Features

- 🤖 **Gemini 2.5 Flash** inference for text extraction
- 📦 **Bounding box detection** with normalized coordinate scaling
- 🎨 **Annotated output** with color-coded labels per detected region
- 🖼️ Works on any image (local or downloaded)
- 🔧 Configurable temperature for inference creativity

---

## Tech Stack

| Tool | Role |
|------|------|
| `google-genai` | Gemini 2.5 Flash API client |
| `Ultralytics` | Bounding box annotation & image utilities |
| `Pillow` | Image loading and display |
| `OpenCV` | Image reading and color conversion |

---

## Setup

```bash
pip install -U -q google-genai "Pillow==9.5.0" ultralytics
```

Set your Gemini API key:

```python
client = genai.Client(api_key="YOUR_API_KEY")
```

> ⚠️ Never hardcode API keys in production. Use environment variables or a secrets manager.

---

## How It Works

1. **Load an image** — downloaded or local, read via OpenCV and converted to PIL format.
2. **Send to Gemini** — image + a text prompt asking for text extraction with bounding boxes.
3. **Parse the response** — model returns a JSON list of `{ box_2d: [y1, x1, y2, x2], label: "..." }` objects.
4. **Scale coordinates** — normalized values (0–1000 range) are scaled to actual pixel dimensions.
5. **Annotate and display** — Ultralytics `Annotator` draws labeled boxes on the original image.

---

## Usage

```python
image, w, h = read_image("your_image.png")

results = inference(image, prompt + output_prompt)
cln_results = json.loads(clean_results(results))

annotator = Annotator(image)
for idx, item in enumerate(cln_results):
    y1, x1, y2, x2 = item["box_2d"]
    # scale + annotate ...
```

---

## Model

- **Default:** `gemini-2.5-flash`
- **Alternative:** `gemini-2.5-pro-exp-03-25` (swap in the `inference()` function)

---

## Notes

- Gemini returns bounding box coordinates in `[y1, x1, y2, x2]` order (y-first). The code handles this correctly.
- Coordinates are swapped if `x1 > x2` or `y1 > y2` to ensure valid bounding boxes.
- Temperature defaults to `0.5` — lower for more deterministic output, higher for more creative.

---

## License

MIT
