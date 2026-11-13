# Note sulla configurazione di Claude Code

## Cosa copre `CLAUDE.md`

`CLAUDE.md` viene caricato in ogni sessione, quindi contiene solo ciò che serve quasi sempre:

- una riga di descrizione del progetto;
- i comandi principali: `npm run dev`, `npm test`, `npm run lint`;
- le convenzioni del codice (CommonJS, stile dei test, accesso ai dati solo tramite `db/store.js`, formato degli errori, virgolette doppie e commento sopra ogni route);
- una breve panoramica dell'architettura (`server.js`, `routes/`, `db/store.js`).

**Lasciato fuori di proposito:**

- **La storia delle versioni di Node** (18.11+, 22 in CI, verificato su 26): la versione usata dalla CI è già in `.github/workflows/ci.yml` e non cambia il modo in cui Claude scrive il codice. Tenerla nel file la ripeterebbe in ogni sessione senza beneficio.
- **I comandi per filtrare i test** (`node --test <file>`, `--test-name-pattern`): sono opzioni standard di Node che Claude conosce già; elencarle allunga il file senza aggiungere informazioni specifiche del progetto.
- **Le note specifiche di implementazione** (il controllo `require.main === module` in `server.js`, la conversione di `req.params.id` con `Number()`, lo store condiviso tra i test): si ricavano leggendo il codice, che resta la fonte aggiornata. Se cambiassero, una copia in `CLAUDE.md` diventerebbe sbagliata.

In generale: un `CLAUDE.md` corto viene seguito meglio, perché le istruzioni importanti non si perdono tra i dettagli.

## Permessi (`.claude/settings.json`)

Il file è condiviso nel repo, quindi le regole valgono per chiunque usi Claude Code su questo progetto.

**Consentiti senza conferma** (`allow`):

- `npm test`, `npm run lint`: sono i controlli che la CI esegue. Non modificano nulla e Claude li lancia spesso, quindi una conferma ogni volta sarebbe solo un'interruzione. È consentita anche la forma con argomenti, ad esempio `npm test -- --test-name-pattern=...`.
- `npm run dev`: avvia l'API in locale su http://localhost:3000, utile per provare le modifiche.
- `git status`, `git diff` (anche con argomenti, ad esempio `--staged`): servono solo a leggere lo stato del repo.

**Vietati** (`deny`, prevalgono sempre su `allow`):

- Lettura di `.env`: potrebbe contenere segreti. `.env.example` resta leggibile perché è un modello senza valori reali.
- `git push`: pubblicare sul remote è un'azione verso l'esterno e difficile da annullare, quindi la fa sempre una persona.
- `rm -rf`: una cancellazione ricorsiva e forzata non si può annullare.

## Limiti

Le regole su Bash confrontano il prefisso del comando. Varianti come `rm -fr` o `rm -r -f` non coincidono con `rm -rf` e quindi non vengono bloccate: per quelle Claude Code chiede comunque conferma, perché non sono nella lista `allow`. Anche la regola `Read(./.env)` blocca solo lo strumento Read, non un `cat .env` lanciato da Bash, che però richiede a sua volta una conferma.

## Verifica

- `/memory` ha mostrato il `CLAUDE.md` del progetto tra i file di memoria caricati.
- `/permissions` ha mostrato le regole `allow` e `deny` definite in `.claude/settings.json`.
