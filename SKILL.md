---
name: web-outreach-demo
description: Uses 4 sub-agents to find businesses with a poor website, write personalised e-mails, build demo landing pages based on a template and combine everything into ready-to-send packages. For finding web design clients.
---

# Web outreach + demo landing page (4 sub-agents)

For a freelance web designer: find potential clients with a weak website, write each of them a personal e-mail and prepare a tailored demo landing page based on the user's own template. The work is done by four sub-agents (the Agent tool); each has a single role and they hand data to each other through files.

**Language of the outputs**: the prospects are Czech businesses, so everything they will see (e-mails, demo pages) is written in **Czech**, using the formal "vy" form, in a factual and human tone (not salesy). Notes and reports for the user may be in the user's language.

**Never send or publish anything without the user's confirmation.**

## Inputs (ask only for what is missing)

- **Industry and location**: default is tradespeople and small businesses in the Ústí nad Labem Region (Ústí nad Labem, Děčín, Teplice, Chomutov, Most, Litoměřice).
- **Number of candidates**: default 5.
- **Template folder**: look for a folder whose name contains `vzor`, `template` or `sablona` (up to depth 2). If you cannot find it or it is empty, ask for the path. Without a template, run only agents 1 and 2.
- **Sender details** (brand, phone, e-mail, website, company ID (IČO), address): from what the user provides or from their memory. Never invent missing details; leave `[FILL IN]` instead.

## Working folder and data hand-off

Create `outreach/<YYYY-MM-DD>-<industry>-<city>/` with the subfolders `emails/`, `demos/` and `final/`. Agents do not share context, so in each prompt always pass: the path to the working folder, the path to the template, the sender details and the exact specification of their input and output below. Every prompt must be self-contained and understandable without knowledge of the conversation.

Shared candidate record (`prospects.json`, an array of objects):
```json
{
  "slug": "instalater-novak-teplice",
  "company": "",
  "city": "",
  "url": "",
  "contact": {"email": "", "phone": "", "contact_source": ""},
  "what_they_do": "1-3 sentence summary verified from their website",
  "issues": ["specific verified shortcoming", "..."],
  "score": 4,
  "verified_facts": ["services", "service area", "opening hours"],
  "unknown_data": ["prices", "references", "photos"]
}
```

## Procedure and order

1. **Agent 1** (sequential; everything else waits for its output).
2. **Run agents 2 and 3 in parallel** in a single batch (they do not depend on each other; both read only `prospects.json`). With more than 3 candidates you may split agents 2 and 3 by candidate (one agent = one or two candidates) so they run faster.
3. **Agent 4** only after agents 2 and 3 have finished.
4. At the end, send the user the demo screenshots as previews and summarise the results (see After the agents finish).

Run sub-agents in the background and, once they finish, take their results from the files, not from their text replies.

## Agent 1: Researcher (finds websites and fills in the data)

**Input**: industry, location, number of candidates. **Output**: `prospects.json`.

The agent's task:
- WebSearch with combinations of `<industry> <city>` (also Firmy.cz, Mapy.cz). Look for about twice as many candidates as needed, then filter.
- Prefer businesses that **have** a website, but a weak one. Skip businesses that are only on Facebook, large companies and chains, and businesses with an obviously modern website.
- Load each website (WebFetch) and evaluate it. Checks: missing HTTPS or an expired certificate; not responsive (missing viewport, fixed width, tiny fonts; verify in the page source); outdated look (layout tables, frames, Flash, a year such as 2012 in the footer, etc.); no clear call to action (phone, enquiry form, contact above the fold); missing references, price list or scope of services, service area, opening hours; slow/heavy images, broken links, missing meta title/description.
- Score 1-5 (5 = worst website, best candidate). Select only score ≥ 4 with at least 3 specific, verifiable shortcomings.
- **Contact**: only a publicly listed business contact (website, Firmy.cz, business register). Do not guess addresses and do not look for private e-mails. If there is no e-mail, leave it empty and fill in the phone.
- `what_they_do`: a summary of what the business does, based only on what is on their website. `unknown_data`: list what could not be found out.
- No unsupported claims: for every issue the agent must know where it saw it.

## Agent 2: Copywriter (writes the e-mails)

**Input**: `prospects.json`, sender details. **Output**: `emails/<slug>.md` for every candidate with an e-mail address.

Content of each file: a `Předmět:` (Subject) line, the e-mail body, and at the end the note `Odkaz na demo: {{DEMO_URL}}` (a placeholder that Agent 4 fills in). The e-mail itself is in Czech.

E-mail rules:
- Max ~120-150 words. Structure: 1) one sentence on what you noticed and appreciated about their work/business (verifiable facts only); 2) 2-3 specific observations about the website from the `issues` field, kindly, without belittling; 3) "Připravil jsem si nezávaznou ukázku, jak by to mohlo vypadat: {{DEMO_URL}}" ("I have prepared a no-obligation demo of how it could look"); 4) one clear question/call to action (a short call or a reply by e-mail); 5) signature.
- A specific, non-exaggerated subject, e.g. "Nápad na web pro <company> - připravil jsem ukázku" ("An idea for <company>'s website - I have prepared a demo").
- No invented statistics, no false urgency, no "you will get X customers".
- **Legal minimum** (Czech Act No. 480/2004 Coll. on certain information society services): a clearly identified sender (brand, company ID, address), an obvious commercial communication, and an easy way to opt out (e.g. "Pokud nemáte zájem, stačí odpovědět 'ne' a už vás nebudu kontaktovat." – "If you are not interested, just reply 'no' and I will not contact you again."). No bulk mailing; every e-mail is individual and the first contact is one-off. Remind the user that this does not replace a lawyer.
- Skip candidates without an e-mail address and write them to `emails/_no-email.md` (names and phone only).

## Agent 3: Landing page builder (builds a tailored demo)

**Input**: `prospects.json`, path to the template. **Output**: `demos/<slug>/index.html` (and related files).

The agent's task:
- Read the whole template (HTML/CSS/JS, sections, style, fonts) and stick to its design and conventions. If it contains placeholders (`{{COMPANY}}`, `[NAME]`, etc.), fill them in; otherwise rewrite the texts within the same structure.
- Copy the template into `demos/<slug>/` (**never edit the template directly**).
- Content only from `what_they_do` and `verified_facts`: name, services, service area, contacts, opening hours. Whatever is in `unknown_data` (prices, references, reviews, photos, certifications, years of experience) **must not be invented**: use a clearly marked placeholder or leave the section out.
- No invented reviews, client logos, project counts or other claims presented as facts. Do not copy third-party photos or logos; use the neutral placeholder images from the template.
- SEO and performance basics: title, description, semantic headings, lazy loading, responsiveness, contrast, a visible call/enquiry button, working `tel:` and `mailto:` links.
- **Demo label**: in the footer or a discreet banner, "Ukázkový návrh vytvořil <user's brand>, nejedná se o oficiální web firmy <company>" ("Demo design created by <user's brand>; this is not the official website of <company>") and `<meta name="robots" content="noindex,nofollow">`. Never present the demo as the business's real website.
- Check the result (valid HTML, mobile view, working links). If a browser is available, open it and check it visually.
- Next to `index.html`, save `demos/<slug>/_data-used.md`: what is verified and what is a placeholder.

## Agent 4: Assembler (links the e-mail with the landing page)

**Input**: `prospects.json`, `emails/`, `demos/`. **Output**: `final/<slug>/` + `final/_overview.md`.

The agent's task, for every candidate that has both an e-mail and a demo:
- **Consistency check**: facts in the e-mail (services, website shortcomings, company name) match what the demo actually addresses; the demo contains the demo label and noindex; the e-mail promises nothing the demo does not have; no unfilled placeholders or invented data are left in the demo. Fix discrepancies in the e-mail or the demo and record them in the overview.
- **Linking**: if a URL for the demo exists (see below), substitute it for `{{DEMO_URL}}` in the e-mail. If not, rewrite the sentence to "ukázku posílám v příloze" ("I am attaching the demo") and create a ZIP `final/<slug>/demo-<slug>.zip` with the demo.
- Save the result as `final/<slug>/email.md` (final text) and `final/<slug>/demo/` (a copy of the demo).
- **Demo screenshots**: for every demo create `final/<slug>/preview-desktop.png` (width 1440 px, full page) and `final/<slug>/preview-mobile.png` (width 390 px, full page). Use headless Chromium (Playwright in Python or Node; if it is missing, install `playwright` and run `playwright install chromium`) and open `file://.../index.html`. After taking them, look at the screenshots (Read) and verify that the whole page rendered, without a broken layout, overflow or empty areas; fix any errors in the demo and retake the screenshots. If a screenshot cannot be taken, note it in `_overview.md` and continue without it.
- `final/_overview.md`: a table with company, city, score, main shortcomings, contact, path to the e-mail, path to the demo, what is verified and what is a placeholder, and what the user must fill in before sending.

Agent 4 itself **sends nothing, deploys nothing and does not create Gmail drafts**. The main thread handles those steps only after confirmation.

## After the agents finish (main thread)

- **Send the screenshots as previews**: for every candidate send `preview-desktop.png` and `preview-mobile.png` with the SendUserFile tool (`display: "render"`, `status: "normal"`, a caption with the company name and city, e.g. "Demo: Instalatér Novák, Teplice (desktop)"). Send them progressively per candidate, not all at the end. Send the files as individual images, not as a ZIP.
- Give the user a short overview (table) from `final/_overview.md`, pointing out what still needs to be filled in (`[FILL IN]`).
- Offer (do nothing without consent): a) deploying the demos to Vercel as a preview (not production) and inserting the links into the e-mails; b) Gmail drafts of the e-mails (`create_draft`), **never `send_message`**; c) logging to Google Sheets to track outreach (status, date, reply).
- When a candidate replies "no" or "do not write", record it and never contact them again.

## Rules

- Nothing is sent or published without the user's confirmation.
- Every claim about a candidate's website must have a verifiable source.
- Do not overdo the criticism; the goal is to offer help, not to shame.
- Personal data: only public business contacts and the minimum of data (name, e-mail, website, notes on the website).
