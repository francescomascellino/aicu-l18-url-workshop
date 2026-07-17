# XSS Fix Plan — L18 URL Workshop

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

## Patch

### File toccato

| File         | Funzione                | Righe   |
|--------------|-------------------------|---------|
| `src/main.js`| `showTicketDetails()`   | 123-132 |

### Modifica

Nel blocco `if (ticket.referenceUrl)`, aggiungere un controllo `startsWith('https://')`:

```js
// PRIMA (righe 123-132):
if (ticket.referenceUrl) {
    // Intentionally unsafe for the L18 URL workshop.
    ticketReferenceLink.href = ticket.referenceUrl;
    ticketReferenceLink.hidden = false;
    ticketReferenceFallback.hidden = true;
} else {
    ticketReferenceLink.removeAttribute("href");
    ticketReferenceLink.hidden = true;
    ticketReferenceFallback.hidden = false;
}

// DOPO:
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
```

### Annotazioni

- **`https://` funziona**: il primo ramo assegna href normalmente.
- **`javascript:` rifiutato**: il ramo `else` nasconde il `<a>`, rimuove href, mostra `<p id="ticket-reference-fallback">Riferimento non disponibile.</p>`.
- **Nessun nuovo `<p>` da creare**: l'elemento fallback esiste gia' in `index.html:158`.
- **Valore originale intatto**: nessuna modifica a DB, server, o struttura dati.
- **Nessuna nuova dipendenza.**
- **Nessuna modifica a server, test, database, HTML, CSS.**
- **Test ostile non indebolito**: il payload `javascript:` delle fixture rimane identico.

### Verifica

```bash
pnpm check
pnpm test
pnpm test:e2e
pnpm test:all
```

### Vincoli rispettati

- [x] URL `https` valido resta disponibile
- [x] Schema `javascript:` non esposto come destinazione navigabile
- [x] Pannello mostra `Riferimento non disponibile.` per valore non consentito
- [x] Fallback non modifica il valore originale memorizzato
- [x] Nessuna nuova dipendenza
- [x] Test mirato e suite precedente verdi
- [x] Diff limitato al percorso `referenceUrl -> href`
- [x] Nessun tocco a server, test, database, HTML, CSS
- [x] Test ostile non indebolito o riscritto
