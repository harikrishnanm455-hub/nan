# ComicCraft – AI Comic Story Creator

ComicCraft is a full-stack FastAPI application that turns a short story idea into a five-panel comic. The app accepts a story prompt, main character, setting, tone, and art style, then generates a complete comic preview and downloadable PDF.

## Features

- Story input form and modern comic-generation interface
- Gemini-powered five-panel outline generation
- Story continuity for narration, captions, and dialogue
- Hugging Face image generation for each panel
- Comic preview page with responsive comic-card layout
- PDF export using FPDF2
- Demo mode for local testing without API costs
- Health and JSON endpoints for API use
- Input validation and user-friendly error handling

## Architecture

ComicCraft follows a clean separation of concerns:

- `app/main.py` – FastAPI app bootstrap
- `app/routes.py` – routes and HTML responses
- `app/config.py` – environment configuration
- `app/schemas.py` – request and response validation
- `app/services/gemini_service.py` – Gemini outline and story generation
- `app/services/image_generator.py` – Hugging Face image generation and demo images
- `app/services/exporters.py` – PDF export service
- `app/services/layout_builder.py` – orchestration of story, images, and layout
- `app/utils/security.py` – safe filename and file access helpers
- `templates/` – HTML templates for the frontend
- `static/` – CSS, JS, generated images, and exports

## Project Structure

```text
comiccraft/
├── app/
│   ├── __init__.py
│   ├── main.py
│   ├── routes.py
│   ├── config.py
│   ├── schemas.py
│   ├── services/
│   │   ├── __init__.py
│   │   ├── gemini_service.py
│   │   ├── image_generator.py
│   │   ├── layout_builder.py
│   │   └── exporters.py
│   └── utils/
│       ├── __init__.py
│       └── security.py
├── templates/
│   ├── index.html
│   ├── comic_preview.html
│   ├── export_success.html
│   └── error.html
├── static/
│   ├── css/
│   │   └── style.css
│   ├── js/
│   │   └── app.js
│   ├── panels/
│   └── exports/
├── tests/
│   ├── test_health.py
│   ├── test_validation.py
│   ├── test_layout.py
│   └── test_export.py
├── .env
├── .env.example
├── .gitignore
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
├── README.md
└── app.py
```

## Requirements

The application uses the following Python packages:

- fastapi
- uvicorn
- jinja2
- python-multipart
- pydantic
- pydantic-settings
- google-genai
- huggingface_hub
- pillow
- fpdf2
- python-dotenv
- httpx
- pytest
- pytest-asyncio

## Gemini API Setup

1. Create a Google AI account or access your Gemini API key.
2. Add the API key to `.env` as:

```env
GEMINI_API_KEY=YOUR_GEMINI_API_KEY
GEMINI_OUTLINE_MODEL=gemini-3.5-flash-lite
GEMINI_STORY_MODEL=gemini-3.8-flash
```

Use the current Google Python SDK (`google-genai`) instead of deprecated alternatives.

## Hugging Face API Setup

1. Create or access a Hugging Face token.
2. Add the token to `.env`:

```env
HF_TOKEN=YOUR_HUGGINGFACE_TOKEN
IMAGE_MODEL=black-forest-labs/FLUX.1-schnell
IMAGE_PROVIDER=auto
```

The app uses a hosted Hugging Face inference API rather than downloading large local ML models.

## .env Configuration

Create a `.env` file at the project root with the structure below:

```env
GEMINI_API_KEY=YOUR_GEMINI_API_KEY
HF_TOKEN=YOUR_HUGGINGFACE_TOKEN
GEMINI_OUTLINE_MODEL=gemini-3.5-flash-lite
GEMINI_STORY_MODEL=gemini-3.8-flash
IMAGE_MODEL=black-forest-labs/FLUX.1-schnell
IMAGE_PROVIDER=auto
DEMO_MODE=true
```

Set `DEMO_MODE=false` only when you are ready to use the real Gemini and Hugging Face APIs.

## Windows Installation

```powershell
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

## Linux/macOS Installation

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## Demo Mode

Demo mode is designed to allow the app to run without external API costs. When `DEMO_MODE=true`, the backend generates deterministic five-panel comic content and placeholder panel images locally.

## Real AI Mode

When `DEMO_MODE=false` and the required keys are configured:

- Gemini generates the panel outline
- Gemini writes narration and dialogue
- Hugging Face generates one image per panel

If a key is missing, the API returns a user-friendly configuration error instead of crashing the server.

## Running the Application

```bash
uvicorn app.main:app --reload
```

Then open the app at:

- http://127.0.0.1:8000
- http://127.0.0.1:8000/docs

## API Documentation

FastAPI automatically provides interactive docs at:

- Swagger UI: http://127.0.0.1:8000/docs
- ReDoc: http://127.0.0.1:8000/redoc

## Testing

Run:

```bash
pytest -q
```

The suite checks:

- health endpoint
- homepage rendering
- input validation
- exact five-panel comic generation
- PDF export
- demo generation flow
- JSON API responses

## Docker

Build and run with:

```bash
docker compose up --build
```

The container reads variables from `.env` and runs the app at port 8000.

## Troubleshooting

- If the page shows a configuration error, check `.env` values and `DEMO_MODE`.
- If an image generation request fails, the panel-specific error is captured and returned to the user without crashing the service.
- If the PDF export fails, check that the output directory is writable.
- If Gemini returns malformed data, the backend validates the output and surfaces a helpful error message.

## Security Notes

- Secrets are loaded from environment variables, not hard-coded in source files.
- `.env` is ignored by Git.
- Request inputs are length-limited and validated.
- Only safe files in `static/exports` are downloadable.
- The app does not expose API keys in responses or templates.
