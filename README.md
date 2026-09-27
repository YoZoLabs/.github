# YoZoLabs/.github

Il repository comune dell'organizzazione YoZoLabs.

## File di salute di riserva

GitHub mostra questi file in ogni repository dell'organizzazione che non ne ha uno proprio. Un file del repository **sostituisce per intero** quello di qui: i due non si fondono.

| File                                                   | Cosa contiene                                                     |
| ------------------------------------------------------ | ----------------------------------------------------------------- |
| [CONTRIBUTING.md](./CONTRIBUTING.md)                   | Lingue, issue, branch e pull request, commit, documentazione      |
| [SECURITY.md](./SECURITY.md)                           | Come si segnala una vulnerabilità                                 |
| [CODE_OF_CONDUCT.md](./CODE_OF_CONDUCT.md)             | Codice di comportamento (Contributor Covenant 2.1)                |
| [profile/README.md](./profile/README.md)               | La pagina dell'organizzazione su GitHub                           |
| [ISSUE_TEMPLATE/](./ISSUE_TEMPLATE/)                   | Un modulo per tipo di issue: `Bug`, `Feature`, `Task`, `Security` |
| [PULL_REQUEST_TEMPLATE.md](./PULL_REQUEST_TEMPLATE.md) | Il corpo di partenza di ogni pull request                         |

Per i moduli la sostituzione vale per cartella: un repository con una propria `.github/ISSUE_TEMPLATE/` — anche solo un `config.yml` — non mostra nessuno dei moduli di qui.

## Preset di Renovate

[default.json](./default.json) è il preset comune degli aggiornamenti delle dipendenze; ogni regola porta il proprio perché nel campo `description`. Un repository lo adotta con un `renovate.json` di una riga, a patto che l'app Renovate dell'organizzazione lo includa:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["local>YoZoLabs/.github"]
}
```

Le regole del repository si aggiungono sotto `extends` e vincono su quelle del preset.

## CI riusabile

Due componenti, da referenziare sempre con lo **SHA completo** di un tag e la versione in commento — `@<sha> # vX.Y.Z`, mai `@main` né il solo tag, che si può spostare. Lo SHA di un tag:

```bash
gh api repos/YoZoLabs/.github/commits/vX.Y.Z --jq .sha
```

Renovate, col preset qui sopra, apre la PR quando esce un tag nuovo.

### Controlli di igiene

[.github/workflows/hygiene.yml](./.github/workflows/hygiene.yml) è un workflow da chiamare: un job, `checks`, che su GitHub compare come `hygiene / checks`.

| Evento                                  | Titolo della PR | gitleaks                  | actionlint | zizmor |
| --------------------------------------- | --------------- | ------------------------- | ---------- | ------ |
| PR aperta, aggiornata o riaperta        | ✓               | ✓ commit fra base e testa | ✓          | ✓      |
| PR modificata, **cambia il titolo**     | ✓               | ✓ commit fra base e testa | ✓          | ✓      |
| PR modificata, cambia solo il corpo     | —               | —                         | —          | —      |
| Push su `main` (solo questo repository) | —               | ✓ commit del push         | ✓          | ✓      |

- **Titolo della PR**: Conventional Commits, si controlla solo il tipo.
- **gitleaks**: un intervallo vuoto è un errore, non un verde.
- **zizmor**: con gli audit che interrogano GitHub; ogni `uses:` deve essere fissato allo SHA.

Quando cambia il titolo i controlli si ripetono tutti, così un titolo corretto non copre un altro rosso. Quando cambia solo il corpo non parte niente, e l'esecuzione in corso non si annulla. Questi due comportamenti stanno nel workflow **chiamante**, che si copia così com'è:

```yaml
name: hygiene

on:
  pull_request:
    types: [opened, synchronize, reopened, edited]

permissions: {}

# Una modifica al solo corpo della PR ha un gruppo tutto suo: non annulla mai l'esecuzione in corso.
concurrency:
  group: ${{ (github.event.action == 'edited' && !github.event.changes.title) && format('noop-{0}', github.run_id) || format('hygiene-{0}', github.ref) }}
  cancel-in-progress: ${{ github.event_name == 'pull_request' }}

jobs:
  hygiene:
    # Solo il corpo della PR è cambiato: niente da controllare, zero minuti.
    if: github.event.action != 'edited' || github.event.changes.title
    uses: YoZoLabs/.github/.github/workflows/hygiene.yml@<sha> # vX.Y.Z
    permissions:
      contents: read # quelli che chiede il job `checks` di hygiene.yml, non uno di più
      pull-requests: read # il controllo del titolo rilegge la PR via API
```

### Node e pnpm

[actions/setup-node-pnpm](./actions/setup-node-pnpm/action.yml) installa pnpm (versione da `packageManager` in `package.json`) e Node (versione da `.nvmrc`), ripristina lo store di pnpm dalla cache e installa le dipendenze.

```yaml
- uses: actions/checkout@<sha> # vX.Y.Z
  with:
    persist-credentials: false
- uses: YoZoLabs/.github/actions/setup-node-pnpm@<sha> # vX.Y.Z
```

| Input               | Predefinito     | A che serve                                                            |
| ------------------- | --------------- | ---------------------------------------------------------------------- |
| `node-version-file` | `.nvmrc`        | Il file da cui leggere la versione di Node                             |
| `cache-prefix`      | `pnpm-store-v1` | Prefisso della cache dello store: cambiarlo riparte da una cache vuota |
| `install`           | `true`          | `false` si ferma al setup, senza `pnpm install`                        |

## Licenza

Il contenuto del repository è sotto licenza MIT: vedi [LICENSE](./LICENSE). `CODE_OF_CONDUCT.md` è la traduzione italiana del Contributor Covenant 2.1, sotto CC BY 4.0.
