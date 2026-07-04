# Setup Guide

## Prerequisites

- n8n running locally (or self-hosted)
- A Telegram account
- A Google account
- (Optional but recommended) An APITemplate.io free account for PDF invoices

## Step 1 — Create the Telegram bot

1. Open Telegram, search **@BotFather**, send `/newbot`.
2. Choose a name and username; BotFather returns a **bot token**.
3. In n8n: Credentials → New → **Telegram API** → paste the token.

## Step 2 — Get a Gemini API key (free)

1. Go to https://aistudio.google.com/apikey and sign in with Google.
2. Create an API key (free tier: ~1,500 requests/day on Flash models, resets every 24h).
3. In n8n: Credentials → New → **Google Gemini (PaLM) API** → paste the key.

## Step 3 — Create the khata Google Sheet

1. Create a new blank Google Sheet.
2. File → Import → Upload → `sheets/khata-Bills.csv` → **Replace spreadsheet** → rename the tab to exactly **Bills**.
3. File → Import → Upload → `sheets/khata-Summary.csv` → **Insert new sheet(s)** → rename to **Summary**.
4. The Summary tab auto-calculates from Bills — no manual work ever.

## Step 4 — Import the workflow

1. In n8n: Workflows → Import from File → select `workflows/kaccha-bill-bot.json`.
2. Attach your **Telegram credential** on: Telegram Trigger, Download Photo, Send Invoice + Buttons, Send Error Reply, Ask For Photo, Answer Button Tap, Edit Invoice Message, Send PDF.
3. Attach your **Gemini credential** on: Gemini Vision - Bill to JSON. Open the Model dropdown and re-select a Flash model if needed.
4. Create a **Google Sheets OAuth2** credential (Sign in with Google) and attach it on **Log to Khata** and **Update Khata Status**. In BOTH nodes, select your khata document and the **Bills** sheet.
   - In "Update Khata Status": set Column to Match On = **UID**, fill only UID and Confirm values, leave all other columns empty.
5. (PDF) Sign up free at https://apitemplate.io → dashboard → **API Integration** → copy your API key → open **Create PDF (APITemplate)** node → replace `PASTE_YOUR_APITEMPLATE_API_KEY_HERE` in the X-API-KEY header.

## Step 5 — Activate

Toggle the workflow to **Active** (top-right) and save. The bot now runs continuously — no manual execution needed. Note: since n8n runs locally, the bot only works while your computer and n8n are running.

## Security notes

- Never commit real API keys or tokens to Git — the JSON in this repo contains placeholders only.
- If you ever paste a real key into the workflow and export it, scrub it before committing.
