# Domande per Colloquio Tecnico

## Git e Workflow con Pull Request

### Domanda 1: Concetti Base di Git
**Qual è la differenza tra `git merge` e `git rebase`? Quando useresti l'uno rispetto all'altro?**

**Risposta:**
- `git merge` crea un nuovo commit di merge che unisce le storie di due branch, preservando la storia completa di entrambi i branch
- `git rebase` riscrive la storia spostando i commit di un branch in cima a un altro, creando una storia lineare
- **Quando usare merge**: per branch pubblici/condivisi, per preservare il contesto storico completo, per feature branch importanti
- **Quando usare rebase**: per pulire la storia prima di una PR, per branch locali non ancora pushati, per mantenere una storia lineare e più leggibile

### Domanda 2: Gestione dei Conflitti
**Durante una Pull Request si verifica un conflitto di merge. Come lo gestisci passo per passo?**

**Risposta:**
1. Aggiorna il branch locale: `git checkout feature-branch && git fetch origin`
2. Merge o rebase con il branch di destinazione: `git merge origin/main` o `git rebase origin/main`
3. Git segnala i conflitti nei file
4. Apri i file in conflitto e risolvi manualmente le sezioni tra `<<<<<<<`, `=======`, `>>>>>>>`
5. Dopo la risoluzione: `git add <file-risolti>`
6. Se hai fatto merge: `git commit`, se hai fatto rebase: `git rebase --continue`
7. Push delle modifiche: `git push origin feature-branch` (potrebbe richiedere `--force` se hai fatto rebase)

### Domanda 3: Best Practices PR
**Quali sono le best practices per creare una buona Pull Request?**

**Risposta:**
- **Dimensione**: PR piccole e focalizzate (idealmente < 400 righe)
- **Descrizione chiara**: cosa fa la PR, perché è necessaria, come testare
- **Commit atomici**: ogni commit dovrebbe essere una unità logica completa
- **Messaggi commit**: seguire una convenzione (es. Conventional Commits)
- **Branch aggiornato**: fare rebase/merge con main prima di aprire la PR
- **Test**: includere test per il nuovo codice
- **Code review**: rispondere ai commenti in modo costruttivo
- **CI/CD**: assicurarsi che tutti i check automatici passino

### Domanda 4: Git Workflow
**Descrivi il Git Flow o GitHub Flow. Quali sono i branch principali e quando vengono usati?**

**Risposta:**

**GitHub Flow (più semplice):**
- Branch `main`: sempre deployabile
- Feature branch: `feature/nome-feature` creati da main
- Si sviluppa, si apre PR, si fa review, si mergia in main
- Deploy automatico da main

**Git Flow (più strutturato):**
- `main`: codice in produzione
- `develop`: branch di integrazione per lo sviluppo
- `feature/*`: nuove funzionalità (da develop)
- `release/*`: preparazione release (da develop)
- `hotfix/*`: fix urgenti in produzione (da main)

### Domanda 5: Comandi Avanzati
**Come annulli l'ultimo commit mantenendo le modifiche nel working directory?**

**Risposta:**
```bash
git reset --soft HEAD~1  # mantiene le modifiche nello stage
# oppure
git reset HEAD~1         # mantiene le modifiche ma non nello stage
# oppure
git reset --hard HEAD~1  # elimina completamente le modifiche
```

Per annullare solo in locale: `git reset`
Se già pushato: `git revert HEAD` (crea un nuovo commit che annulla le modifiche)

---

## Protocollo HTTP e API RESTful

### Domanda 6: Metodi HTTP
**Spiega la differenza tra i metodi HTTP GET, POST, PUT, PATCH e DELETE. Quali sono idempotenti?**

**Risposta:**
- **GET**: recupera risorse, non modifica dati, idempotente e safe
- **POST**: crea nuove risorse, non idempotente
- **PUT**: sostituisce completamente una risorsa, idempotente
- **PATCH**: modifica parzialmente una risorsa, idempotente
- **DELETE**: elimina una risorsa, idempotente

**Idempotenti**: GET, PUT, PATCH, DELETE (chiamate multiple producono lo stesso risultato)
**Non idempotenti**: POST (ogni chiamata può creare una nuova risorsa)

### Domanda 7: Codici di Stato HTTP
**Quali sono i principali codici di stato HTTP e cosa significano? Fornisci esempi per le categorie 2xx, 4xx, 5xx.**

**Risposta:**

**2xx - Successo:**
- 200 OK: richiesta riuscita
- 201 Created: risorsa creata con successo
- 204 No Content: successo ma nessun contenuto da restituire

**4xx - Errori Client:**
- 400 Bad Request: richiesta malformata
- 401 Unauthorized: autenticazione richiesta
- 403 Forbidden: autenticato ma non autorizzato
- 404 Not Found: risorsa non trovata
- 422 Unprocessable Entity: validazione fallita

**5xx - Errori Server:**
- 500 Internal Server Error: errore generico del server
- 502 Bad Gateway: problema con un server upstream
- 503 Service Unavailable: servizio temporaneamente non disponibile

### Domanda 8: Design di API RESTful
**Progetta gli endpoint REST per un sistema di gestione blog (articoli e commenti). Includi i metodi HTTP appropriati.**

**Risposta:**

**Articoli:**
- `GET /api/articles` - lista tutti gli articoli
- `GET /api/articles/:id` - dettaglio articolo
- `POST /api/articles` - crea nuovo articolo
- `PUT /api/articles/:id` - aggiorna completamente articolo
- `PATCH /api/articles/:id` - aggiorna parzialmente articolo
- `DELETE /api/articles/:id` - elimina articolo

**Commenti (risorsa nested):**
- `GET /api/articles/:id/comments` - lista commenti di un articolo
- `POST /api/articles/:id/comments` - crea commento per un articolo
- `GET /api/comments/:id` - dettaglio singolo commento
- `PUT /api/comments/:id` - aggiorna commento
- `DELETE /api/comments/:id` - elimina commento

### Domanda 9: Headers HTTP
**Quali sono gli header HTTP più importanti in una richiesta e risposta API? Spiega almeno 5.**

**Risposta:**

**Request Headers:**
- `Content-Type`: formato del body (es. `application/json`)
- `Authorization`: credenziali (es. `Bearer <token>`)
- `Accept`: formato risposta desiderato
- `User-Agent`: informazioni sul client

**Response Headers:**
- `Content-Type`: formato della risposta
- `Content-Length`: dimensione del body in bytes
- `Cache-Control`: direttive di caching
- `ETag`: identificatore versione risorsa
- `Location`: URL della risorsa creata (con 201)
- `Access-Control-Allow-Origin`: CORS policy

### Domanda 10: REST vs SOAP
**Qual è la differenza tra REST e SOAP? Quando useresti l'uno o l'altro?**

**Risposta:**

**REST (Representational State Transfer):**
- Architettura basata su HTTP standard
- Usa JSON (principalmente) o XML
- Stateless, più leggero e flessibile
- Migliore per web e mobile APIs

**SOAP (Simple Object Access Protocol):**
- Protocollo più rigido e strutturato
- Solo XML
- Ha WS-Security integrato
- Migliore per transazioni enterprise complesse, quando serve ACID

**Quando usare REST**: applicazioni web moderne, mobile apps, microservizi
**Quando usare SOAP**: sistemi bancari, pagamenti, dove serve sicurezza rigorosa e transazioni ACID

### Domanda 11: Autenticazione API
**Spiega la differenza tra autenticazione con Session-based, Token-based (JWT) e OAuth2.**

**Risposta:**

**Session-based:**
- Server mantiene stato della sessione
- Cookie con session ID inviato al client
- Richiede storage server-side
- Problematico per architetture distribuite

**Token-based (JWT):**
- Stateless: token contiene tutte le info
- Token firmato digitalmente
- Inviato in header `Authorization: Bearer <token>`
- Scalabile per microservizi

**OAuth2:**
- Framework di autorizzazione (non autenticazione)
- Delega accesso a risorse senza condividere password
- Usa access token e refresh token
- Ideale per third-party integrations

### Domanda 12: CORS
**Cos'è CORS e perché esiste? Come si gestisce in un'API?**

**Risposta:**

**CORS (Cross-Origin Resource Sharing):**
- Meccanismo di sicurezza browser
- Impedisce richieste da origini diverse da quella della pagina
- Protezione contro attacchi CSRF

**Origine diversa**: diverso dominio, protocollo o porta

**Gestione:**
- Server deve includere headers CORS:
  - `Access-Control-Allow-Origin`: origini permesse
  - `Access-Control-Allow-Methods`: metodi permessi
  - `Access-Control-Allow-Headers`: headers permessi
- Gestione preflight request (OPTIONS) per richieste complesse
- Configurazione nel backend (Express, Django, ecc.)

Esempio:
```
Access-Control-Allow-Origin: https://example.com
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization
```


## Cosa è un pull request

Un **pull request** (o PR) è una funzionalità dei sistemi di controllo versione come Git, particolarmente usata su piattaforme come GitHub, GitLab e Bitbucket.

In sostanza, è una **richiesta di revisione e integrazione** del codice. Funziona così:

1. **Lavori su una copia del codice** - Crei un branch (ramo) separato dove fai le tue modifiche
2. **Proponi le modifiche** - Quando hai finito, apri un pull request per chiedere che il tuo codice venga "tirato" (pull) nel branch principale del progetto
3. **Revisione del codice** - Altri sviluppatori possono vedere le tue modifiche, commentarle, suggerire miglioramenti
4. **Discussione e aggiustamenti** - Puoi rispondere ai commenti e fare ulteriori modifiche se necessario
5. **Approvazione e merge** - Una volta che tutto è ok, le tue modifiche vengono integrate (merged) nel codice principale

**Perché è utile?**
- Permette la **revisione del codice** prima che venga integrato
- Facilita la **collaborazione** tra sviluppatori
- Mantiene uno **storico** delle discussioni e decisioni
- Previene errori e mantiene la qualità del codice

È uno strumento fondamentale nello sviluppo software moderno, specialmente nei progetti open source dove persone diverse contribuiscono allo stesso codice.
