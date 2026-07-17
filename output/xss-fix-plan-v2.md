# XSS Fix Plan v2 — L18 URL Workshop

## Data flow: source → path → sink

| Step     | Dettaglio                                                                 | File:riga              |
|----------|---------------------------------------------------------------------------|------------------------|
| source   | `referenceUrl` inviato via `POST /api/tickets`, salvato nel DB senza check protocollo | `server/validation.js:34` |
| path     | `GET /api/tickets` restituisce `referenceUrl` nel JSON, va nella Map `ticketsById` | `server/app.js:57`, `src/main.js:55` |
| sink     | `ticketReferenceLink.href = ticket.referenceUrl` — valore grezzo nell'href | **`src/main.js:125`** |
| effetto  | `javascript:` href esegue codice al click (stored XSS)                    | —                      |

## Vulnerability

Stored XSS via `javascript:` URI injection in anchor `href`. Il server non valida il protocollo; il frontend assegna il valore ricevuto direttamente all'attributo `href` senza sanitizzazione.

## Policy

Solo lo schema `https://` e' consentito per i riferimenti esterni; ogni altro protocollo (incluso `javascript:`) attiva il fallback testuale `Riferimento non disponibile.` senza alterare il valore originale memorizzato.

## Patch (v2: `new URL()` — aggiornamento dalla v1)

### Motivazione dell'upgrade

| Problema v1 (`startsWith`)            | Risolto da v2 (`new URL`)                                     |
|---------------------------------------|---------------------------------------------------------------|
| Case-sensitive: `Https://...` bloccato | `.protocol === 'https:'` normalizza il case (falso positivo risolto) |
| `https://\njavascript:...` passa il check lessicale | `new URL()` lancia TypeError su newline nel protocollo |
| URL malformati: comportamento imprevedibile | try/catch → sempre bloccato, sempre fallback |

### File toccato

| File         | Funzione                | Righe   |
|--------------|-------------------------|---------|
| `src/main.js`| `showTicketDetails()`   | 123-137 |

### Modifica

Sostituire l'intero blocco `if (ticket.referenceUrl) { ... } else { ... }` con la versione basata su `new URL()`:

```js
// PRIMA (v1 — startsWith, case-sensitive, bypassabile con newline):
if (ticket.referenceUrl) {
    if (ticket.referenceUrl.startsWith('https://')) {
        ticketReferenceLink.href = ticket.referenceUrl;
        ticketReferenceLink.hidden = false;
        ticketReferenceFallback.hidden = true;
    } else {
        ticketReferenceLink.removeAttribute("href");
        ticketReferenceLink.hidden = true;
        ticketReferenceFallback.hidden = false;
    }
} else {
    ticketReferenceLink.removeAttribute("href");
    ticketReferenceLink.hidden = true;
    ticketReferenceFallback.hidden = false;
}

// DOPO (v2 — new URL(), protocol check, try/catch):
if (ticket.referenceUrl) {
    let safeUrl = null;

    try {
        const parsed = new URL(ticket.referenceUrl);
        if (parsed.protocol === 'https:') {
            safeUrl = ticket.referenceUrl;
        }
    } catch {}

    if (safeUrl) {
        ticketReferenceLink.href = safeUrl;
        ticketReferenceLink.hidden = false;
        ticketReferenceFallback.hidden = true;
    } else {
        ticketReferenceLink.removeAttribute("href");
        ticketReferenceLink.hidden = true;
        ticketReferenceFallback.hidden = false;
    }
} else {
    ticketReferenceLink.removeAttribute("href");
    ticketReferenceLink.hidden = true;
    ticketReferenceFallback.hidden = false;
}
```

### Flusso decisionale

```
ticket.referenceUrl presente?
├─ NO  → link nascosto, fallback visibile
└─ SI' → new URL(referenceUrl)
          ├─ throws  → catch → safeUrl = null → link nascosto, fallback visibile
          └─ ok      → .protocol === 'https:' ?
                         ├─ SI' → safeUrl = referenceUrl → link visibile con href
                         └─ NO  → safeUrl = null → link nascosto, fallback visibile
```

Tutti i casi non-`https` convergono sullo stesso ramo: `<a>` nascosto, `href` rimosso, `<p id="ticket-reference-fallback">Riferimento non disponibile.</p>` visibile.

### Tabella dei casi

| Input                                  | `new URL()` | `.protocol`   | Esito                                      |
|----------------------------------------|-------------|---------------|--------------------------------------------|
| `https://docs.example.test/ticket/42`  | ok          | `https:`      | link visibile, href impostato              |
| `HTTPS://docs.example.test`            | ok          | `https:` (*)  | link visibile, href impostato              |
| `javascript:alert(1)`                  | ok          | `javascript:` | link nascosto, fallback visibile           |
| `javascript:(()=>{...})()` (payload)   | ok          | `javascript:` | link nascosto, fallback visibile           |
| `http://example.test`                  | ok          | `http:`       | link nascosto, fallback visibile           |
| `data:text/html,<script>alert(1)</script>` | ok      | `data:`       | link nascosto, fallback visibile           |
| `file:///etc/passwd`                   | ok          | `file:`       | link nascosto, fallback visibile           |
| `https://\njavascript:alert(1)`        | **throws**  | —             | catch → link nascosto, fallback visibile   |
| `not-a-url`                            | **throws**  | —             | catch → link nascosto, fallback visibile   |
| `""` (vuota)                           | —           | —             | ramo else → link nascosto, fallback visibile |

(*) `new URL()` normalizza il protocollo a lowercase, quindi `HTTPS://` diventa `.protocol === 'https:'`.

### Annotazioni

- **`https://` funziona** — case-insensitive: anche `Https://` o `HTTPS://` passano.
- **`javascript:` rifiutato** — `.protocol === 'javascript:'`, non matcha `'https:'`.
- **Newline bypass neutralizzato** — `new URL('https://\n...')` lancia TypeError, finisce nel catch, fallback.
- **Nessun nuovo `<p>` da creare**: l'elemento fallback esiste gia' in `index.html:158`.
- **Valore originale intatto**: nessuna modifica a DB, server, o struttura dati. `referenceUrl` nel DB resta esattamente come e' stato inserito.
- **Nessuna nuova dipendenza**: `URL` e' API nativa del browser (gia' usata lato server in `server/app.js:128`).
- **Nessuna modifica a server, test, database, HTML, CSS.**
- **Test ostile non indebolito**: il payload `javascript:` delle fixture rimane identico.
- **Ogni altro protocollo richiede una scelta esplicita**: per abilitare `http://` bisognerebbe aggiungere `|| parsed.protocol === 'http:'`.

### Verifica

```bash
pnpm check
pnpm test
pnpm test:e2e
pnpm test:all
```

### Vincoli rispettati

- [x] URL `https` valido resta disponibile (case-insensitive nella v2)
- [x] Schema `javascript:` non esposto come destinazione navigabile
- [x] Pannello mostra `Riferimento non disponibile.` per valore non consentito (catch incluso)
- [x] Fallback non modifica il valore originale memorizzato
- [x] Nessuna nuova dipendenza
- [x] Test mirato e suite precedente verdi
- [x] Diff limitato al percorso `referenceUrl -> href`
- [x] Nessun tocco a server, test, database, HTML, CSS
- [x] Test ostile non indebolito o riscritto
- [x] Resistente a newline encoding bypass
- [x] Case-insensitive sui protocolli https validi
