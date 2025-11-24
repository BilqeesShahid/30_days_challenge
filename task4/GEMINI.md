# Role: Senior Python AI Engineer – Study Notes Summarizer & Quiz Generator Agent

&gt; Deliver a single, self-contained repo that turns any PDF into concise study notes + a customizable quiz.  
&gt; Use **only** the OpenAI-Agents SDK (never the vanilla `openai` lib) wired to **Gemini 2.0 Flash** via the official OpenAI-compatible endpoint.

---

## 1. Tech Stack & Constraints (non-negotiable)

| Component        | Choice                                                                 |
|------------------|------------------------------------------------------------------------|
| LLM SDK          | `openai-agents` (latest)                                               |
| Model            | `gemini-2.0-flash`                                                     |
| Base URL         | `https://generativelanguage.googleapis.com/v1beta/openai/`             |
| PDF parser       | `pypdf`                                                                |
| Doc search       | Context7 MCP Docs Reader (built-in tool)                               |
| UI framework     | Streamlit (lightweight, pure-Python)                                   |
| Python manager   | `uv` (all deps added via `uv add &lt;pkg&gt;`)                               |
| Code style       | Zero-bloat: no decorators, no retry wrappers, no unused imports        |

---

## 2. What the Agent Must Do

1. Accept PDF upload (Streamlit file uploader).  
2. Extract raw text with `pypdf`.  
3. Produce a **concise, student-friendly summary** (markdown).  
4. Persist summary to `memory/summaries.json`.  
5. Let user pick:
   - Quiz type → `MCQ` | `Short` | `Mixed`  
   - Question count → any int ≥ 3  
6. Generate quiz **from full text**, never from summary.  
7. Display quiz in numbered cards (A/B/C/D for MCQ, text-box for short).  
8. Allow one-click download of summary & quiz (JSON or PDF).

.
├── .env                    # GEMINI_API_KEY=...
├── pyproject.toml          # uv-managed deps
├── main.py                 # Streamlit UI + agent orchestration
├── tools.py                # 2 SDK-compliant tools
├── memory/
│   └── summaries.json      # auto-created
├── uploads/                # temp PDF cache (git-ignored)
└── README.md               # setup & run instructions


## 4. SDK-Compliant Tools (`tools.py`)

```python
from openai_agents import tool
import pypdf, pathlib, uuid, json, datetime, os

@tool
def extract_pdf_text(file_path: str) -> str:
    """Extract plain text from a local PDF file."""
    reader = pypdf.PdfReader(file_path)
    return "\n".join(page.extract_text() or "" for page in reader.pages)

@tool
def generate_quiz(text: str, q_type: str, n: int) -> list[dict]:
    """
    Generate n quiz questions from the provided text.
    q_type: 'MCQ' | 'Short' | 'Mixed'
    Returns list of dicts with keys: id, question, options (if MCQ), answer.
    """
    # Agent will implement the actual LLM call; this is the schema stub.
    return []
``` 
# 5. Agent Initialization (main.py)
```python
import os, streamlit as st, pathlib, uuid, json, datetime
from openai_agents import Agent, OpenAI
from tools import extract_pdf_text, generate_quiz

client = OpenAI(
    api_key=os.getenv("GEMINI_API_KEY"),
    base_url="https://generativelanguage.googleapis.com/v1beta/openai/"
)

agent = Agent(
    model="gemini-2.0-flash",
    tools=[extract_pdf_text, generate_quiz],
    system="You are a study assistant. Summarize clearly, then create accurate quizzes from the full text."
)
```
# 6. Streamlit UI Flow

Step	Widget	Action
①	st.file_uploader("Upload PDF", type="pdf")	Save to uploads/
②	st.button("Summarize", type="primary")	Run extract_pdf_text → agent summary → display & persist
③	st.selectbox("Quiz type", ["MCQ","Short","Mixed"])	
④	st.number_input("# Questions", min_value=3, max_value=50)	
⑤	st.button("Create Quiz")	Run generate_quiz with full text → display cards
⑥	st.download_button	Export summary or quiz as JSON/PDF

# 7. Style & UX Checklist
Header: 📘 Study Notes Summarizer & Quiz Generator
Color palette: light gradient background; cards with subtle shadow + rounded corners
Buttons: crimson red, darken 10 % on hover
Progress spinner during LLM calls
Sidebar (optional): history of past PDFs; click to reload summary
8. Testing Scenarios
Table
Copy
#	Test	Expected
1	Upload 1-page PDF	Summary card appears within 5 s
2	Click “Create Quiz” (MCQ, 5 q)	5 numbered cards, each with 4 options
3	Upload 200-page textbook	Summary remains concise; quiz still uses full text
4	Invalid file (jpg)	Error toast, no crash
5	Missing GEMINI_API_KEY	Clean exit with helpful message
9. Quick Start (copy-paste for README)
 
uv venv && source .venv/bin/activate
uv add streamlit pypdf openai-agents
echo "GEMINI_API_KEY=your_key" > .env
streamlit run main.py

10. Delivery Checklist
[ ] Only openai-agents imported (no openai).
[ ] Both tools follow exact SDK signature.
[ ] Quiz always generated from raw text, not summary.
[ ] Summary stored in memory/summaries.json.
[ ] UI meets color & layout specs.
 


