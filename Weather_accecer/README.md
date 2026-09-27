# Weather Accesser (Assignment 1)

A Jupyter notebook where a **Google Gemini** agent uses native automatic function calling to fetch **live** weather data (via the real OpenWeatherMap API) for multiple cities sequentially, then calculates the average temperature across them.

## Agent & Tech Stack

| Component | What it does |
|---|---|
| **Google GenAI SDK** (`google-genai`, imported as `from google import genai`) | Client + native automatic function-calling agent — no third-party agent framework used |
| **Gemini** (`gemini-3.5-flash-lite`) | The LLM powering the agent |
| **OpenWeatherMap REST API** (via `requests`) | Real, live weather data — this is a genuine external API call, not a demo/hardcoded dataset |
| **python-dotenv** | Loads secrets from `.env` |
| **Jupyter / ipykernel** | Notebook execution environment |

### Tool available to the agent
- `get_weather(city)` — calls `https://api.openweathermap.org/data/2.5/weather` and returns temperature (°C), condition, and humidity for the given city

## Requirements

- **Python 3.11.9** (this is the exact version the notebook was authored/tested with — see Known Issues)
- Packages:
  ```bash
  pip install google-genai requests python-dotenv jupyter ipykernel
  ```
- A `.env` file in this folder containing:
  ```
  GEMINI_API_KEY=your_gemini_api_key_here
  OPENWEATHERMAP_API_KEY=your_openweathermap_api_key_here
  ```
  - Get a Gemini key at https://aistudio.google.com/apikey — it must be a real API key, format `AIzaSy...`. **Note:** Google has recently been issuing some keys in a new `AQ.`-prefixed "Authentication Key" format instead. As of this writing, `AQ.`-prefixed keys are known to intermittently fail against the Generative Language REST API with a `401 ACCESS_TOKEN_TYPE_UNSUPPORTED` error — this is a documented, ongoing issue on Google's side (see their developer forum), not a bug in this code. If your key comes out as `AQ.`-prefixed and fails, try regenerating it, or check Google's forum for the current status of this rollout.
  - Get an OpenWeatherMap key (free tier available) at https://openweathermap.org/api
- **Live internet access is mandatory** — this notebook makes real calls to both Google's API and OpenWeatherMap's API and will fail without connectivity or valid, active keys.

## How to Run

1. **Register/select the correct kernel.** This is the single most common source of confusing errors with this notebook. Notebook apps (Jupyter, VS Code, Antigravity, etc.) track the selected Python interpreter independently of the notebook file — if it silently points at a different Python than the one you installed `google-genai` into, you'll get `ImportError: cannot import name 'genai' from 'google'` even though the package is genuinely installed somewhere on your machine.
   - Register your environment as a named kernel once:
     ```bash
     python -m ipykernel install --user --name=ai-genai-venv --display-name "AI-GenAI-labs (venv)"
     ```
   - Explicitly select **"AI-GenAI-labs (venv)"** (or equivalent) from the kernel picker in your notebook UI before running any cells.
   - If unsure which interpreter is active, run this in a cell:
     ```python
     import sys
     print(sys.executable)
     ```
     It should point at the same environment where you ran `pip install google-genai`.
2. Open `Assignment 1.ipynb`.
3. Run cells top to bottom. The `genai.Client(...)` initialization should be called with `api_key=` **explicitly passed** rather than relying on implicit environment-variable auto-detection — this avoids the client silently switching into Vertex AI/OAuth mode if unrelated Google Cloud environment variables (e.g. `GOOGLE_APPLICATION_CREDENTIALS`, `GOOGLE_GENAI_USE_VERTEXAI`) happen to exist on your machine.
4. Edit the `user_prompt` in the final cell to ask about any cities you like.

## Known Issues / Troubleshooting

| Symptom | Likely Cause | Fix |
|---|---|---|
| `ImportError: cannot import name 'genai' from 'google'` | Wrong kernel/interpreter selected — `google-genai` isn't installed there | Check `sys.executable`; reselect the correct kernel |
| `401 UNAUTHENTICATED — ACCESS_TOKEN_TYPE_UNSUPPORTED` | `AQ.`-prefixed key hitting a known Google auth-routing issue, or `genai.Client()` called with no args and silently defaulting to Vertex/OAuth mode | Pass `api_key=` explicitly to `genai.Client()`; check for stray `GOOGLE_*` env vars; regenerate the key |
| `Running cells with 'Python X.X' requires the ipykernel package` | Notebook's kernel metadata points at a bare system Python with no `ipykernel` installed | Register and select the correct venv kernel (see step 1 above) |
| This is **not** a free-tier vs. paid-tier issue if you're seeing `401` errors — tier/quota problems show up as `429 RESOURCE_EXHAUSTED` instead. | — | — |
