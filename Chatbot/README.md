# Planner Chatbot

A Gradio-based chat UI where a **Strands Agent** acts as a travel-planning assistant. The agent uses three tools (weather lookup, attraction search, calculator) — all backed by hardcoded demo data, not live APIs — reasoned over by an LLM hosted on **Groq**.

## Agent & Tech Stack

| Component | What it does |
|---|---|
| **Strands Agents SDK** (`strands`) | Agent framework — defines the `Agent`, `@tool` decorators, and `SequentialToolExecutor` |
| **Groq** (`openai/gpt-oss-120b`) | The LLM powering the agent, called via Strands' OpenAI-compatible model wrapper |
| **Gradio** | Web chat interface (`gr.ChatInterface`) |
| **python-dotenv** | Loads secrets from `.env` |

### Tools available to the agent
- `get_weather(city)` — hardcoded weather lookup (Kathmandu, Pokhara, London, New York)
- `search_attractions(city)` — hardcoded top-3-attractions lookup for the same cities
- `calculator(expression)` — evaluates basic math expressions

## Requirements

- **Python 3.11+**
- Packages:
  ```bash
  pip install strands-agents gradio python-dotenv
  ```
- A `.env` file in this folder containing:
  ```
  GROQ_API_KEY=your_groq_api_key_here
  ```
  Get a free key at https://console.groq.com/keys
- Internet access (to reach Groq's API — the tools themselves work fully offline)

## How to Run

```bash
python chatbot.py
```

Gradio will launch a local web UI (URL printed in the terminal, typically `http://127.0.0.1:7860`) — open it in your browser and start chatting.

## Notes / Known Limitations

- `get_weather` and `search_attractions` only recognize the 4 cities hardcoded above (case-insensitive); any other city returns a "not available" message.
- `calculator` uses Python's `eval()` with builtins disabled — safe for simple arithmetic, but don't feed it untrusted input in production.
- If you see `ModuleNotFoundError: No module named 'strands'`, double check you're running this with the **same Python interpreter** where you ran `pip install strands-agents` — a common cause of this error is having multiple Python installations/virtual environments on the machine.
