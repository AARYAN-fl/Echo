# ECHO

**Campus knowledge shouldn't disappear when a batch graduates.**

ECHO is a shared memory for a university community. It collects useful answers, advice and campus updates from senior students, then makes that information easier for juniors to find later.

A senior can record a quick voice note on the web app, or useful question-and-answer exchanges can be picked up from a WhatsApp group. When someone searches, ECHO looks for related memories and brings the most relevant ones together into an answer.

The idea is simple: students shouldn't have to ask the same questions every semester just because the people who knew the answers have moved on.

---

## The problem

A lot of the most useful information about college never makes it into a handbook.

It lives in WhatsApp groups, personal chats, conversations after class and advice passed from one batch to the next. That information is hard to search, easy to lose, and often repeated by every new batch.

ECHO gives that knowledge a place to stay.

## What you can do with ECHO

### Record and save knowledge

Seniors can share what they know through the web app. They can record audio in the browser or provide a transcript, add course and topic details, and save it as an Echo. Audio is transcribed before being stored when a transcript is not already provided.

### Search by meaning, not just keywords

A junior can ask a question in their own words. ECHO creates an embedding for the query and compares it with saved memories using cosine similarity. This helps find related advice even when the wording is different.

### Get an answer from multiple memories

When relevant memories are found, ECHO uses its language-model service to combine the context into one response. The matching memories remain available alongside the synthesis, so the answer is not just an isolated chatbot reply.

### Capture useful WhatsApp conversations

The separate WhatsApp bot listens to group messages. It looks for questions and possible replies, checks whether a conversation contains a useful campus Q&A, and sends valid information to the same ECHO store.

It can also look up a question against saved memories and reply in the group when that option is enabled. If it cannot post in a restricted group, it can try sending the answer privately to the student.

### Build confidence through confirmations

When similar information appears again, ECHO can merge it with an existing memory rather than treating it as completely new. Confirmations and the groups in which a memory has appeared are recorded, giving students more context about how widely the advice has been echoed.

### Keep older advice in perspective

Campus information can become outdated. ECHO tracks when a memory was last confirmed and gives it a freshness status:

- **Fresh:** confirmed within the last 6 months
- **Aging:** last confirmed 6–12 months ago
- **Stale:** last confirmed more than a year ago

Older memories can be marked as still true to refresh their confirmation date.

### Find what students still need answers to

Searches that do not find a useful match are logged. The Knowledge Gaps page groups repeated unanswered queries so the team can see what students are looking for and where more senior input would help.

### Share a memory with a QR code

Each Echo can have its own QR code linking to its page. This makes it possible to put relevant knowledge somewhere students might need it, such as a lab, hostel noticeboard or classroom.

---

## How it works

There are two main ways information enters ECHO.

```text
        WEB APP                                WHATSAPP GROUPS
           |                                          |
   Record audio / add text                   Questions and replies
           |                                          |
   Groq Whisper transcription               Question and reply checks
           |                                          |
           +------------------+-----------------------+
                              |
                       Create an embedding
                              |
                    Compare with saved memories
                              |
                    Store or confirm an Echo
                              |
                         SQLite database
                              |
                 +------------+-------------+
                 |                          |
             Web search                 WhatsApp search
                 |                          |
        Relevant memories found       Relevant memories found
                 |                          |
        Answer synthesis with LLM      Reply in the conversation
                 |                          |
                 +------------+-------------+
                              |
                       Knowledge stays
                       available to the
                         next batch
```

For web searches, the query is embedded and compared with stored vectors. Results above the configured similarity threshold are ranked, and the best matches are passed to the synthesis service.

The WhatsApp worker talks to the Flask app through its API. Both channels use the same memory store, so a useful answer captured in a group can later turn up in a web search too.

---

## Tech stack

| Part | Technology | What it does |
|---|---|---|
| Web application | Python, Flask | Serves the application and API |
| Database | SQLite | Stores memories, embeddings and search history |
| Vector operations | NumPy | Calculates cosine similarity for semantic search |
| Speech-to-text | Groq Whisper | Transcribes voice recordings |
| Embeddings | Google Gemini | Converts text and queries into vectors |
| Answer synthesis | Gemini / Groq | Combines relevant memories into a response |
| WhatsApp connection | Node.js, Baileys | Connects the bot to WhatsApp groups |
| QR codes | Python qrcode, Pillow | Generates QR codes for individual memories |
| Deployment | Docker, Railway | Packages and hosts the application |

---

## Project structure

```text
Echo/
├── app.py                  # Flask application setup
├── config.py               # Environment settings and model configuration
├── requirements.txt        # Python dependencies
├── Dockerfile              # Container configuration
├── railway.toml            # Railway deployment settings
├── Procfile                # Gunicorn start command
│
├── database/
│   └── db.py               # SQLite schema and database operations
│
├── routes/
│   ├── echoes.py           # Recording, browsing and memory pages
│   ├── search.py           # Search and answer results
│   └── admin.py            # Stats, knowledge gaps and bot endpoints
│
├── services/
│   ├── embeddings.py       # Text embeddings
│   ├── transcription.py   # Audio transcription
│   ├── search.py           # Semantic search and similarity
│   ├── synthesis.py        # Answer synthesis
│   └── memory_health.py   # Memory freshness calculations
│
├── templates/              # Flask HTML templates
├── static/                 # CSS, JavaScript and audio files
│
└── whatsapp-bot/
    ├── bot.js              # WhatsApp event handling
    └── package.json        # Node dependencies and scripts
```

---

## Run it locally

You will need:

- Python 3.11 or newer
- Node.js 20 or newer (for the WhatsApp bot)
- A Groq API key
- A Google AI Studio API key

### 1. Clone the repository

```bash
git clone https://github.com/bhoiaaryansingh-aafl/Echo.git
cd Echo
```

### 2. Set up the Python app

Create and activate a virtual environment:

```bash
python -m venv .venv
```

On Windows:

```bash
.venv\Scripts\activate
```

On macOS or Linux:

```bash
source .venv/bin/activate
```

Install the dependencies:

```bash
pip install -r requirements.txt
```

### 3. Add your environment variables

Copy the example file:

```bash
# Windows
copy .env.example .env

# macOS / Linux
cp .env.example .env
```

Then add your API keys to `.env`:

```env
GROQ_API_KEY=your_groq_api_key
GEMINI_API_KEY=your_gemini_api_key
SECRET_KEY=your_random_secret_key
```

For local development, you can leave `DATABASE_PATH` and `UPLOAD_FOLDER` unset. The app defaults to a local SQLite database and `static/audio` directory.

### 4. Start the web app

```bash
python app.py
```

Open [http://localhost:5000](http://localhost:5000).

### 5. Start the WhatsApp bot (optional)

Open another terminal:

```bash
cd whatsapp-bot
npm install
npm start
```

The bot uses the WhatsApp multi-device connection. Follow the QR or pairing instructions printed by the bot to link a WhatsApp account.

The bot also needs its runtime configuration and a reachable Flask app URL. Check the configuration values used in `whatsapp-bot/bot.js` before starting it; run the bot only in a group where you have permission to connect it.

---

## Configuration

The main app reads its settings from environment variables. The repository includes `.env.example` as a starting point.

| Variable | Purpose |
|---|---|
| `GROQ_API_KEY` | Groq access for audio transcription and language-model calls |
| `GEMINI_API_KEY` | Gemini access for embeddings and language-model calls |
| `SECRET_KEY` | Flask session and application secret |
| `FLASK_ENV` | Set to `production` for production configuration |
| `DATABASE_PATH` | Location of the SQLite database; defaults to `echo.db` |
| `UPLOAD_FOLDER` | Where uploaded audio is stored; defaults to `static/audio` |

Keep your actual `.env` file and API keys out of Git. The example file is safe to commit; real credentials are not.

---

## Roadmap

ECHO is a work in progress. There are a few things I'd like to improve as I keep building it:

- [ ] **Make search more useful** — Add better filters for courses, topics and batches, and improve how results are ranked.
- [ ] **Let people correct memories** — Give students an easier way to flag advice that's outdated or no longer accurate.
- [ ] **Show where answers come from** — Make it clearer which saved Echoes were used to put an answer together.
- [ ] **Improve the WhatsApp bot** — Make it better at recognising useful conversations and less likely to pick up irrelevant messages.
- [ ] **Use unanswered questions better** — Turn knowledge gaps into a clearer way to see what students are struggling to find.
- [ ] **Make contributions more collaborative** — Explore ways for students to confirm, edit and build on information shared by others.
- [ ] **Improve the mobile experience** — Make recording, searching and browsing easier on phones.
- [ ] **Keep information reliable over time** — Explore more ways to check, refresh and flag memories as campus information changes.

These are ideas rather than fixed deadlines. I'll keep updating this list as ECHO develops.

---

## Deployment

The repository includes a Dockerfile and Railway configuration for deployment.

The Flask app is served with Gunicorn. For persistent deployment, configure a Railway volume and set:

- `DATABASE_PATH=/data/echo.db`
- `UPLOAD_FOLDER=/data/audio`

The WhatsApp bot is a separate Node.js process and needs its own running service and persistent storage for its linked-device session. It also needs the correct Flask service URL so it can send captured memories and search requests to the web app.

---

## A note on campus knowledge

ECHO is meant to preserve useful student-to-student knowledge, not replace official college communication. Advice about exams, attendance, policies or deadlines should always be checked against the latest information from the college.

Please get appropriate permission before connecting a bot to a WhatsApp group or saving messages from it. Avoid storing personal, private or sensitive conversations.

---

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

---

Built for the Scaler School of Technology community.

*People graduate. Knowledge shouldn't.*
