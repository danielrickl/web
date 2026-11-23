---
name: web-outreach-demo
description: Uses 4 sub-agents to find local businesses with a poor website, write personalised e-mails, build demo landing pages based on a template and combine everything into ready-to-review packages. Works for any country and language. For finding web design clients.
---

# Web outreach + demo landing page (4 sub-agents)

For a freelance web designer: find potential clients with a weak website, write each of them a personal e-mail and prepare a tailored demo landing page based on the user's own template. The work is done by four sub-agents (the Agent tool); each has a single role and they hand data to each other through files.

**Never send or publish anything without the user's confirmation.**

## Inputs (ask only for what is missing)

- **Industry**: e.g. carpenters, plumbers, electricians, hair salons. Default: tradespeople and small local businesses.
- **Location**: country plus a region or list of cities, e.g. "Czech Republic: Ústí nad Labem, Děčín, Teplice" or "UK: Leeds, Bradford". There is no default; if the user's request does not say, ask.
- **Number of candidates**: default 5.
- **Outreach language**: the language the e-mails and demos are written in. Default: the main business language of the location (Czech for the Czech Republic, German for Germany, …). Where a country has several (e.g. Switzerland, Belgium), use the language of the prospect's own website.
- **Form of address**: default: the polite business form customary in that language (e.g. "vy" in Czech, "Sie" in German, "vous" in French; first name or "Hi" in English).
- **Template folder**: first look in the current project for a folder whose name contains `template`, `vzor` or `sablona` (up to depth 2) with an `index.html`; this is the user's own design. If there is none, use the `template/` folder next to this `SKILL.md` (the bundled sample) and tell the user. If neither exists, ask for the path. Without a template, run only agents 1 and 2.
- **Sender details** (brand, phone, e-mail, website, company registration number, postal address): from what the user provides or from their memory. Never invent missing details; leave `[FILL IN]` instead.

Reports for the user (overview, notes, chat) are in the language the user writes in. Everything the prospect will see (e-mail, demo) is in the outreach language, in a factual and human tone, not salesy.

## Step 0: Legal check (main thread, before the agents)

E-mail marketing rules depend on the **recipient's** country. Before starting, tell the user in two or three sentences which rules apply to the location and what they mean for this run. Use this rule of thumb and add any other country you know about; when unsure, say so:

| Jurisdiction | Unsolicited e-mail to businesses |
|--------------|----------------------------------|
| USA (CAN-SPAM) | Allowed with an honest subject, a clear sender, a postal address and a working opt-out. |
| UK (PECR) | Allowed to companies (corporate subscribers) with a clear sender and an opt-out; sole traders and partnerships are treated like individuals and need prior consent. |
| France | Generally allowed to a professional address if the offer is relevant to the recipient's profession, with an opt-out. |
| Germany (UWG § 7) | Prior express consent required, also for businesses. |
| Czech Republic (Act 480/2004 Coll., § 7) | Prior consent required for commercial e-mail, also to businesses (except existing customers). |
| Other EU countries | Check national law implementing the ePrivacy Directive; many require consent. GDPR applies to the data in all EU countries. |

Where consent is required, tell the user plainly that sending the e-mail without consent is likely unlawful, and offer to prepare the demo plus a **phone call script or a letter** instead (other channels have their own rules too). Agent 2 still writes the e-mail texts only if the user confirms they want them. This is not legal advice; remind the user to check with a lawyer.

## Working folder and data hand-off

Create `outreach/<YYYY-MM-DD>-<industry>-<city>/` with the subfolders `emails/`, `demos/` and `final/`. Agents do not share context, so in each prompt always pass: the path to the working folder, the path to the template, the sender details, the outreach language and form of address, the jurisdiction notes from Step 0 and the exact specification of their input and output below. Every prompt must be self-contained and understandable without knowledge of the conversation.

Shared candidate record (`prospects.json`, an array of objects):
```json
{
  "slug": "smith-joinery-leeds",
  "company": "",
  "city": "",
  "country": "",
  "url": "",
  "language": "language of their website, e.g. en",
  "contact": {"email": "", "phone": "", "contact_source": ""},
  "what_they_do": "1-3 sentence summary verified from their website",
  "issues": ["specific verified shortcoming", "..."],
  "score": 4,
  "verified_facts": ["services", "service area", "opening hours"],
  "unknown_data": ["prices", "references", "photos"]
}
```

## Procedure and order

0. **Legal check** (main thread, see above).
1. **Agent 1** (sequential; everything else waits for its output).
2. **Run agents 2 and 3 in parallel** in a single batch (they do not depend on each other; both read only `prospects.json`). With more than 3 candidates you may split agents 2 and 3 by candidate (one agent = one or two candidates) so they run faster.
3. **Agent 4** only after agents 2 and 3 have finished.
4. At the end, send the user the demo screenshots as previews and summarise the results (see After the agents finish).

Run sub-agents in the background and, once they finish, take their results from the files, not from their text replies.

## Agent 1: Researcher (finds websites and fills in the data)

**Input**: industry, location, number of candidates. **Output**: `prospects.json`.

The agent's task:
- WebSearch with combinations of `<industry> <city>` in the local language, plus local business directories and maps (e.g. Google Maps, Yelp, Yell, Gelbe Seiten, Firmy.cz). Look for about twice as many candidates as needed, then filter.
- Prefer businesses that **have** a website, but a weak one. Skip businesses that are only on social media, large companies and chains, and businesses with an obviously modern website.
- Load each website (WebFetch) and evaluate it. Checks: missing HTTPS or an expired certificate; not responsive (missing viewport, fixed width, tiny fonts; verify in the page source); outdated look (layout tables, frames, Flash, a year such as 2012 in the footer, etc.); no clear call to action (phone, enquiry form, contact above the fold); missing references, price list or scope of services, service area, opening hours; slow/heavy images, broken links, missing meta title/description.
- Score 1-5 (5 = worst website, best candidate). Select only score ≥ 4 with at least 3 specific, verifiable shortcomings.
- **Contact**: only a publicly listed business contact (their website, a business directory, the business register). Do not guess addresses and do not look for private e-mails. If there is no e-mail, leave it empty and fill in the phone. Note whether the business is a company or a sole trader when it is visible (this matters under some laws, e.g. in the UK).
- `what_they_do`: a summary of what the business does, based only on what is on their website. `unknown_data`: list what could not be found out.
- No unsupported claims: for every issue the agent must know where it saw it.

## Agent 2: Copywriter (writes the e-mails)

**Input**: `prospects.json`, sender details, outreach language, form of address, jurisdiction notes. **Output**: `emails/<slug>.md` for every candidate with an e-mail address.

Content of each file: a `Subject:` line, the e-mail body, and at the end the note `Demo link: {{DEMO_URL}}` (a placeholder that Agent 4 fills in). The subject and body are in the outreach language; the labels `Subject:` and `Demo link:` stay in English.

E-mail rules:
- Max ~120-150 words. Structure: 1) one sentence on what you noticed and appreciated about their work/business (verifiable facts only); 2) 2-3 specific observations about the website from the `issues` field, kindly, without belittling; 3) a sentence like "I have put together a no-obligation demo of how it could look: {{DEMO_URL}}"; 4) one clear question/call to action (a short call or a reply by e-mail); 5) signature.
- A specific, non-exaggerated subject, e.g. "An idea for <company>'s website – I made you a demo".
- Write natively in the outreach language, as a local would; do not translate English idioms literally.
- No invented statistics, no false urgency, no "you will get X customers".
- **Legal minimum** (stricter local rules from Step 0 take precedence): a clearly identified sender (brand, registration number, postal address), an obvious commercial communication, and an easy way to opt out (e.g. "If you are not interested, just reply 'no' and I will not contact you again."). No bulk mailing; every e-mail is individual and the first contact is one-off.
- Skip candidates without an e-mail address and write them to `emails/_no-email.md` (names and phone only).

## Agent 3: Landing page builder (builds a tailored demo)

**Input**: `prospects.json`, path to the template, outreach language. **Output**: `demos/<slug>/index.html` (and related files).

The agent's task:
- Read the whole template (HTML/CSS/JS, sections, style, fonts) and stick to its design and conventions. If it contains placeholders (`{{COMPANY}}`, `[NAME]`, etc.), fill them in; otherwise rewrite the texts within the same structure. Translate any fixed texts of the template (navigation, headings, FAQ) into the outreach language and set `<html lang="…">` accordingly.
- Copy the template into `demos/<slug>/` (**never edit the template directly**).
- Content only from `what_they_do` and `verified_facts`: name, services, service area, contacts, opening hours. Whatever is in `unknown_data` (prices, references, reviews, photos, certifications, years of experience) **must not be invented**: use a clearly marked placeholder or leave the section out.
- No invented reviews, client logos, project counts or other claims presented as facts. Do not copy third-party photos or logos; use the neutral placeholder images from the template.
- Use local formats: phone numbers, addresses, opening hours, dates and currency as customary in the country.
- SEO and performance basics: title, description, semantic headings, lazy loading, responsiveness, contrast, a visible call/enquiry button, working `tel:` and `mailto:` links.
- **Demo label**: in the footer or a discreet banner, in the outreach language: "Demo design created by <user's brand>; this is not the official website of <company>", and `<meta name="robots" content="noindex,nofollow">`. Never present the demo as the business's real website.
- Check the result (valid HTML, mobile view, working links). If a browser is available, open it and check it visually.
- Next to `index.html`, save `demos/<slug>/_data-used.md`: what is verified and what is a placeholder.

## Agent 4: Assembler (links the e-mail with the landing page)

**Input**: `prospects.json`, `emails/`, `demos/`. **Output**: `final/<slug>/` + `final/_overview.md`.

The agent's task, for every candidate that has a demo:
- **Consistency check**: facts in the e-mail (services, website shortcomings, company name) match what the demo actually addresses; the demo contains the demo label and noindex; the e-mail promises nothing the demo does not have; the e-mail and the demo are in the same language; no unfilled placeholders or invented data are left in the demo. Fix discrepancies in the e-mail or the demo and record them in the overview.
- **Linking**: if a URL for the demo exists (see below), substitute it for `{{DEMO_URL}}` in the e-mail. If not, rewrite the sentence to say the demo is attached and create a ZIP `final/<slug>/demo-<slug>.zip` with the demo.
- Save the result as `final/<slug>/email.md` (final text, if there is an e-mail) and `final/<slug>/demo/` (a copy of the demo).
- **Demo screenshots**: for every demo create `final/<slug>/preview-desktop.png` (width 1440 px, full page) and `final/<slug>/preview-mobile.png` (width 390 px, full page). Use headless Chromium (Playwright in Python or Node; if it is missing, install `playwright` and run `playwright install chromium`) and open `file://.../index.html`. After taking them, look at the screenshots (Read) and verify that the whole page rendered, without a broken layout, overflow or empty areas; fix any errors in the demo and retake the screenshots. If a screenshot cannot be taken, note it in `_overview.md` and continue without it.
- `final/_overview.md` (in the user's language): a table with company, city, score, main shortcomings, contact, path to the e-mail, path to the demo, what is verified and what is a placeholder, and what the user must fill in or check before contacting them (including the legal note from Step 0).

Agent 4 itself **sends nothing, deploys nothing and does not create e-mail drafts**. The main thread handles those steps only after confirmation.

## After the agents finish (main thread)

- **Send the screenshots as previews**: for every candidate send `preview-desktop.png` and `preview-mobile.png` with the SendUserFile tool (`display: "render"`, `status: "normal"`, a caption with the company name and city, e.g. "Demo: Smith Joinery, Leeds (desktop)"). Send them progressively per candidate, not all at the end. Send the files as individual images, not as a ZIP.
- Give the user a short overview (table) from `final/_overview.md`, pointing out what still needs to be filled in (`[FILL IN]`) and repeating the legal note in one line.
- Offer (do nothing without consent): a) deploying the demos as previews (e.g. Vercel preview, not production) and inserting the links into the e-mails; b) e-mail drafts (e.g. Gmail `create_draft`), **never sending**; c) logging to a spreadsheet to track outreach (status, date, reply).
- When a candidate replies "no" or "do not write", record it and never contact them again.

## Rules

- Nothing is sent or published without the user's confirmation.
- The recipient country's rules on commercial e-mail take precedence over this skill.
- Every claim about a candidate's website must have a verifiable source.
- Do not overdo the criticism; the goal is to offer help, not to shame.
- Personal data: only public business contacts and the minimum of data (name, e-mail, website, notes on the website).
