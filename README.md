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

## Licenza

Il contenuto del repository è sotto licenza MIT: vedi [LICENSE](./LICENSE). `CODE_OF_CONDUCT.md` è la traduzione italiana del Contributor Covenant 2.1, sotto CC BY 4.0.
