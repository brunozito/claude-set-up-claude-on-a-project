# Note sulla configurazione di Claude Code

## Versione di Node (CLAUDE.md)

La nota diceva "Node 18+", ma `node --watch` e `--test-name-pattern` sono arrivati in Node 18.11, quindi ora dice "18.11+". La versione consigliata resta la 22, quella usata dalla CI. Test e lint passano anche su Node 26.

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
