---
name: web-outreach-demo
description: Pomocí 4 sub-agentů najde firmy se špatným webem, napíše personalizované e-maily, vytvoří demo landing pages podle vzoru a spojí je do hotových balíčků. Pro hledání klientů na tvorbu webu.
---

# Web outreach + demo landing page (4 sub-agenti)

Pro freelance webdesignéra: najdi potenciální klienty se slabým webem, napiš jim osobní e-mail a připrav uživateli na míru ukázkovou landing page podle jeho vzoru. Práci dělají čtyři sub-agenti (nástroj Agent), každý má jednu roli a předávají si data přes soubory.

Vše píšeš česky, vykáš, tón je věcný a lidský (ne reklamní). **Nikdy nic neodesílej a nezveřejňuj bez potvrzení uživatele.**

## Vstupy (zeptej se jen na to, co chybí)

- **Obor a lokalita**: výchozí řemeslníci a malé firmy v Ústeckém kraji (Ústí nad Labem, Děčín, Teplice, Chomutov, Most, Litoměřice).
- **Počet kandidátů**: výchozí 5.
- **Složka se vzorem**: hledej složku s názvem obsahujícím `vzor`, `template` nebo `sablona` (do hloubky 2). Pokud ji nenajdeš nebo je prázdná, zeptej se na cestu. Bez vzoru spusť jen agenty 1 a 2.
- **Údaje odesílatele** (značka, telefon, e-mail, web, IČO, adresa): z toho, co uživatel zadá, nebo z jeho paměti. Chybějící údaje nevymýšlej, nech `[DOPLNIT]`.

## Pracovní složka a předávání dat

Vytvoř `outreach/<YYYY-MM-DD>-<obor>-<mesto>/` s podsložkami `emails/`, `demos/`, `final/`. Agenti spolu nesdílí kontext, proto jim v promptu vždy předej: cestu k pracovní složce, cestu ke vzoru, údaje odesílatele a přesnou specifikaci jejich vstupu a výstupu níže. Každý prompt musí být samostatný a srozumitelný bez znalosti konverzace.

Sdílený záznam kandidáta (`prospects.json`, pole objektů):
```json
{
  "slug": "instalater-novak-teplice",
  "firma": "",
  "mesto": "",
  "url": "",
  "kontakt": {"email": "", "telefon": "", "zdroj_kontaktu": ""},
  "co_dela": "souhrn 1-3 věty ověřený z jejich webu",
  "problemy": ["konkrétní ověřený nedostatek", "..."],
  "skore": 4,
  "overena_fakta": ["služby", "oblast působnosti", "otevírací doba"],
  "neznama_data": ["ceny", "reference", "fotky"]
}
```

## Postup a pořadí

1. **Agent 1** (sekvenčně, vše ostatní čeká na jeho výstup).
2. **Agenti 2 a 3 spusť paralelně** v jedné dávce (nezávisí na sobě, oba čtou jen `prospects.json`). U více než 3 kandidátů můžeš agenty 2 a 3 rozdělit po kandidátech (jeden agent = jeden nebo dva kandidáti), aby běželi rychleji.
3. **Agent 4** až po dokončení agentů 2 a 3.
4. Na konci pošli uživateli screenshoty dem do náhledu a shrň výsledky (viz Po dokončení agentů).

Sub-agenty spouštěj na pozadí a po jejich dokončení výsledky převezmi ze souborů, ne z jejich textových odpovědí.

## Agent 1: Průzkumník (hledá weby a vyplňuje údaje)

**Vstup**: obor, lokalita, počet kandidátů. **Výstup**: `prospects.json`.

Úkol agenta:
- WebSearch kombinací `<obor> <město>` (i Firmy.cz, Mapy.cz). Hledej cca 2× víc kandidátů, než je potřeba, a pak vyřaď.
- Preferuj firmy, které web **mají**, ale slabý. Přeskoč weby jen na Facebooku, velké firmy a řetězce, a firmy s evidentně moderním webem.
- Každý web načti (WebFetch) a vyhodnoť. Kontroly: chybí HTTPS nebo vypršelý certifikát; není responzivní (chybí viewport, fixní šířka, mrňavá písma, ověř ve zdroji stránky); zastaralý vzhled (tabulky na layout, frames, Flash, rok v patičce 2012 apod.); chybí jasná výzva k akci (telefon, poptávkový formulář, kontakt nad ohybem); chybí reference, ceník či rozsah služeb, oblast působnosti, otevírací doba; pomalé/těžké obrázky, rozbité odkazy, chybí meta title/description.
- Skóre 1-5 (5 = nejhorší web, nejlepší kandidát). Do výběru jen skóre ≥ 4 s alespoň 3 konkrétními, ověřitelnými nedostatky.
- **Kontakt** jen veřejně uvedený firemní (web, Firmy.cz, rejstřík). Neodhaduj adresy a nehledej soukromé e-maily. Pokud e-mail není, ponech prázdný a uveď telefon.
- Do `co_dela` souhrn toho, co firma dělá, jen z toho, co je na jejich webu. Do `neznama_data` vypiš, co se zjistit nepodařilo.
- Žádná tvrzení bez opory: u každého problému musí agent vědět, kde ho viděl.

## Agent 2: Copywriter (píše e-maily)

**Vstup**: `prospects.json`, údaje odesílatele. **Výstup**: `emails/<slug>.md` pro každého kandidáta s e-mailem.

Obsah každého souboru: řádek `Předmět:`, text e-mailu, na konci poznámka `Odkaz na demo: {{DEMO_URL}}` (zástupná značka, vyplní ji Agent 4).

Pravidla e-mailu:
- Max ~120-150 slov. Struktura: 1) jedna věta, co jsi na jejich práci/firmě viděl a ocenil (jen ověřitelné); 2) 2-3 konkrétní postřehy k webu z pole `problemy`, laskavě, bez shazování; 3) "Připravil jsem si nezávaznou ukázku, jak by to mohlo vypadat: {{DEMO_URL}}"; 4) jedna jasná otázka/výzva (krátký hovor nebo odpověď e-mailem); 5) podpis.
- Předmět konkrétní a nepřehnaný, např. "Nápad na web pro <firma> - připravil jsem ukázku".
- Žádné vymyšlené statistiky, žádný falešný nátlak, žádné "získáte X zákazníků".
- **Právní minimum** (zákon č. 480/2004 Sb.): jasně uvedený odesílatel (značka, IČO, adresa), zjevné obchodní sdělení a snadný způsob odmítnutí (např. "Pokud nemáte zájem, stačí odpovědět 'ne' a už vás nebudu kontaktovat"). Žádné hromadné rozesílání, každý e-mail je individuální a první oslovení jednorázové. Upozorni uživatele, že to nenahrazuje právníka.
- Kandidáty bez e-mailu přeskoč a zapiš do `emails/_bez-emailu.md` (jen jména a telefon).

## Agent 3: Tvůrce landing pages (staví demo na míru)

**Vstup**: `prospects.json`, cesta ke vzoru. **Výstup**: `demos/<slug>/index.html` (a související soubory).

Úkol agenta:
- Načti celý vzor (HTML/CSS/JS, sekce, styl, fonty) a drž se jeho designu a konvencí. Pokud obsahuje zástupné značky (`{{FIRMA}}`, `[NAZEV]` apod.), vyplň je, jinak přepiš texty ve stejné struktuře.
- Zkopíruj vzor do `demos/<slug>/` (**vzor nikdy needituj přímo**).
- Obsah jen z `co_dela` a `overena_fakta`: název, služby, oblast působnosti, kontakty, otevírací doba. Co je v `neznama_data` (ceny, reference, recenze, fotky, certifikace, roky praxe) **nevymýšlej**: použij jasně označený zástupný prvek nebo sekci vynech.
- Žádné vymyšlené recenze, loga klientů, počty zakázek ani jiná tvrzení vydávaná za fakta. Cizí fotky a loga nekopíruj, použij neutrální zástupné obrázky ze vzoru.
- Základy SEO a výkonu: title, description, sémantické nadpisy, lazy loading, responzivita, kontrast, viditelné tlačítko volat/poptat, funkční `tel:` a `mailto:`.
- **Označení dema**: v patičce nebo diskrétním pruhu "Ukázkový návrh vytvořil <značka uživatele>, nejedná se o oficiální web firmy <firma>" a `<meta name="robots" content="noindex,nofollow">`. Demo nikdy nevydávej za skutečný web firmy.
- Zkontroluj výsledek (validní HTML, mobilní zobrazení, funkční odkazy). Pokud je dostupný prohlížeč, otevři a ověř vizuálně.
- Vedle `index.html` ulož `demos/<slug>/_pouzite-udaje.md`: co je ověřené a co je zástupné.

## Agent 4: Spojovač (propojí e-mail s landing pagí)

**Vstup**: `prospects.json`, `emails/`, `demos/`. **Výstup**: `final/<slug>/` + `final/_prehled.md`.

Úkol agenta, pro každého kandidáta, který má e-mail i demo:
- **Kontrola konzistence**: fakta v e-mailu (služby, nedostatky webu, název firmy) odpovídají tomu, co demo skutečně řeší; demo obsahuje označení ukázky a noindex; e-mail nic neslibuje, co demo nemá; v demu nezůstaly nevyplněné značky ani vymyšlené údaje. Nesrovnalosti oprav v e-mailu nebo demu a zapiš do přehledu.
- **Propojení**: pokud existuje URL pro demo (viz níže), dosaď ji za `{{DEMO_URL}}` v e-mailu. Pokud ne, přepiš větu na "ukázku posílám v příloze" a vytvoř ZIP `final/<slug>/demo-<slug>.zip` s demem.
- Výsledek ulož jako `final/<slug>/email.md` (hotový text) a `final/<slug>/demo/` (kopie dema).
- **Screenshoty dema**: pro každé demo vytvoř `final/<slug>/nahled-desktop.png` (šířka 1440 px, celá stránka) a `final/<slug>/nahled-mobil.png` (šířka 390 px, celá stránka). Použij headless Chromium (Playwright v Pythonu nebo Node; když chybí, nainstaluj `playwright` a `playwright install chromium`) a otevři `file://.../index.html`. Po pořízení se na snímky podívej (Read) a ověř, že se stránka vykreslila celá, bez rozbitého layoutu, přetékání a prázdných míst; chyby oprav v demu a snímky udělej znovu. Když screenshot nejde pořídit, napiš to do `_prehled.md` a pokračuj bez nich.
- `final/_prehled.md`: tabulka firma, město, skóre, hlavní nedostatky, kontakt, cesta k e-mailu, cesta k demu, co je ověřené a co zástupné, co musí uživatel doplnit před odesláním.

Agent 4 sám **nic neodesílá, nic nenasazuje a nevytváří koncepty v Gmailu**. Tyto kroky řeší hlavní vlákno až po potvrzení.

## Po dokončení agentů (hlavní vlákno)

- **Pošli screenshoty do náhledu**: pro každého kandidáta pošli `nahled-desktop.png` a `nahled-mobil.png` nástrojem SendUserFile (`display: "render"`, `status: "normal"`, popisek s názvem firmy a městem, např. "Demo: Instalatér Novák, Teplice (desktop)"). Posílej průběžně po kandidátech, ne až na konci. Soubory pošli jako samostatné snímky, ne ZIP.
- Předej uživateli stručný přehled (tabulka) z `final/_prehled.md`, s upozorněním, co je třeba doplnit (`[DOPLNIT]`).
- Nabídni (nic nedělej bez souhlasu): a) nasazení dem na Vercel jako preview (ne produkce) a dosazení odkazů do e-mailů; b) koncepty e-mailů v Gmailu (`create_draft`), **nikdy `send_message`**; c) zápis do Google Sheets pro sledování oslovení (stav, datum, odpověď).
- Když kandidát odpoví "ne" nebo "nepište", zapiš to a už ho neoslovuj.

## Pravidla

- Žádné odeslání ani zveřejnění bez potvrzení uživatele.
- U každého tvrzení o webu kandidáta musí být ověřitelný zdroj.
- Nepřehánět kritiku, cílem je nabídnout pomoc, ne zahanbit.
- Osobní údaje: jen veřejné firemní kontakty a minimum dat (název, e-mail, web, poznámky k webu).
