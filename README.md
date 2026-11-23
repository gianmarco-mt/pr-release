# pr-release

Tiene traccia dei passi manuali che una pull request richiede quando il suo codice arriva in un ambiente, come uno script sui dati, un backfill o un ricalcolo, così che al merge non vengano dimenticati.

Sono due brevi file di testo da mettere nella repo. Li segue qualsiasi agente, di qualunque provider.

## Il problema

A volte una PR richiede di lanciare uno script sul database, prima su dev e poi su prod. Se il passo è scritto solo nella descrizione della PR, al merge nessuno lo vede. E giorni dopo, quando lo stesso codice arriva su prod, nessuno se lo ricorda.

## Come funziona

1. **L'agente di chi sviluppa dichiara il passo.** Se la PR richiede un passo manuale, l'agente aggiunge nella stessa PR un file nella cartella `release-actions/`.
2. **L'agente di chi mergia lo segnala.** Prima del merge mostra alla persona i passi che la PR porta. Segnala anche un file incompleto o un passo che la PR non ha dichiarato. Non esegue niente di sua iniziativa.
3. **La persona esegue i passi** dopo il deploy, oppure chiede al suo agente di eseguirli.
4. **L'agente smarca il passo** con un commento sulla PR che ha aggiunto il file.

Dopo il merge nella repo non cambia niente: niente caselle da spuntare, niente commit sui branch protetti. La storia di ogni passo, ambiente per ambiente, resta nei commenti della sua PR.

| Cosa | Dove | Chi lo scrive | Quando |
| --- | --- | --- | --- |
| Istruzioni | `release-actions/README.md` e una sezione in `AGENTS.md` | Chi installa il setup | Una volta per repo |
| Azione di rilascio | `release-actions/<task>-<titolo>.md`, nella stessa PR del codice | L'agente di chi sviluppa | Solo se la PR richiede un passo manuale |
| Smarcamento | Commento su GitHub, sulla PR che ha aggiunto il file | L'agente di chi mergia, quando la persona glielo dice | Dopo che il passo è fatto, o se deve aspettare |

## Il file di azione

Si chiama come il branch senza il tipo: `feat/482-recalculate-totals` diventa `release-actions/482-recalculate-totals.md`. Contiene tre cose:

- **Environments**: in quali ambienti va fatto il passo.
- **What to do**: i comandi esatti, prima la prova e poi l'esecuzione vera.
- **When**: cosa deve succedere prima, per esempio una configurazione pubblicata da un'altra repo.

```
Environments: dev, prod

What to do: run `python scripts/recalculate_totals.py` (dry run), check the count, then rerun with `--apply`.
When: after this PR is deployed there and the other repo has published config version 12.
```

Se il passo può farlo la pipeline, si automatizza e il file non serve. I file non si modificano e non si cancellano: per cambiare un passo già fatto in un ambiente se ne aggiunge uno nuovo.

I file che finiscono nella repo sono in inglese, come di solito il resto del codice e della documentazione.

## Esempio: da una PR a dev, poi a prod

**Merge su develop.** La PR #31 aggiunge `482-recalculate-totals.md`. Prima del merge l'agente di chi mergia scrive:

> La PR #31 aggiunge un'azione di rilascio per dev e prod: ricalcolo dei totali, prima in prova e poi con `--apply`, dopo il deploy e dopo che l'altra repo ha pubblicato la configurazione v12. Dopo il merge va fatta su dev. La lanci tu o la lancio io?

Fatto il passo, l'agente commenta sulla PR #31 con un testo fisso, che si ritrova con una ricerca:

```
Release action done on dev
Release action pending on dev: waiting for config v12
```

**Develop su main.** Su prod si porta develop intero, con tutte le PR mergiate dall'ultimo rilascio. I file che develop ha nella cartella e main non ha ancora sono esattamente i passi da fare su prod:

> Portando develop su main arrivano su prod 2 azioni di rilascio: `482-recalculate-totals.md` (PR #31) e `495-backfill-history.md` (PR #36). Le altre PR del rilascio non hanno passi manuali. Le lanci tu o le lancio io?

Ogni passo si smarca sulla PR che ha aggiunto il suo file, non sulla PR di rilascio: `Release action done on prod` sulla #31, un altro sulla #36.

## Installazione

Copia il contenuto di `template/` nella radice della tua repo:

1. `template/release-actions/README.md` va in `release-actions/README.md`.
2. `template/AGENTS.md` contiene la sezione "Release actions". Se la repo ha già un `AGENTS.md`, aggiungi la sezione in fondo invece di sostituire il file.
3. Se usi una versione di Claude Code che legge solo `CLAUDE.md`, aggiungi un `CLAUDE.md` con dentro `@AGENTS.md`.

```
template/
├── AGENTS.md                 la sezione da aggiungere al tuo AGENTS.md
└── release-actions/
    └── README.md             le istruzioni per chi sviluppa e per chi mergia
```
