# AICU L18 - URL workshop

Baseline indipendente per il workshop post-aula sul percorso:

```txt
referenceUrl -> API -> SQLite -> response -> href
```

## Requisiti

- Node.js 26 o superiore;
- pnpm.

## Setup

```bash
pnpm install
pnpm setup:browsers
```

## Primo run

```bash
pnpm test:e2e
```

Il risultato iniziale e' intenzionale:

- URL `https`: verde;
- URL `javascript:`: rosso.

Per aprire soltanto i due scenari security in modalita' debug:

```bash
pnpm demo:security --debug
```

Il risultato atteso resta un test verde e un test rosso.

## Missione

Leggi `consegna.md`. Devi definire una policy minima dei protocolli consentiti e
applicare un fallback sicuro, senza cambiare il valore memorizzato o aggiungere
dipendenze.

Il requisito certo e' `https` consentito e `javascript:` rifiutato. Ogni protocollo
aggiuntivo richiede una scelta esplicita.

## Comandi

```bash
pnpm dev
pnpm check
pnpm test
pnpm test:e2e
pnpm test:all
```

SQLite usa un file locale in `data/` per `pnpm dev` e un database in-memory nei test.

## Demo manuale locale

Avviare l'app:

```bash
pnpm demo:reset
pnpm dev
```

Creare un ticket con titolo `Riferimento malevolo L18` e incollare nel campo
`URL di riferimento`:

```txt
javascript:(()=>{document.body.innerHTML='<main style="position:fixed;inset:0;z-index:999999;display:grid;place-items:center;background:#ff00a8;color:#050005;border:16px solid red;font:900 5vw system-ui;text-align:center">CONTENUTO MALEVOLO ESEGUITO<br><small style="font-size:2vw">QUESTA UI NON APPARTIENE AL PRODOTTO</small></main>';document.body.style.overflow='hidden'})()
```

Dopo il salvataggio, aprire `Dettagli` e selezionare `Apri riferimento`. Il payload
modifica solo la pagina locale con un takeover chiaramente dimostrativo. Per ripristinare:

```bash
pnpm demo:reset
```

Ricaricare quindi la dashboard.

---

# Handoff Note v2 — L18 URL Workshop XSS Fix

## Scope

- **Modifica richiesta**: neutralizzare l'esposizione di `javascript:` URL nell'attributo `href` del `<a id="ticket-reference-link">` mantenendo funzionanti i riferimenti `https://`.
- **Fuori scope**: server, test, database, HTML, CSS, autenticazione, CSP, redirect, nuove dipendenze, validazione lato server dei protocolli, qualsiasi modifica al payload `visibleJavascriptReferenceUrl` nei test.

## Changes

- **File principale**: `src/main.js` (unico file modificato).
- **Sintesi**: estratta la funzione `isSafeHttpsUrl(value)` che usa `new URL(value)` per il parsing nativo del browser e verifica `.protocol === 'https:'`. Il catch restituisce esplicitamente `false`. In `showTicketDetails()`, la condizione diventa `ticket.referenceUrl && isSafeHttpsUrl(ticket.referenceUrl)`. Se false, il link viene nascosto, l'href rimosso, e il fallback `<p id="ticket-reference-fallback">Riferimento non disponibile.</p>` (preesistente in `index.html:158`) viene reso visibile. Il dato originale nel database non viene alterato.
- **Changelog v1 → v2**:
  - `startsWith('https://')` (case-sensitive) → `new URL()` + `.protocol` (case-insensitive, `Https://` ora accettato).
  - Check lessicale → parsing strutturale: `https://\njavascript:...` ora bloccato (`new URL()` lancia TypeError).
  - `catch {}` vuoto → `catch { return false }` esplicito, contratto documentato dal nome della funzione.

## Validation

- **Controlli eseguiti**:
  - `pnpm check` — syntax check su tutti i file JS.
  - `pnpm test` — 16 test unit/api passati.
  - `pnpm test:e2e` — 3 test e2e passati, incluso `does not expose a javascript URL as a navigable reference`.
  - `pnpm test:all` — suite completa verde.
- **Controlli non eseguiti**:
  - Test manuale su browser reale.
  - Penetration test su altri vettori XSS (innerHTML, template injection).

## Review Notes

- `http://` e' rifiutato insieme a `javascript:`. Se in futuro servisse HTTP, va aggiunto esplicitamente alla allowlist.