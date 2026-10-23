# Value Chain Quiz (SanYati)

A self-guided quiz page. The visitor picks one moment from their life (a high point or a low point) and answers one question at a time until they reach the value underneath it. At the end they give their name, email and WhatsApp number, see their result, and get the option to book a 45-minute 1:1 Value Clarity Call for ₹499.

## Files

| File | What it is |
|---|---|
| `value-chain.html` | The quiz page. Upload this to your website. |
| `value-chain-sheet-endpoint.gs` | Google Apps Script. Saves every sign-up as a row in your Google Sheet. |
| `README.md` | This file. |

## How the quiz flows

1. **Level 1: The Moment.** Pick a high point or a low point and describe it in a line.
   - High point: "What was important to you about that moment?"
   - Low point: "What did that moment make you decide you never want again?", then "So what do you want instead?"
2. **Level 2: The Dig.** Two questions take turns: "What does that give you?" and "What's important to you about that?" Each answer becomes the next question. It stops when the person taps "Nothing deeper comes up", or when an answer repeats an earlier one and they confirm it's the root.
3. **Level 3: The Picture.** They imagine a life with all their values and write a few lines about it.
4. **Level 4: The Guard.** A 30-second pause, then "Is any part of you saying 'yes, but...'?"
   - No: they move on to the result.
   - Yes: they write what came up, then answer "What is this part trying to get for you?". That answer gets its own dig (back to Level 2), then they add to the picture and check again. This can repeat.
5. **Sign-up form.** First name, email, WhatsApp, and a consent tickbox. All four are required.
6. **Result page.** It shows:
   - their core value, with a result code like `LOW-D4-G1-FREEDOM` (type of moment, how deep they dug, how many "but" rounds, the core value)
   - their chain of answers and their picture
   - the low-point check ("I must have it" or "I get to have it"), shown only if they picked a low point
   - the guard: what pushed back and what it wants for them
   - a "lock in one action for today" box
   - a hidden insight they tap to reveal
   - a Value Profile that collects up to 3 moments
   - the program levels and the booking button
   - copy result and share-with-a-friend buttons

### Automatic nudges during the quiz

These are suggestions only. The person can always keep their answer.

| When | What the page says |
|---|---|
| An answer contains "not", "never", "don't", "nahi" and similar words | Asks them to say what they want instead |
| An answer matches an earlier value | Asks "Is that the root?" |
| A "but" answer blames a boss, money, time, family or similar | Suggests asking again, more slowly |

These work by spotting words, so they'll sometimes fire when they shouldn't.

## Setup

### Step 1: Connect the Google Sheet

1. Open the Google Sheet you want leads in. Your Stuck Code sheet is fine, because this script writes to its own tab called **Value Chain**.
2. Go to **Extensions > Apps Script**. Delete what's there, paste in all of `value-chain-sheet-endpoint.gs`, and save.
3. Go to **Deploy > New deployment**, choose the type **Web app**, and set:
   - Execute as: **Me**
   - Who has access: **Anyone**. Not "Anyone in sanyati.in", or website visitors won't be able to save.
4. Allow access when Google asks. Copy the **Web app URL** (it ends in `/exec`).
5. Check it works by opening that URL in a browser. You should see `Value Chain endpoint is running.`

### Step 2: Edit the settings at the top of the page's script

Open `value-chain.html` in any text editor and look for the `config` section near the top of the `<script>` part.

```js
const L2_PRICE = "₹499";                                    // price shown on the result page
const PAY_LINK = "https://example.com/book-value-clarity-call";  // your real booking or payment link
const ENDPOINT = "";                                             // the /exec URL from Step 1
```

- **ENDPOINT:** paste the `/exec` URL between the quotes. If you leave it empty, the form still shows but nothing is saved.
- **PAY_LINK:** replace the placeholder with your Razorpay, TagMango or Calendly link. **Until you do, the booking button goes to a page that doesn't exist.**
- **L2_PRICE:** change it if the price changes. `₹` is the ₹ symbol.

### Step 3: Upload the page

Upload `value-chain.html` to your website, next to Stuck Code.

You can add `?src=something` to the link to track where people came from. For example, `value-chain.html?src=instagram` saves "instagram" in the `source` column.

## What gets saved in the Sheet

Each sign-up adds one row to the **Value Chain** tab.

| Column | What it holds |
|---|---|
| ts | Date and time (UTC) |
| name, email, phone | Contact details |
| consent | Always "yes" (the form can't be sent without the tickbox) |
| kind | "high point" or "low point" |
| story | The moment they described |
| never_again | The "never again" answer (low points only) |
| first_value | Their first answer |
| chain | The full chain, e.g. `to be heard > respect > confidence > freedom` |
| core_value | Where the chain ended |
| depth | How many answers in the chain |
| guard_rounds | How many times a "but" came up |
| guard_said | What each "but" said (separated by `\|`) |
| guard_wants | What each "but" wants for them |
| guard_values | The chain found under each "but" |
| vision | Their picture |
| code | The result code |
| badges | Badges collected, out of 8 |
| source | The `?src=` value, if any |

If someone explores a second moment, it saves a new row without asking for their details again.

## Things to know

- **Saving only works on your own website.** The preview on claude.ai can't send data to outside sites, so the form there saves nothing.
- **If you edit the Apps Script later,** you have to publish it again or the old version keeps running. Go to **Deploy > Manage deployments**, click edit, and choose **Version: New version**. The `/exec` URL stays the same.
- **Answers are kept in the visitor's browser** so they can pick up where they left off. Clearing browser data, or using a private window, starts them from the beginning.
- **The page protects your Sheet:** answers that start with `=`, `+`, `-` or `@` are saved as plain text rather than run as formulas, and very long answers are cut off at 5,000 characters.
- **Your two quizzes don't match yet.** Stuck Code's Level 2 is a ₹1,499 call; this one's is ₹499, and the levels after that are different too. Line them up before both go live.

## Troubleshooting

| Problem | Likely cause | What to do |
|---|---|---|
| No rows appear in the Sheet | ENDPOINT is empty, or you're testing the claude.ai preview | Paste the `/exec` URL into ENDPOINT and test on your website |
| Still no rows | "Who has access" isn't set to **Anyone** | Change it under **Deploy > Manage deployments** |
| You changed the script but nothing changed | The old version is still published | Deploy a **New version** |
| The booking button goes to example.com | PAY_LINK is still the placeholder | Paste your real link |
| Fonts look plain | Google Fonts didn't load | Nothing breaks; the page falls back to system fonts |
