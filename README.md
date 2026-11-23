# pr-release

Tiene traccia dei passi manuali che una pull request richiede quando il suo codice arriva in un ambiente, come uno script sui dati, un backfill o un ricalcolo, così che al merge non vengano dimenticati.

Sono due brevi file di testo da mettere nella repo. Li segue qualsiasi agente, di qualunque provider.

## Il problema

A volte una PR richiede di lanciare uno script sul database, prima su dev e poi su prod. Se il passo è scritto solo nella descrizione della PR, al merge nessuno lo vede. E giorni dopo, quando lo stesso codice arriva su prod, nessuno se lo ricorda.

## Come funziona

1. **L'agente di chi sviluppa dichiara il passo.** Se la PR richiede un passo manuale, l'agente aggiunge nella stessa PR un piccolo file nella cartella `release-actions/`. Il file dice in quali ambienti va fatto il passo, cosa fare e quando.
2. **L'agente di chi mergia lo segnala.** Prima del merge mostra alla persona i passi che la PR porta, e segnala se un file è incompleto o se alla PR serve un passo che non ha dichiarato. Non esegue niente di sua iniziativa.
3. **La persona esegue i passi** dopo il deploy, oppure chiede al suo agente di eseguirli.
4. **Il passo si smarca con un commento** sulla PR che ha aggiunto il file:
   `Release action done on dev`, oppure `Release action pending on prod: <motivo>`.

Quando si porta `develop` su `main` funziona allo stesso modo, anche se il rilascio contiene molte PR. Ogni file di azione che `develop` ha e `main` non ha ancora è un passo da fare su prod.

Dopo il merge nella repo non cambia niente: niente caselle da spuntare, niente commit sui branch protetti. La storia di ogni passo, ambiente per ambiente, resta nei commenti della sua PR.

## Cosa entra nella tua repo

```
la-tua-repo/
├── AGENTS.md                      + la sezione "Release actions"
└── release-actions/
    ├── README.md                  le istruzioni per i due ruoli
    └── 482-recalculate-totals.md  un'azione, aggiunta da una PR quando serve
```

Un file di azione è fatto così:

```
Environments: dev, prod

What to do: run `python scripts/recalculate_totals.py` (dry run), check the count, then rerun with `--apply`.
When: after this PR is deployed there and the other repo has published config version 12.
```

I file che finiscono nella repo sono in inglese, come di solito il resto del codice e della documentazione.

## Installazione

Copia il contenuto di `template/` nella radice della tua repo:

1. `template/release-actions/README.md` va in `release-actions/README.md`.
2. `template/AGENTS.md` contiene la sezione "Release actions". Se la repo ha già un `AGENTS.md`, aggiungi la sezione in fondo invece di sostituire il file.
3. Se usi una versione di Claude Code che legge solo `CLAUDE.md`, aggiungi un `CLAUDE.md` con dentro `@AGENTS.md`.

## File

| Percorso | Cos'è |
| --- | --- |
| `README.md` | Questa pagina: cos'è il setup, come funziona, come si installa. |
| `how-it-works.html` | Una pagina di spiegazione con un esempio passo per passo. Scaricala e aprila nel browser. |
| `template/` | Gli unici file da copiare, disposti esattamente come vanno nella tua repo. |
| `template/AGENTS.md` | La sezione da aggiungere al tuo `AGENTS.md`: indica agli agenti dove trovare le istruzioni. |
| `template/release-actions/README.md` | Le istruzioni per chi sviluppa e per chi mergia. |
