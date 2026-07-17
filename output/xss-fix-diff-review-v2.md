# Diff Review v2 — XSS Fix L18 URL Workshop

## Modifiche dalla v1 (`startsWith`)

| Voce | v1 | v2 |
|------|----|----|
| Controllo protocollo | `startsWith('https://')` — lessicale, case-sensitive | `new URL()` + `.protocol === 'https:'` — parsing nativo |
| `Https://...` | bloccato (falso positivo) | accettato (`new URL` normalizza a lowercase) |
| `https://\njavascript:...` | passa il check lessicale (non sfruttabile, ma debole) | `new URL()` lancia, `isSafeHttpsUrl` restituisce `false`, bloccato |
| Nuova funzione | nessuna | `isSafeHttpsUrl()` estratta |
| `catch` vuoto | — | `catch { return false }` — esplicito, documentato dal nome della funzione |

## File toccato

| File | Righe | Modifica |
|------|-------|----------|
| `src/main.js` | 118-126 | Nuova funzione `isSafeHttpsUrl()` |
| `src/main.js` | 131 | Condizione in `showTicketDetails` |

## Diff

```diff
+function isSafeHttpsUrl(value) {
+  try {
+    return new URL(value).protocol === 'https:';
+  } catch {
+    return false;
+  }
+}
+
 function showTicketDetails(ticket) {
   ...
-  if (ticket.referenceUrl) {
-    if (ticket.referenceUrl.startsWith('https://')) {
-      ticketReferenceLink.href = ticket.referenceUrl;
-      ticketReferenceLink.hidden = false;
-      ticketReferenceFallback.hidden = true;
-    } else {
-      ticketReferenceLink.removeAttribute("href");
-      ticketReferenceLink.hidden = true;
-      ticketReferenceFallback.hidden = false;
-    }
+  if (ticket.referenceUrl && isSafeHttpsUrl(ticket.referenceUrl)) {
+    ticketReferenceLink.href = ticket.referenceUrl;
+    ticketReferenceLink.hidden = false;
+    ticketReferenceFallback.hidden = true;
   } else {
     ticketReferenceLink.removeAttribute("href");
     ticketReferenceLink.hidden = true;
     ticketReferenceFallback.hidden = false;
   }
```

## `isSafeHttpsUrl` — funzione estratta

- **`new URL()` ok + `.protocol === 'https:'`** → `true` — URL strutturalmente valido e con protocollo HTTPS.
- **`new URL()` ok + `.protocol !== 'https:'`** → `false` — URL valido ma protocollo non consentito (`javascript:`, `data:`, `http:`, `file:`).
- **`new URL()` lancia** → catch → `return false` — stringa non e' un URL valido (es. caratteri non consentiti, newline).

Nessun `catch {}` vuoto: il ramo restituisce esplicitamente `false`, e il nome della funzione documenta il contratto.

## Rami logici finali

| Condizione | Link `<a>` | Fallback `<p>` |
|------------|-----------|----------------|
| `referenceUrl` assente | nascosto, href rimosso | visibile |
| `referenceUrl` presente, `isSafeHttpsUrl` → `true` | visibile, href impostato | nascosto |
| `referenceUrl` presente, `isSafeHttpsUrl` → `false` | nascosto, href rimosso | visibile |

## Cosa non cambia

- `index.html:158` — l'elemento `<p id="ticket-reference-fallback">` preesiste, mai toccato.
- Valore `referenceUrl` nel DB: intatto. Nessuna modifica a server, test, HTML, CSS.
- Nessuna nuova dipendenza. `URL` e' API nativa del browser.
- Test ostile `visibleJavascriptReferenceUrl` non modificato.

## Verifica

```
pnpm check     → ok
pnpm test      → 16/16 pass
pnpm test:e2e  → 3/3 pass
  ✓ keeps an https reference available in ticket details
  ✓ does not expose a javascript URL as a navigable reference
  ✓ shows the server-calculated urgency label in the ticket list
```
