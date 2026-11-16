# web-outreach-demo

Claude skill, který pomocí čtyř sub-agentů najde firmy se špatným webem, napíše jim personalizovaný e-mail a vytvoří demo landing page podle vlastního vzoru.

## Jak to funguje

1. **Průzkumník** najde weby a vyplní kontakt, problémy webu, souhrn činnosti a odkaz (`prospects.json`).
2. **Copywriter** napíše e-maily (`emails/`).
3. **Tvůrce landing pages** postaví demo podle vzoru (`demos/`).
4. **Spojovač** zkontroluje konzistenci, propojí e-mail s demem a připraví hotové balíčky (`final/`).

Agenti 2 a 3 běží paralelně. Agent 4 pořídí screenshoty dem (desktop + mobil) a hlavní vlákno je pošle do náhledu. Nic se neodesílá ani nezveřejňuje bez potvrzení.

## Struktura

- `SKILL.md` – definice skillu
- `vzor/` – sem patří vzorová landing page (HTML/CSS/JS)
- `outreach/` – pracovní výstupy jednotlivých běhů (v `.gitignore`, obsahují kontakty)

## Použití

Zkopíruj `SKILL.md` jako skill do Claude (název `web-outreach-demo`), do `vzor/` vlož svůj vzor a zadej např. „najdi mi 5 klientů se špatným webem, truhláři Teplice“.

## Právní poznámka

E-maily jsou obchodní sdělení podle zákona č. 480/2004 Sb. Posílej jen individuálně na veřejné firemní kontakty, uváděj odesílatele a umožni snadné odmítnutí. Nenahrazuje právní poradenství.
