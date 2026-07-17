# Diff Review — XSS Fix L18 URL Workshop

## File toccato

| File | Funzione | Righe |
|------|----------|-------|
| `src/main.js` | `showTicketDetails()` | 123-137 |

Tutti gli altri file (server, test, database, HTML, CSS, package.json) sono **immutati**.

## Diff con commenti

```diff
 function showTicketDetails(ticket) {
   ticketDetailsTitle.textContent = ticket.title;
   ticketDetailsCustomer.textContent = ticket.customer;
   ticketDetailsBody.textContent = ticket.description || "Nessuna descrizione.";

   if (ticket.referenceUrl) {
-    // Intentionally unsafe for the L18 URL workshop.
-    ticketReferenceLink.href = ticket.referenceUrl;
-    ticketReferenceLink.hidden = false;
-    ticketReferenceFallback.hidden = true;
+    // Solo https:// e' consentito — ogni altro schema mostra il fallback.
+    if (ticket.referenceUrl.startsWith('https://')) {
+      // CASO CONSENTITO: assegna href, mostra il link, nasconde fallback.
+      ticketReferenceLink.href = ticket.referenceUrl;
+      ticketReferenceLink.hidden = false;
+      ticketReferenceFallback.hidden = true;
+    } else {
+      // CASO NON CONSENTITO (es. javascript:, data:, http://):
+      // rimuove href, nasconde link, mostra "Riferimento non disponibile."
+      ticketReferenceLink.removeAttribute("href");
+      ticketReferenceLink.hidden = true;
+      ticketReferenceFallback.hidden = false;
+    }
   } else {
+    // NESSUN referenceUrl: link nascosto, fallback visibile.
     ticketReferenceLink.removeAttribute("href");
     ticketReferenceLink.hidden = true;
     ticketReferenceFallback.hidden = false;
   }

   ticketDetails.hidden = false;
 }
```

## Tre rami logici del controllo

| Condizione | Link (`<a>`) | Fallback (`<p>`) | Esempio |
|------------|-------------|-------------------|---------|
| `referenceUrl` inizia con `https://` | visibile, href impostato | nascosto | `https://docs.example.test/ticket/42` |
| `referenceUrl` presente ma NON `https://` | nascosto, href rimosso | visibile: "Riferimento non disponibile." | `javascript:(...)`, `data:`, `http://` |
| `referenceUrl` vuoto/assente | nascosto, href rimosso | visibile: "Riferimento non disponibile." | `""` |

## Cosa non e' cambiato

- Il valore `referenceUrl` nel database rimane identico (lettura/scrittura server immutate).
- Il payload JSON della response API contiene ancora il valore originale.
- Il test `visibleJavascriptReferenceUrl` nelle fixture non e' stato modificato.
- Nessuna nuova dipendenza, nessun nuovo file.
- Nessuna modifica a `index.html`: l'elemento `<p id="ticket-reference-fallback">` esisteva gia'.

## Verifica

```
pnpm check     → ok (nessun errore di sintassi)
pnpm test      → 16/16 pass
pnpm test:e2e  → 3/3 pass (1 skipped)
  ✓ keeps an https reference available in ticket details
  ✓ does not expose a javascript URL as a navigable reference   ← FIXATO
  ✓ shows the server-calculated urgency label in the ticket list
```
