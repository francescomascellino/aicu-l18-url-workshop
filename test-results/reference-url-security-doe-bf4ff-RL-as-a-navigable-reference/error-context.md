# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: reference-url-security.spec.js >> does not expose a javascript URL as a navigable reference
- Location: tests\e2e\reference-url-security.spec.js:40:1

# Error details

```
Error: expect(received).not.toMatch(expected)

Expected pattern: not /^javascript:/i
Received string:      "javascript:(()=>{document.body.innerHTML='<main style=\"position:fixed;inset:0;z-index:999999;display:grid;place-items:center;background:#ff00a8;color:#050005;border:16px solid red;font:900 5vw system-ui;text-align:center\">CONTENUTO MALEVOLO ESEGUITO<br><small style=\"font-size:2vw\">QUESTA UI NON APPARTIENE AL PRODOTTO</small></main>';document.body.style.overflow='hidden'})()"
```

# Page snapshot

```yaml
- generic [ref=e2]:
  - complementary "Navigazione" [ref=e3]:
    - button "Ticket" [ref=e4] [cursor=pointer]: ▣
    - button "Inbox" [ref=e5] [cursor=pointer]: □
    - button "Messaggi" [ref=e6] [cursor=pointer]: ◌
    - button "Impostazioni" [ref=e7] [cursor=pointer]: ⚙
  - main [ref=e8]:
    - generic [ref=e9]:
      - generic [ref=e10]:
        - paragraph [ref=e11]: Support ops
        - heading "Dashboard ticket" [level=1] [ref=e12]
      - generic "Operatore corrente" [ref=e13]:
        - generic [ref=e14]: GR
        - generic [ref=e15]:
          - generic [ref=e16]: Operatore
          - strong [ref=e17]: Giulia Rinaldi
        - generic [ref=e18]:
          - generic [ref=e19]: Turno
          - strong [ref=e20]: 08:00 - 16:00
    - region "Un link di cui non ci fidiamo" [ref=e21]:
      - generic [ref=e22]:
        - paragraph [ref=e23]: Modulo 05
        - heading "Un link di cui non ci fidiamo" [level=2] [ref=e24]
      - list [ref=e25]:
        - listitem [ref=e26]: source
        - listitem [ref=e27]: percorso
        - listitem [ref=e28]: href
        - listitem [ref=e29]: policy
        - listitem [ref=e30]: evidenza
    - generic [ref=e31]:
      - generic [ref=e32]:
        - generic [ref=e33]:
          - paragraph [ref=e34]: Nuovo ticket
          - heading "Crea richiesta" [level=2] [ref=e35]
          - generic [ref=e36]: Workshop L18
        - generic [ref=e37]:
          - text: Titolo
          - textbox "Titolo" [ref=e38]:
            - /placeholder: Es. Impossibile accedere al portale clienti
        - generic [ref=e39]:
          - text: Cliente
          - textbox "Cliente" [ref=e40]:
            - /placeholder: Es. Alfa S.r.l.
        - group "Priorita'" [ref=e41]:
          - generic [ref=e42]: Priorita'
          - generic [ref=e43]:
            - generic [ref=e44] [cursor=pointer]:
              - radio "bassa" [ref=e45]
              - text: bassa
            - generic [ref=e47] [cursor=pointer]:
              - radio "normale" [checked] [ref=e48]
              - text: normale
            - generic [ref=e50] [cursor=pointer]:
              - radio "alta" [ref=e51]
              - text: alta
        - group "Canale richiesta" [ref=e53]:
          - generic [ref=e54]: Canale richiesta
          - generic [ref=e55]:
            - generic [ref=e56] [cursor=pointer]:
              - radio "email" [checked] [ref=e57]
              - text: email
            - generic [ref=e58] [cursor=pointer]:
              - radio "telefono" [ref=e59]
              - text: telefono
            - generic [ref=e60] [cursor=pointer]:
              - radio "chat" [ref=e61]
              - text: chat
        - generic [ref=e62]:
          - text: Descrizione
          - textbox "Descrizione" [ref=e63]:
            - /placeholder: Descrivi in dettaglio la richiesta del cliente...
        - generic [ref=e64]:
          - text: URL di riferimento
          - textbox "URL di riferimento" [ref=e65]:
            - /placeholder: https://docs.example.test/ticket/42
        - generic [ref=e66]:
          - generic [ref=e67]: Campo calcolato dal server
          - strong [ref=e68]: "urgencyLabel: da calcolare"
          - paragraph [ref=e69]: La UI non deve decidere questo valore.
        - button "Salva ticket" [ref=e70] [cursor=pointer]
        - status [ref=e71]: Ticket caricati.
      - region "Coda supporto" [ref=e72]:
        - generic [ref=e73]:
          - generic [ref=e74]:
            - paragraph [ref=e75]: Elenco ticket
            - heading "Coda supporto" [level=2] [ref=e76]
          - generic [ref=e77]:
            - generic [ref=e78]: "4"
            - button "Aggiorna" [ref=e79] [cursor=pointer]
        - table [ref=e81]:
          - rowgroup [ref=e82]:
            - row "ID Titolo Cliente Priorita' Canale Urgency label Stato Azioni" [ref=e83]:
              - columnheader "ID" [ref=e84]
              - columnheader "Titolo" [ref=e85]
              - columnheader "Cliente" [ref=e86]
              - columnheader "Priorita'" [ref=e87]
              - columnheader "Canale" [ref=e88]
              - columnheader "Urgency label" [ref=e89]
              - columnheader "Stato" [ref=e90]
              - columnheader "Azioni" [ref=e91]
          - rowgroup [ref=e92]:
            - row "#TCK-42369 Riferimento non consentito L18 Alfa S.r.l. normale email standard aperto 16/07, 21:09 Apri dettagli Riferimento non consentito L18" [ref=e93]:
              - cell "#TCK-42369" [ref=e94]:
                - strong [ref=e95]: "#TCK-42369"
              - cell "Riferimento non consentito L18" [ref=e96]
              - cell "Alfa S.r.l." [ref=e97]
              - cell "normale" [ref=e98]:
                - generic [ref=e99]: normale
              - cell "email" [ref=e100]
              - cell "standard" [ref=e101]:
                - generic [ref=e102]: standard
              - cell "aperto 16/07, 21:09" [ref=e103]:
                - generic [ref=e104]: aperto
                - generic [ref=e105]: 16/07, 21:09
              - cell "Apri dettagli Riferimento non consentito L18" [ref=e106]:
                - button "Apri dettagli Riferimento non consentito L18" [active] [ref=e107] [cursor=pointer]: Dettagli
            - row "#TCK-66086 Riferimento HTTPS L18 Alfa S.r.l. normale email standard aperto 16/07, 21:09 Apri dettagli Riferimento HTTPS L18" [ref=e108]:
              - cell "#TCK-66086" [ref=e109]:
                - strong [ref=e110]: "#TCK-66086"
              - cell "Riferimento HTTPS L18" [ref=e111]
              - cell "Alfa S.r.l." [ref=e112]
              - cell "normale" [ref=e113]:
                - generic [ref=e114]: normale
              - cell "email" [ref=e115]
              - cell "standard" [ref=e116]:
                - generic [ref=e117]: standard
              - cell "aperto 16/07, 21:09" [ref=e118]:
                - generic [ref=e119]: aperto
                - generic [ref=e120]: 16/07, 21:09
              - cell "Apri dettagli Riferimento HTTPS L18" [ref=e121]:
                - button "Apri dettagli Riferimento HTTPS L18" [ref=e122] [cursor=pointer]: Dettagli
            - row "#TCK-10482 Impossibile accedere al portale clienti Alfa S.r.l. alta email prioritario aperto 16/07, 21:09 Apri dettagli Impossibile accedere al portale clienti" [ref=e123]:
              - cell "#TCK-10482" [ref=e124]:
                - strong [ref=e125]: "#TCK-10482"
              - cell "Impossibile accedere al portale clienti" [ref=e126]
              - cell "Alfa S.r.l." [ref=e127]
              - cell "alta" [ref=e128]:
                - generic [ref=e129]: alta
              - cell "email" [ref=e130]
              - cell "prioritario" [ref=e131]:
                - generic [ref=e132]: prioritario
              - cell "aperto 16/07, 21:09" [ref=e133]:
                - generic [ref=e134]: aperto
                - generic [ref=e135]: 16/07, 21:09
              - cell "Apri dettagli Impossibile accedere al portale clienti" [ref=e136]:
                - button "Apri dettagli Impossibile accedere al portale clienti" [ref=e137] [cursor=pointer]: Dettagli
            - row "#TCK-10481 Errore 500 durante il caricamento Beta Consulting normale chat da gestire in lavorazione 16/07, 21:09 Apri dettagli Errore 500 durante il caricamento" [ref=e138]:
              - cell "#TCK-10481" [ref=e139]:
                - strong [ref=e140]: "#TCK-10481"
              - cell "Errore 500 durante il caricamento" [ref=e141]
              - cell "Beta Consulting" [ref=e142]
              - cell "normale" [ref=e143]:
                - generic [ref=e144]: normale
              - cell "chat" [ref=e145]
              - cell "da gestire" [ref=e146]:
                - generic [ref=e147]: da gestire
              - cell "in lavorazione 16/07, 21:09" [ref=e148]:
                - generic [ref=e149]: in lavorazione
                - generic [ref=e150]: 16/07, 21:09
              - cell "Apri dettagli Errore 500 durante il caricamento" [ref=e151]:
                - button "Apri dettagli Errore 500 durante il caricamento" [ref=e152] [cursor=pointer]: Dettagli
        - generic [ref=e153]:
          - strong [ref=e154]: Nessun ticket trovato
          - generic [ref=e155]: La lista verra' aggiornata dopo il salvataggio.
    - region "Riferimento non consentito L18" [ref=e156]:
      - generic [ref=e157]:
        - generic [ref=e158]:
          - paragraph [ref=e159]: Dettagli ticket
          - heading "Riferimento non consentito L18" [level=2] [ref=e160]
        - button "Chiudi" [ref=e161] [cursor=pointer]
      - paragraph [ref=e162]: Alfa S.r.l.
      - paragraph [ref=e163]: Aprire la documentazione collegata.
      - link "Apri riferimento" [ref=e164] [cursor=pointer]:
        - /url: "javascript:(()=>{document.body.innerHTML='<main style=\"position:fixed;inset:0;z-index:999999;display:grid;place-items:center;background:#ff00a8;color:#050005;border:16px solid red;font:900 5vw system-ui;text-align:center\">CONTENUTO MALEVOLO ESEGUITO<br><small style=\"font-size:2vw\">QUESTA UI NON APPARTIENE AL PRODOTTO</small></main>';document.body.style.overflow='hidden'})()"
    - region "Evidenze runtime" [ref=e165]:
      - article [ref=e166]:
        - generic [ref=e167]: payload
        - generic [ref=e168]: "{ \"title\": \"\", \"customer\": \"\", \"description\": \"\", \"referenceUrl\": \"\", \"priority\": \"normale\", \"sourceChannel\": \"email\" }"
      - article [ref=e169]:
        - generic [ref=e170]: response
        - generic [ref=e171]: "{ \"tickets\": [ { \"id\": \"TCK-42369\", \"title\": \"Riferimento non consentito L18\", \"customer\": \"Alfa S.r.l.\", \"description\": \"Aprire la documentazione collegata.\", \"referenceUrl\": \"javascript:(()=>{document.body.innerHTML='<main style=\\\"position:fixed;inset:0;z-index:999999;display:grid;place-items:center;background:#ff00a8;color:#050005;border:16px solid red;font:900 5vw system-ui;text-align:center\\\">CONTENUTO MALEVOLO ESEGUITO<br><small style=\\\"font-size:2vw\\\">QUESTA UI NON APPARTIENE AL PRODOTTO</small></main>';document.body.style.overflow='hidden'})()\", \"priority\": \"normale\", \"sourceChannel\": \"email\", \"urgencyLabel\": \"standard\", \"status\": \"aperto\", \"createdAt\": \"2026-07-16T19:09:48.496Z\" }, { \"id\": \"TCK-66086\", \"title\": \"Riferimento HTTPS L18\", \"customer\": \"Alfa S.r.l.\", \"description\": \"Aprire la documentazione collegata.\", \"referenceUrl\": \"https://docs.example.test/ticket/42\", \"priority\": \"normale\", \"sourceChannel\": \"email\", \"urgencyLabel\": \"standard\", \"status\": \"aperto\", \"createdAt\": \"2026-07-16T19:09:48.256Z\" }, { \"id\": \"TCK-10482\", \"title\": \"Impossibile accedere al portale clienti\", \"customer\": \"Alfa S.r.l.\", \"description\": \"Errore login su account amministrativo.\", \"referenceUrl\": \"https://docs.example.test/accesso-portale\", \"priority\": \"alta\", \"sourceChannel\": \"email\", \"urgencyLabel\": \"prioritario\", \"status\": \"aperto\", \"createdAt\": \"2026-07-16T19:09:47.761Z\" }, { \"id\": \"TCK-10481\", \"title\": \"Errore 500 durante il caricamento\", \"customer\": \"Beta Consulting\", \"description\": \"Errore intermittente nella pagina fatture.\", \"referenceUrl\": \"\", \"priority\": \"normale\", \"sourceChannel\": \"chat\", \"urgencyLabel\": \"da gestire\", \"status\": \"in lavorazione\", \"createdAt\": \"2026-07-16T19:09:47.761Z\" } ] }"
      - article [ref=e172]:
        - generic [ref=e173]: caso valido
        - strong [ref=e174]: alta + telefono → intervento rapido
      - article [ref=e175]:
        - generic [ref=e176]: caso invalido
        - strong [ref=e177]: whatsapp → 400 fieldErrors.sourceChannel
```

# Test source

```ts
  1  | import { expect, test } from "@playwright/test";
  2  | 
  3  | import { visibleJavascriptReferenceUrl } from "../fixtures/url-demo-payloads.js";
  4  | 
  5  | async function createTicket(request, overrides) {
  6  |   const response = await request.post("/api/tickets", {
  7  |     data: {
  8  |       title: "Ticket con riferimento",
  9  |       customer: "Alfa S.r.l.",
  10 |       description: "Aprire la documentazione collegata.",
  11 |       referenceUrl: "",
  12 |       priority: "normale",
  13 |       sourceChannel: "email",
  14 |       ...overrides
  15 |     }
  16 |   });
  17 | 
  18 |   expect(response.status()).toBe(201);
  19 | }
  20 | 
  21 | test("keeps an https reference available in ticket details", async ({
  22 |   page,
  23 |   request
  24 | }) => {
  25 |   const referenceUrl = "https://docs.example.test/ticket/42";
  26 | 
  27 |   await createTicket(request, {
  28 |     title: "Riferimento HTTPS L18",
  29 |     referenceUrl
  30 |   });
  31 | 
  32 |   await page.goto("/");
  33 |   await page.getByRole("button", { name: "Apri dettagli Riferimento HTTPS L18" }).click();
  34 | 
  35 |   const link = page.locator("#ticket-reference-link");
  36 |   await expect(link).toBeVisible();
  37 |   await expect(link).toHaveAttribute("href", referenceUrl);
  38 | });
  39 | 
  40 | test("does not expose a javascript URL as a navigable reference", async ({
  41 |   page,
  42 |   request
  43 | }) => {
  44 |   await createTicket(request, {
  45 |     title: "Riferimento non consentito L18",
  46 |     referenceUrl: visibleJavascriptReferenceUrl
  47 |   });
  48 | 
  49 |   await page.goto("/");
  50 |   await page
  51 |     .getByRole("button", { name: "Apri dettagli Riferimento non consentito L18" })
  52 |     .click();
  53 | 
  54 |   const link = page.locator("#ticket-reference-link");
  55 |   const href = await link.getAttribute("href");
  56 | 
> 57 |   expect(href || "").not.toMatch(/^javascript:/i);
     |                          ^ Error: expect(received).not.toMatch(expected)
  58 |   await expect(page.getByText("Riferimento non disponibile.")).toBeVisible();
  59 | });
  60 | 
```