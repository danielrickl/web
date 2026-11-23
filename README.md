# web-outreach-demo

**Stop pitching. Start showing.**

Cold e-mails saying "your website could be better" get ignored. An e-mail with
the prospect's own new website gets a reply.

This Claude skill runs a 4-agent pipeline that does the boring part of finding
clients for you:

- 🔍 **Finds** local businesses whose websites are stuck in 2012
- ✍️ **Writes** a short, personal e-mail for each, based on verified facts only
- 🎨 **Builds** a demo landing page in your own design
- 📦 **Packages** everything, with desktop and mobile screenshots, for you to review

You stay in control: nothing is sent or published until you say so.

## About

A Claude skill that uses four sub-agents to find businesses with a poor website, write each of them a personalised e-mail and build a demo landing page based on your own template. It is meant for freelance web designers looking for clients.

The prospects are Czech businesses (by default tradespeople and small businesses in the Ústí nad Labem Region), so the generated e-mails and demo pages are in Czech. This documentation and the skill definition are in English.

## How it works

| # | Agent | Input | Output |
|---|-------|-------|--------|
| 1 | **Researcher** – finds websites, evaluates them, fills in contact, website issues, a summary of the business and the link | industry, location, number of candidates | `prospects.json` |
| 2 | **Copywriter** – writes a short, personal e-mail for every candidate with an e-mail address | `prospects.json`, sender details | `emails/<slug>.md` |
| 3 | **Landing page builder** – copies the template and fills it with the candidate's verified data | `prospects.json`, template | `demos/<slug>/` |
| 4 | **Assembler** – checks consistency, links the e-mail with the demo, takes screenshots and prepares final packages | everything above | `final/<slug>/`, `final/_overview.md` |

Agent 1 runs first. Agents 2 and 3 run in parallel. Agent 4 runs last, takes desktop (1440 px) and mobile (390 px) screenshots of every demo, and the main thread then shows them to you as previews together with a summary table.

**Nothing is sent or published without your confirmation.** After a run, the skill only offers the next steps: deploying the demos to Vercel as previews, creating Gmail drafts (never sending), or logging the outreach in Google Sheets.

## Repository structure

```
.
├── SKILL.md        # skill definition (instructions for Claude)
├── README.md       # this documentation
├── LICENSE         # MIT
├── template/       # sample landing page template – replace with your own design
│   ├── index.html
│   ├── style.css
│   └── README.md
└── outreach/       # outputs of individual runs (git-ignored, they contain contacts)
    └── .gitkeep
```

## Installation

1. Add `SKILL.md` to Claude as a skill named `web-outreach-demo` (in Claude Code, e.g. copy it to `.claude/skills/web-outreach-demo/SKILL.md` in your project or to `~/.claude/skills/web-outreach-demo/SKILL.md`).
2. Use the included sample template, or replace it with your own design in `template/` – see [`template/README.md`](template/README.md) for requirements and recommended conventions.
3. Make sure Claude has access to web search and web fetch (Agent 1) and, for screenshots, to Playwright with Chromium (Agent 4 installs it if missing).

## Usage

Ask Claude for example:

> Find me 5 clients with a poor website, carpenters in Teplice.

or in Czech:

> Najdi mi 5 klientů se špatným webem, truhláři Teplice.

### Inputs

The skill asks only for what is missing:

| Input | Default |
|-------|---------|
| Industry and location | tradespeople and small businesses in Ústí nad Labem, Děčín, Teplice, Chomutov, Most, Litoměřice |
| Number of candidates | 5 |
| Template folder | first folder containing `vzor`, `template` or `sablona` (depth ≤ 2) |
| Sender details | brand, phone, e-mail, website, company ID (IČO), address – missing items are left as `[FILL IN]`, never invented |

### Outputs of a run

Each run creates `outreach/<YYYY-MM-DD>-<industry>-<city>/`:

```
outreach/2026-11-23-carpenters-teplice/
├── prospects.json             # candidates (Agent 1)
├── emails/
│   ├── <slug>.md              # e-mail drafts with {{DEMO_URL}} (Agent 2)
│   └── _no-email.md           # candidates with a phone number only
├── demos/
│   └── <slug>/
│       ├── index.html         # tailored demo (Agent 3)
│       └── _data-used.md      # what is verified and what is a placeholder
└── final/
    ├── _overview.md           # summary table and what you still need to fill in
    └── <slug>/
        ├── email.md           # final e-mail text
        ├── demo/              # copy of the demo
        ├── demo-<slug>.zip    # only if there is no URL for the demo
        ├── preview-desktop.png
        └── preview-mobile.png
```

### Candidate record (`prospects.json`)

```json
{
  "slug": "plumber-novak-teplice",
  "company": "Instalatér Novák",
  "city": "Teplice",
  "url": "http://example.cz",
  "contact": {"email": "", "phone": "", "contact_source": "website / Firmy.cz / business register"},
  "what_they_do": "1-3 sentence summary verified from their website",
  "issues": ["no HTTPS", "not responsive", "no contact above the fold"],
  "score": 4,
  "verified_facts": ["services", "service area", "opening hours"],
  "unknown_data": ["prices", "references", "photos"]
}
```

`score` is 1–5 (5 = worst website, best candidate). Only candidates with a score of 4 or more and at least three specific, verifiable issues are selected.

## Principles

- **Verified facts only** – every claim about a prospect's website has a source; unknown data (prices, references, reviews, photos) is never invented.
- **Kind tone** – the goal is to offer help, not to shame. E-mails are around 120–150 words, without invented statistics or false urgency.
- **Demos are clearly labelled** – each demo says it is a design proposal, not the business's official website, and carries `noindex,nofollow`.
- **Minimal personal data** – only publicly listed business contacts.
- **Opt-outs are respected** – anyone who replies "no" is recorded and not contacted again.

## Legal note

The e-mails are commercial communications under Czech Act No. 480/2004 Coll. on certain information society services. Send them only individually to public business contacts, clearly identify the sender (brand, company ID, address) and provide an easy way to opt out. The data processing is also subject to the GDPR. This is not legal advice.

## Troubleshooting

- **No demos are created** – the template folder is missing or has no `index.html`; add `template/index.html` or give the skill the template path.
- **No screenshots** – Playwright/Chromium could not be installed or started; the reason is noted in `final/_overview.md` and the demos can be opened manually from `final/<slug>/demo/index.html`.
- **A candidate has no e-mail** – it is listed in `emails/_no-email.md` with a phone number for a call instead.
- **`[FILL IN]` in the outputs** – a sender detail was not provided; fill it in before sending.

## License

[MIT](LICENSE) – free to use, modify and share, including commercially.
