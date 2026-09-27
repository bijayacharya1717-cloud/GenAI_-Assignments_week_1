# Strands Travel Planner (1-day itinerary)

A Jupyter notebook where a **Strands Agent** builds a 1-day tabular travel itinerary — weather, top attractions, and cost breakdown — for a city the user names, using an LLM hosted on **Groq**.

## Agent & Tech Stack

| Component | What it does |
|---|---|
| **Strands Agents SDK** (`strands`) | Agent framework — `Agent`, `@tool` decorators, `SequentialToolExecutor` |
| **Groq** (`openai/gpt-oss-120b`) | The LLM powering the agent, via Strands' OpenAI-compatible model wrapper |
| **python-dotenv** | Loads secrets from `.env` |
| **Jupyter / ipykernel** | Notebook execution environment |

### Tools available to the agent
- `City_names(city)` — looks up a city against a tiny demo `city_db`
- `weather_status(celcius, city_name)` — records a temperature (⚠️ see Known Issues below)
- `search_attractions(city_name)` — hardcoded top-3-attractions for Damak, Kathmandu, Pokhara only
- `Calculating_costs(total_expenses)` — records a total cost figure

## Requirements

- **Python 3.11.9** (this is the exact version the notebook was authored/tested with — see Known Issues)
- Packages:
  ```bash
  pip install strands-agents pydantic python-dotenv jupyter ipykernel
  ```
- A `.env` file in this folder containing:
  ```
  GROQ_API_KEY=your_groq_api_key_here
  ```
  (A `GEMINI_API_KEY` is also read and printed for a sanity check, but not actually used by the agent — Groq is the only real dependency here.)
- Internet access (to reach Groq's API)

## How to Run

1. **Register/select the correct kernel.** Notebooks don't automatically use the right Python interpreter — if you have more than one Python installed (a system Python and a project `venv`, for example), Jupyter/your IDE can silently default to the wrong one, causing `ModuleNotFoundError` for `strands` even though it's installed.
   - If using a dedicated virtual environment, register it once:
     ```bash
     python -m ipykernel install --user --name=ai-genai-venv --display-name "AI-GenAI-labs (venv)"
     ```
   - Then explicitly select that kernel in your notebook UI's kernel picker before running anything.
2. Open `1 day travel planner.ipynb`.
3. Run all cells top to bottom.
4. Edit the `user_query` string in the final cell to ask about any city you like.

## Known Issues (from the current code — not environment-related)

- **`weather_status` always shows 0°C.** The function signature is `weather_status(celcius, city_name)` — it expects the model to *pass in* a temperature, but the model has no real source for that value, so it defaults to `0.0`. To get real weather, this would need to call a live API (like the `Weather accesser` project does) instead of relying on the model to invent a number.
- **`search_attractions` only recognizes 3 hardcoded cities** (Damak, Kathmandu, Pokhara — note the capitalization must match exactly). Any other city returns `"no places found"`, though the agent's system prompt will often have the underlying LLM fill in the gap from its own general knowledge instead — worth being aware of if you need guaranteed factual accuracy.
- If you hit `ModuleNotFoundError` or `ImportError` errors that seem to contradict packages you're sure are installed, check `sys.executable` in a cell to confirm the notebook is actually running on the Python environment you installed packages into.
