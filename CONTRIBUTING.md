# Come si contribuisce

Questo file vale per ogni repository di YoZoLabs che non ne ha uno proprio: GitHub lo mostra al suo posto. Un repository con un proprio `CONTRIBUTING.md` lo sostituisce per intero, quindi ogni regola qui sotto vale **salvo che il file del repository disponga altrimenti**.

Le regole valgono allo stesso modo per chi lavora a mano e per gli agenti AI: una issue aperta da un agente da riga di comando deve essere indistinguibile da una aperta dal browser.

## Lingue

Chi legge trova l'italiano, il codice parla inglese.

| In italiano                                                | In inglese                                             |
| ---------------------------------------------------------- | ------------------------------------------------------ |
| Documenti e commenti nel codice                            | Identificatori, nomi di file e di cartelle             |
| Titolo e corpo di issue e PR                               | Nomi di job e di input di workflow e azioni            |
| Descrizione e corpo del commit (dopo `tipo(scope):`)       | Tipo e scope del commit (`feat`, `fix`, `docs(M-XXX)`) |
| Descrizioni delle etichette, testi dei moduli e dei preset | Nomi delle etichette e dei tipi di issue               |

I testi che scrive un bot — titolo e corpo delle PR di Renovate — restano quelli del bot: il controllo sul titolo guarda soltanto il tipo.

## Issue

### Tipi

Ogni issue ha **un tipo**, nel campo _Type_ della issue:

| Tipo       | Quando                                                                                                                                                   |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Bug`      | Il sistema si comporta in modo diverso da quello atteso                                                                                                  |
| `Feature`  | Una capacità nuova, o il miglioramento di una esistente                                                                                                  |
| `Task`     | Lavoro che non cambia il comportamento: manutenzione, strumenti, documenti, decisioni, fasi di consegna                                                  |
| `Security` | Un rilievo di sicurezza interno: difesa in profondità, configurazione, verifica. Una vulnerabilità sfruttabile segue invece [SECURITY.md](./SECURITY.md) |

`gh issue create` non imposta il tipo: lo si sceglie nel campo _Type_ della pagina della issue, oppure via GraphQL con `createIssue` (argomento `issueTypeId`) e `updateIssueIssueType`.

### Etichette

Due, indipendenti dal tipo. Il tipo non si ripete in un'etichetta.

| Etichetta      | Significato                                                      |
| -------------- | ---------------------------------------------------------------- |
| `blocked`      | In attesa di una persona, di una decisione o di un fatto esterno |
| `dependencies` | Aggiorna una dipendenza                                          |

### Titolo

Descrive **l'esito osservabile**, non l'attività: `[M-XXX] la ricevuta arriva anche quando l'indirizzo contiene lettere accentate`, non `sistemare le email`. Il prefisso fra parentesi quadre lo sceglie il repository — un modulo, un cliente — e lo definisce il suo `CONTRIBUTING.md`.

### Corpo

Il corpo è fatto di sezioni `### Titolo`, nell'ordine della tabella e con i titoli scritti esattamente così: sono le stesse che producono i moduli di [.github/ISSUE_TEMPLATE/](./.github/ISSUE_TEMPLATE/), uno per tipo. Le sezioni in **grassetto** sono obbligatorie; le altre si omettono quando non hanno niente da dire.

| Tipo         | Sezioni                                                                                                                                         |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `Bug`        | **Comportamento osservato** · **Comportamento atteso** · Come riprodurre · **Criteri di accettazione** · File toccati e documento da aggiornare |
| `Feature`    | **Problema** · **Soluzione proposta** · Alternative considerate · **Criteri di accettazione** · File toccati e documento da aggiornare          |
| `Task`       | **Contesto** · **Esito atteso** · **Criteri di accettazione** · File toccati e documento da aggiornare                                          |
| `Security`   | **Rilievo** · **Rischio** · **Criteri di chiusura** · File toccati e documento da aggiornare                                                    |
| `Task` madre | **Obiettivo** · **Cosa include** · Cosa non include · **Criteri di completamento**                                                              |

- **Comportamento osservato**, **Contesto**, **Rilievo**: in un repository di codice almeno un percorso reale in forma `file:riga`; altrove un link o un fatto verificabile. Mai un rilievo generico.
- **Problema** viene prima di **Soluzione proposta**: una `Feature` dice che cosa non funziona per chi la chiede, poi come risolverlo.
- **Criteri di accettazione**: verificabili — un comando, un test, uno screenshot. Chi chiude la issue li ha visti passare.
- **Criteri di chiusura** di una `Security`: chi chiude la issue e con quale prova. Un rilievo senza criterio di chiusura resta aperto per sempre, e il registro diventa un elenco di rischi accettati senza che nessuno l'abbia deciso.
- **File toccati e documento da aggiornare**: i file che cambieranno e il documento che possiede l'informazione toccata.

### Blocchi

- Una issue che aspetta **un'altra issue** lo dichiara con la relazione _Blocked by_ nella pagina della issue: GitHub la mostra da entrambi i lati e la rende filtrabile. Il corpo non la ripete.
- Una issue che aspetta **una persona, una decisione o un fatto esterno** porta l'etichetta `blocked` e una sezione `### Bloccato da` con chi o che cosa si attende.

### Issue madri

Una fase di consegna, o un lavoro che attraversa più repository, è una issue `Task` madre: le parti sono le sue _sub-issue_, anche in repository diversi della stessa organizzazione. L'avanzamento lo calcola GitHub dalle sub-issue chiuse, e il corpo della madre non lo ripete.

### Che cosa non entra in una issue

Testi di contratti, dati di clienti, credenziali. Per un contratto la issue riporta la decisione e il link al documento, che resta dove è archiviato.

## Branch e pull request

Nei repository privati del piano gratuito di GitHub la piattaforma non applica regole di branch: le regole di questa sezione reggono sulla disciplina di chi lavora.

- **Una PR = un branch.** Due persone possono lavorare sullo stesso branch. Su `main` non si fa mai push diretto.
- **Nome del branch**: comincia col tipo del commit — `feat/…`, `fix/…`, `docs/…`, `chore/…`.
- **Titolo della PR = Conventional Commits**: `tipo(scope): descrizione`, con tipo e scope in inglese e descrizione in italiano. È il titolo che arriva su `main` con qualunque metodo di fusione, e nei repository che chiamano i controlli di igiene di `.github` lo verifica la CI.
- **Metodo di fusione**: merge commit o squash, mai rebase. _Create a merge commit_ per una PR fatta di più passi, così ogni passo resta su `main` come punto di ritorno; _Squash and merge_ per una PR di un solo commit e per quelle dei bot. La fusione con rebase è disattivata: ricreerebbe su `main` i commit con SHA diversi da quelli del branch.
- **Prima di fondere**: la CI verde deve riguardare il codice che entra. Se `main` è andato avanti dopo l'ultima esecuzione verde, _Update branch_, poi di nuovo verde, poi si fonde.

### Branch condiviso

**Mai `rebase` né `push --force` su un branch su cui lavora anche un altro**: riscrivono la storia e distruggono il lavoro altrui. `main` entra nel branch con un merge.

```bash
# chi apre il branch
git switch main && git pull
git switch -c feat/descrizione-breve
git push -u origin feat/descrizione-breve

# chi si aggiunge
git fetch origin && git switch feat/descrizione-breve

# prima di modificare, ogni giorno
git pull
git merge origin/main   # porta dentro main; risolvi i conflitti e committa
```

## Commit e hook

- **Conventional Commits**: `tipo(scope): descrizione`, con la descrizione in italiano, al presente, che dice l'effetto — `fix: la ricevuta si genera anche senza email del cliente`.
- **Un commit fa una cosa.** Una PR a più passi ha un commit per passo, ognuno verificato prima di passare al successivo.
- **Mai `--no-verify`.** Se un hook blocca, il messaggio dice che cosa correggere; aggirarlo sposta soltanto la scoperta più avanti.
- **Il pre-commit sta sotto i 6 secondi** su un commit tipico. È la barriera che ferma per prima, e un hook lento finisce aggirato: i controlli lenti — lint con le informazioni di tipo, test completi — girano in CI.

## Documentazione

- **Un solo proprietario per informazione.** Chi la possiede la scrive per esteso; gli altri documenti mettono una riga e un link, mai una copia.
- **Niente file di piano.** `PLAN.md`, `TODO.md`, `NOTES.md`, `ROADMAP.md` e simili non entrano in un repository: il registro dei lavori sono le issue.
- **I documenti descrivono il sistema com'è.** Niente avanzamento (✅, caselle da spuntare), niente formule temporali («già», «attualmente», «per ora», «prossimamente», «non ancora»), niente cronaca delle decisioni: la storia sta in git. Si scrive la regola in vigore, al presente.
- **Diagrammi in mermaid**, che GitHub disegna da sé; ASCII solo per gli alberi di directory.
- **Link**: fra file si linka il file, mai un `#anchor` — i titoli cambiano e l'ancora muore in silenzio; la sezione si nomina in prosa. Dentro lo stesso file gli anchor sono ammessi. Un file fuori dal repository si linka con l'URL completo se è su GitHub, altrimenti si nomina in testo semplice.

## Sicurezza e comportamento

Le vulnerabilità si segnalano come dice [SECURITY.md](./SECURITY.md). Chi partecipa segue il [codice di comportamento](./CODE_OF_CONDUCT.md).
