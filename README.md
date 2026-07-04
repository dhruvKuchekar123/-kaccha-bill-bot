# 🧾 Kaccha Bill Bot — Rough Bill to Digital Invoice + Khata

A Telegram bot built on **n8n + Google Gemini Vision** that converts photos of handwritten/rough Indian shop bills (kaccha bills) into clean, verified digital invoices — with automatic bookkeeping (khata), a human confirmation loop, and a printable PDF invoice.

---

## ❓ The Problem

- Millions of small Indian shopkeepers (kirana stores, hardware shops, vegetable vendors) still write bills by hand on rough paper — the "kaccha bill".
- These bills are **easily lost, torn, or faded** — there is no permanent record of sales.
- **Manual math errors** are common: item amounts often don't add up to the written total, causing losses and customer disputes.
- Shopkeepers have **no daily/monthly sales visibility** — no idea what today's total was, how much credit (baki) is pending, or which items sell most.
- Existing billing software is **too complex, too English, and too expensive** for a small shopkeeper who just wants to click a photo.

## 💡 The Solution

- Shopkeeper sends **one photo** of the rough bill to a Telegram bot — that's the entire workflow for the user.
- **Gemini Vision AI** reads the handwriting and extracts structured data: shop name, customer name, date, bill number, line items (item, qty, rate, amount), discount (chhoot), advance paid (jama), and the stated total.
- **Deterministic math verification** (done in code, not by AI): every line is checked (qty × rate = amount) and the grand total is recomputed. Mismatches are flagged loudly — never silently "corrected".
- **Never guesses**: any unreadable value is marked "⚠ confirm" instead of inventing a number. A wrong number is worse than a flagged one.
- The bot replies in **Hinglish** (Hindi labels, English digits) — the language shopkeepers actually use.
- Every bill is **automatically logged to a Google Sheets khata** (digital ledger) with a live summary: today's sales, monthly sales, pending credit, mismatch count.
- A **confirmation loop**: every invoice comes with ✅ Sahi hai / ✏️ Galat hai buttons. The shopkeeper's response is written back to the khata, so records are human-verified, not just AI-generated.
- A **printable PDF invoice** is generated and sent along with every bill — ready to print or forward to the customer.

## ⚙️ How It Works (Flow)

```
Telegram photo → Download image → Gemini Vision (structured JSON)
       → Code node (parse + verify math + build Hinglish invoice)
       → Reply with invoice + ✅/✏️ buttons
       → Log row to Google Sheets khata (status: ⏳ Pending)
       → Generate PDF (APITemplate.io) → send PDF

Button tap → acknowledge tap → update khata row (✅ Confirmed / ✏️ Fix maanga)
       → edit original message with status (buttons removed)

Text/sticker → polite "please send a bill photo" reply
```

## ✨ Key Features

- **One-photo UX** — no app install, no forms, no training needed. If you can use WhatsApp, you can use this.
- **Math guardrails** — line-level `⚠ check` flags and total `⚠ MISMATCH` detection with the exact difference (farak) shown.
- **Honest AI** — unreadable values become "⚠ confirm"; non-bill or blurry photos are rejected with a clear message.
- **Discount / credit aware** — handles chhoot (discount), jama (advance paid), and baki (balance) written on the bill.
- **Digital khata** — 16-column Google Sheet with a self-updating Summary tab (today's sales, monthly sales, total credit outstanding, confirmation stats).
- **Human-in-the-loop** — inline confirm buttons close the loop between AI output and trusted records.
- **PDF invoices** — clean, printable A4 invoice with shop header, items table, totals, and verification status.

## 🏪 Why This Is Effective for Local Businesses

- **Zero behaviour change** — shopkeepers keep writing bills exactly as they always have; digitization happens from a photo, after the fact.
- **Catches real money leaks** — totalling mistakes on handwritten bills are found instantly (e.g. bill says ₹6,160 but items add up to ₹9,960 → flagged with farak ₹3,800).
- **Credit (udhaar) tracking** — jama/baki columns give a running record of who owes what, replacing memory and loose paper.
- **Business visibility for the first time** — daily and monthly sales totals appear automatically in the Summary sheet, with zero data entry.
- **Dispute protection** — a timestamped, confirmed, printable invoice exists for every sale.
- **Near-zero cost** — runs on n8n (self-hosted, free), Gemini free tier (~1,500 requests/day), Google Sheets (free), and APITemplate free tier (~50 PDFs/month). Total software cost to start: ₹0.
- **Hinglish-first** — outputs match how small Indian businesses actually read and write.

## 🧰 Tech Stack

- **n8n** (self-hosted) — workflow automation engine
- **Telegram Bot API** — user interface (trigger, replies, inline buttons, PDF delivery)
- **Google Gemini Vision** (`gemini-2.5-flash`) — handwriting OCR + structured extraction
- **JavaScript Code node** — deterministic math verification and invoice formatting (AI is never trusted with arithmetic)
- **Google Sheets** — khata (ledger) storage + live summary formulas
- **APITemplate.io** — HTML → PDF invoice generation

## 📁 Repository Structure

```
kaccha-bill-bot/
├── README.md                      ← you are here
├── workflows/
│   └── kaccha-bill-bot.json       ← complete n8n workflow (import this)
├── sheets/
│   ├── khata-Bills.csv            ← Bills tab template (16 columns)
│   └── khata-Summary.csv          ← Summary tab with live formulas
└── docs/
    ├── SETUP.md                   ← step-by-step setup guide
    └── TESTING.md                 ← 8-test verification checklist
```

## 🚀 Quick Start

1. Import `workflows/kaccha-bill-bot.json` into your n8n instance.
2. Create a Google Sheet and import both CSVs from `sheets/` (tab names must be **Bills** and **Summary**).
3. Add your credentials: Telegram bot token (from @BotFather), Google Gemini API key (aistudio.google.com/apikey), Google Sheets OAuth, and APITemplate.io API key.
4. Select your khata document + **Bills** sheet in both Google Sheets nodes.
5. Activate the workflow and send your bot a bill photo.

Full instructions: [docs/SETUP.md](docs/SETUP.md) · Test plan: [docs/TESTING.md](docs/TESTING.md)

> **Note:** All credentials in the workflow JSON are placeholders. Never commit real API keys to this repository.

## 🗺️ Roadmap

- [ ] WhatsApp Business API support (where Indian shopkeepers actually live)
- [ ] Shop onboarding via /start (name, GST no, phone → branded invoices)
- [ ] Nightly auto-summary message ("Aaj 12 bills, total ₹8,450")
- [ ] Hindi/Marathi output options
- [ ] Freemium model with UPI payments (Razorpay)

## 📄 License

MIT
