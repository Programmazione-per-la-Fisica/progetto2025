<!-- markdownlint-disable-file MD014 MD028 -->

<!-- omit in toc -->
# Installazione di SFML 2.6 su macOS

- [Introduzione](#introduzione)
- [Installazione di SFML 2.6](#installazione-di-sfml-26)

> [!CAUTION]
> Le istruzioni riportate di seguito sono valide **unicamente** per gli
> utilizzatori di macOS, i quali:
>
> - abbiano una versione del sistema operativo compatibile con _Homebrew_
>   (Sonoma, Sequoia, Tahoe);
> - abbiano necessità di utilizzare SFML 2.6 per **collaborare con studenti che
>   utilizzano Ubuntu** (installazione nativa o tramite WSL);
> - incorrano in errori nella compilazione del progetto (non di configurazione
>   dell'area di _build_ o _linking_) imputabili direttamente a SFML.

## Introduzione

Come descritto altrove durante il corso, quest'anno utilizziamo come
**piattaforma di riferimento Ubuntu 24.04**. Pertanto, per lo sviluppo
collaborativo di progetti che fanno uso di librerie esterne (es. SFML), vogliamo
che i diversi ambienti di sviluppo (es. quelli basati su macOS) siano "il più
simile possibile" a quello in Ubuntu 24.04.

Questo significa che **gli utenti macOS che collaborano con utenti
Windows/Linux** devono **installare SFML 2.6**, se ne fanno uso.

> [!IMPORTANT]
> Singoli individui o gruppi che sviluppano il progetto d'esame utilizzando
> unicamente macOS possono avvalersi di SFML 3.0, ma devono **specificarlo
> nella relazione**.

## Installazione di SFML 2.6

All'oggi, brew installa "di default" **SFML 3.0**; permette però anche
l'installazione di versioni di SFML della famiglia di _release_ 2.X.

Per effettuare il _downgrade_ a SFML 2.6 procedete come indicato qui:

```zsh
$ brew remove sfml
...
$ brew install sfml@2
...
```

Potete verificare la correttezza dell'installazione tramite il comando:

```zsh
$ brew info sfml@2
==> sfml@2 ✔: stable 2.6.2 (bottled) [keg-only]
Multi-media library with bindings for multiple languages
https://www.sfml-dev.org/
Installed (on request)
/opt/homebrew/Cellar/sfml@2/2.6.2_1 (954 files, 15.0MB)
...
```

A questo punto, è necessario aggiungere alcune variabili d'ambiente nel file
`.zshrc` che configura il terminale di macOS alla sua apertura.

Per farlo eseguite il comando:

```zsh
$ code ${HOME}/.zshrc
```

e aggiungete la seguente riga in fondo al file appena aperto:

```zsh
export SFML_DIR="/opt/homebrew/opt/sfml@2/"
```

salvate il file, chiudete il terminale e riapritelo per configurarlo
correttamente.
