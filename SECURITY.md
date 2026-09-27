# Sicurezza

Questo file vale per ogni repository di YoZoLabs che non ha un proprio `SECURITY.md`.

## Segnalare una vulnerabilità

**Non aprire una issue pubblica.**

- In un repository **pubblico**: scheda _Security_, pulsante _Report a vulnerability_. La segnalazione resta privata fra te e chi mantiene il repository.
- In ogni altro caso: scrivi a **junliang.zheng01@gmail.com**.

Indica come riprodurre il problema, quale repository e quale superficie tocca, e che impatto ha sui dati.

Rispondiamo il prima possibile. I tempi della correzione e della divulgazione si concordano con chi ha segnalato.

## Fuori ambito

- **Dipendenze di terze parti**: la vulnerabilità si segnala al progetto a monte; poi avvisaci, se tocca anche noi.
- **Servizi di terzi** (GitHub, fornitori cloud): si segnalano al fornitore.

## Registro

I rilievi di sicurezza noti di un repository sono le sue issue di tipo `Security`: è quello, non un file, il registro.

## Segreti

Una chiave finita nella storia di git va considerata compromessa: prima si ruota presso chi l'ha emessa, poi, se serve, si riscrive la storia.
