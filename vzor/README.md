# Vzor landing page

Vzor pro skill `web-outreach-demo` (Agent 3 podle něj staví demo na míru).

- `index.html` – samostatná statická stránka (bez buildu, otevři v prohlížeči)
- `landing.css` – styly (vše pod `.lp`, světlá paleta v proměnných `--lp-*`)

Zdroj: landing page projektu `danielrickl/autoskolasoftware` (`src/pages/LandingPage.tsx` + `landing.css`), převedená z Reactu na čisté HTML s malým inline JS (mobilní menu, kontaktní formulář přes `mailto:`).

## Struktura stránky (sekce k přepsání pro klienta)

1. Hlavička s navigací a tlačítkem „Domluvit ukázku“
2. Hero: eyebrow, nadpis, úvodní odstavec, 2 tlačítka + ukázkový panel (kalendář)
3. „Dnes / S aplikací“ – srovnání v řádcích
4. Funkce 01–03 (text + ukázkový panel) a mřížka dalších funkcí
5. Ceník (3 plány, prostřední zvýrazněný)
6. Časté otázky (`<details>`)
7. Kontakt: formulář + telefon
8. Patička

Pro řemeslníky se mění obsah a ukázkové panely (místo kalendáře např. služby, reference, oblast působnosti), struktura, typografie a barvy se drží. Barvy lze změnit jen přepsáním proměnných `--lp-*` na začátku `landing.css`.
