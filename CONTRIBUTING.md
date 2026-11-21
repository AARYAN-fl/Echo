# Contributing to ECHO

Thanks for taking an interest in ECHO.

ECHO is being built to make useful campus knowledge easier to find and easier to pass on. There is plenty that can be improved, and contributions of different sizes are welcome — from fixing a small bug to working on a feature.

You do not need to know the whole codebase before getting started. Just let us know what you are planning to change and keep the work focused.

## Before you start

A few things are useful to have installed:

- Python 3.11 or newer
- Node.js 20 or newer (only needed for the WhatsApp bot)
- Git
- A Groq API key and a Google AI Studio API key for running the app's AI features

Please read the main [README](README.md) for an overview of ECHO and the local setup instructions.

## Finding something to work on

You can contribute in a few ways:

- Fix a bug or improve an existing feature.
- Make search results more relevant or easier to understand.
- Improve the recording, browsing or mobile experience.
- Work on the WhatsApp bot and how it identifies useful conversations.
- Improve documentation or add setup instructions.
- Suggest an idea that would make ECHO more useful to students.

For a larger change, open an issue first or start a discussion before writing a lot of code. This helps avoid spending time on something that may not fit the direction of the project.

For small fixes, you can usually go ahead and open a pull request.

## Setting up your workspace

1. Fork the repository on GitHub.
2. Clone your fork:

   ```bash
   git clone https://github.com/<your-username>/Echo.git
   cd Echo
   ```

3. Create a branch for your change:

   ```bash
   git checkout -b fix/short-description
   ```

4. Set up the Python environment and install the dependencies:

   ```bash
   python -m venv .venv
   ```

   Activate it on Windows:

   ```powershell
   .venv\\Scripts\\activate
   ```

   Or on macOS/Linux:

   ```bash
   source .venv/bin/activate
   ```

   Then install the requirements:

   ```bash
   pip install -r requirements.txt
   ```

5. Create your local environment file using `.env.example`:

   Windows:

   ```powershell
   copy .env.example .env
   ```

   macOS/Linux:

   ```bash
   cp .env.example .env
   ```

   Add your own API keys to `.env`. Do not commit this file.

6. Start the app:

   ```bash
   python app.py
   ```

   Open http://localhost:5000 in your browser.

### Working on the WhatsApp bot

The bot is a separate Node.js project inside `whatsapp-bot/`. You only need this setup if your change involves the bot.

```bash
cd whatsapp-bot
npm install
npm start
```

Check the bot's configuration before starting it. Use a test account or group where you have permission to connect the bot. Do not use private group messages as test data without permission.

## Making changes

A few things help keep the project easy to work on:

- Try to keep each pull request focused on one change.
- Follow the style already used in the part of the code you are editing.
- Give variables, functions and files names that explain what they do.
- Avoid adding dependencies unless they are needed.
- If you change how something works, update the relevant documentation as well.
- Do not commit API keys, credentials, local databases, audio uploads or private conversations.

If a feature involves student-submitted information, think about privacy and how the information might be misunderstood or become outdated. ECHO should preserve useful campus knowledge, not expose personal conversations.

## Testing your changes

Before opening a pull request, run the app and try the part you changed.

For example:

- For a search change, try a few differently worded questions and check that the results make sense.
- For recording or transcription changes, test both the audio flow and the transcript flow where possible.
- For WhatsApp changes, test with a group or account where you have permission.
- For UI changes, check that the page still works at a smaller screen size.
- For documentation changes, make sure commands, paths and filenames are correct.

If you add or change automated tests, include them with your contribution. If something cannot be tested locally because it needs an API key, an external service or a WhatsApp session, mention that in the pull request.

## Opening a pull request

When your change is ready:

1. Commit your work with a message that explains what changed.

   ```bash
   git add .
   git commit -m "Improve search result ranking"
   ```

2. Push your branch to your fork:

   ```bash
   git push origin fix/short-description
   ```

3. Open a pull request against the `main` branch of the ECHO repository.

In the pull request description, include:

- What you changed and why.
- Any issue the change addresses (link it if there is one).
- How you tested it.
- Screenshots or a short recording for visible UI changes.
- Anything reviewers should know, including parts you could not test.

A pull request does not need to be perfect before it is opened. It is fine to ask questions or get feedback while working on it. Please be open to review comments and make updates where needed.

## A few things to keep in mind

ECHO is still evolving, so some parts of the codebase and product may change. If you are unsure about an implementation, ask before building around it.

Please be respectful in issues, discussions and code reviews. Constructive feedback is welcome; personal or dismissive comments are not.

Thanks for helping make ECHO better. Every useful improvement, however small, helps campus knowledge last a little longer.
