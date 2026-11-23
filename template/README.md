# Template folder (`template/`)

This folder holds the landing page template that Agent 3 of the `web-outreach-demo` skill uses to build a tailored demo for each prospect. It is currently **empty**: add your own template before running the full pipeline. Without a template the skill runs only the Researcher and the Copywriter (agents 1 and 2) and no demos are created.

The skill finds the template by looking for a folder whose name contains `vzor`, `template` or `sablona` (up to two levels deep). If you keep it elsewhere, give the skill the path when you start a run.

## What to put here

A static website that opens directly in a browser, without a build step:

- `index.html` – the page itself (required)
- CSS, JS, fonts and images it references, using **relative paths** (e.g. `landing.css`, `img/hero.jpg`)
- Optionally this `README.md`, describing the sections and conventions for Agent 3

Agent 3 copies the whole folder to `outreach/<run>/demos/<slug>/` and edits only the copy. The template itself is never modified.

## Recommended conventions

These are not required, but they make the generated demos more consistent:

1. **Placeholders** – mark the texts to replace, e.g. `{{COMPANY}}`, `{{CITY}}`, `{{PHONE}}`, `{{EMAIL}}`, `{{SERVICES}}`. Agent 3 fills them in; without placeholders it rewrites the texts within the existing structure.
2. **Clearly separated sections** – e.g. header with navigation and a call button, hero (heading, intro, call to action), services, service area, references, prices, FAQ, contact, footer. Sections whose data is unknown (prices, references, photos) are left out or shown with a clearly marked placeholder, so make them easy to remove.
3. **Colours in CSS variables** – define the palette in one place (e.g. `:root { --brand: …; }`) so the demo can be adapted to the client's colours by changing a few lines.
4. **Neutral placeholder images** – only images you own or that are free to use. Agent 3 never copies the prospect's photos or logos.
5. **Mobile first** – a `viewport` meta tag and a responsive layout; the demos are screenshotted at 1440 px and 390 px widths.
6. **Working contact links** – `tel:` and `mailto:` links and a form that needs no back end (e.g. `mailto:`), so the demo works as a single static folder.
7. **Space for the demo label** – a footer or banner where Agent 3 adds "this is a demo, not the official website of <company>". Agent 3 also adds `<meta name="robots" content="noindex,nofollow">`.
8. **Language** – the demos are in Czech for Czech businesses; write the template in Czech or keep its texts easy to replace.

## Checklist before a run

- [ ] `template/index.html` exists and opens correctly in a browser from the file system
- [ ] all referenced files are inside `template/` and use relative paths
- [ ] no third-party content (photos, logos, texts) you do not have the rights to
- [ ] the layout works at 390 px width without horizontal scrolling
