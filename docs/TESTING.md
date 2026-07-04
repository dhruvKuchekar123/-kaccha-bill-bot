# Testing Checklist

Run these 8 tests in order. Watch the **Executions** tab in n8n after each test — a red node shows exactly where something failed.

## Test 1 — Happy path

Send a clear bill photo containing: shop name, customer name, bill no, date, 3–4 items (qty × rate = amount), a discount (chhoot), an advance (jama), and a correct total.

Expected:
- Text invoice with all fields, Jod/Chhoot/Total/Jama/Baki lines, and "✅ Total sahi hai"
- ✅ Sahi hai / ✏️ Galat hai buttons under the invoice
- A PDF invoice file follows
- Google Sheet → Bills: new row with Confirm = ⏳ Pending
- Summary tab: today's sales updated

## Test 2 — Confirm button

Tap **✅ Sahi hai**.

Expected: popup "Confirm ho gaya ✅", status line appended to the invoice message, buttons removed, sheet row Confirm = "✅ Confirmed".

## Test 3 — Fix button

Send the same bill again, tap **✏️ Galat hai**.

Expected: "dobara photo bhejein" line appended, sheet row Confirm = "✏️ Fix maanga".

## Test 4 — Total mismatch

Write a bill where the stated total is deliberately wrong (e.g. ₹50 extra).

Expected: "⚠ MISMATCH ... (farak ₹50)" in the reply, Verify column = mismatch, red warning in the PDF.

## Test 5 — Unreadable value

Scribble one item's rate illegibly.

Expected: "⚠ confirm" in that position — never a guessed number.

## Test 6 — Non-bill photo

Send a selfie or random photo.

Expected: "Yeh photo saaf nahi hai ya bill nahi lag rahi" — no buttons, no PDF, no sheet row.

## Test 7 — Text message

Send "hello".

Expected: polite "photo bhejein 📸" reply.

## Test 8 — Real handwriting (most important)

Write a real bill with a pen in imperfect handwriting and photograph it. This is the true OCR test — any mistakes found here are the product's real weaknesses.

All 8 pass → the bot is demo-ready.
