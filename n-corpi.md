<!-- omit in toc -->
# Simulazione di un sistema a N-corpi

- [Descrizione del problema](#descrizione-del-problema)
- [Implementazione numerica](#implementazione-numerica)
- [Conservazione dell'energia](#conservazione-dellenergia)
- [Implementazione del progetto](#implementazione-del-progetto)
- [Approfondimenti (opzionali)](#approfondimenti-opzionali)
  - [Gestione delle collisioni](#gestione-delle-collisioni)
  - [Quantità conservate](#quantità-conservate)
  - [Punti di Lagrange](#punti-di-lagrange)
- [Riferimenti utili](#riferimenti-utili)

## Descrizione del problema

La simulazione dei sistemi a N-corpi rappresenta storicamente uno dei problemi
più importanti ed affascinanti dell'astronomia. Il problema consiste nel
simulare la dinamica di un insieme di N corpi che interagiscono
gravitazionalmente fra di loro.

A partire dalle leggi formulate da Newton nel 1687, l'interazione fra due corpi
di massa $m_1$ e $m_2$ è mediata da una forza attrattiva proporzionale a tali
masse e inversamente proporzionale al quadrato della loro distanza

$$
F = G\frac{m_1m_2}{r^2}, \quad r = |\vec{r}_1-\vec{r}_2|
$$

dove $G\approx 6.67\times 10^{-11} \, Nm^2kg^{-2}$ è la costante di gravitazione universale.

Nonostante l'apparente semplicità di questo problema, nel corso dei secoli è
stata trovata una soluzione analitica esclusivamente per sistemi a due corpi,
mentre il problema dei tre (o più) corpi fu studiato per molti anni fino al
raggiungimento della conclusione che non esiste una soluzione generale. Ciò
rende evidente la necessità di sviluppare simulazioni numeriche al fine di
predire le orbite dei pianeti, di modellare la formazione di galassie e di
affrontare numerose altre applicazioni in ambito astronomico.

## Implementazione numerica

Per questo progetto ci limiteremo a utilizzare due dimensioni. Considerando N corpi di massa $m_i$, denominiamo con $\vec{r}_i, \vec{v}_i, \vec{a}_i$ rispettivamente la loro posizione, velocità e accelerazione. Seguendo le leggi della gravitazione sappiamo che in un certo istante di tempo la forza che agisce su ognuna di queste masse è data dalla somma delle forze che le altre masse esercitano su di essa, ossia

$$
\begin{equation*}
\vec F_i = -\sum_{\substack{j=1 \\ j \neq i}}^{N} G \frac{m_i m_j}{|\vec r_i - \vec r_j|^3} (\vec r_i - \vec r_j),
\quad i = 1, \dots, N
\end{equation*}
$$

dove utilizziamo la forma vettoriale della legge di Newton.

Come descritto precedentemente, queste equazioni non sono in genere risolvibili;
di conseguenza bisogna discretizzarle ed implementare un sistema in grado di
simulare il loro comportamento passo dopo passo. A questo proposito introduciamo
un algoritmo chiamato _Velocity Verlet_, che appartiene ad una classe di
algoritmi chiamati integratori simplettici, ossia dei metodi numerici che sono
in grado di risolvere equazioni differenziali come questa, con la cruciale
proprietà di mantenere approssimativamente conservate alcune quantità chiave del
sistema come energia e momento angolare (proprio come ci si aspetta da un
sistema meccanico).

Le equazioni di riferimento da implementare nel progetto per aggiornare il
sistema di un passo (_step_) sono

$$
\begin{align*}
\vec r_i(t+\Delta t) &= \vec r_i(t) + \vec v_i(t)\,\Delta t + \frac{1}{2} \vec a_i(t)\, \Delta t^2, \\
\vec a_i(t+\Delta t) &= -\sum_{\substack{j=1 \\ j \neq i}}^N G \frac{m_j}{\big(|\vec r_j(t+\Delta t)-\vec r_i(t+\Delta t)|^2 + \epsilon^2\big)^{3/2}} (\vec r_i(t+\Delta t)-\vec r_j(t+\Delta t)), \\
\vec v_i(t+\Delta t) &= \vec v_i(t) + \frac{1}{2} \Big(\vec a_i(t) + \vec a_i(t+\Delta t)\Big) \Delta t
\end{align*}
$$

dove $\Delta t$ rappresenta lo step temporale del sistema e generalmente deve essere preso il più piccolo possibile (l'ordine di $0.001-0.01$ è accettabile) compatibilmente con le performance del computer, in modo da migliorare la dinamica.

Abbiamo, inoltre, introdotto una costante $\epsilon=10^{-12}$, che rappresenta un parametro di _softening_ e permette di non avere un valore che esplode a infinito nel caso le posizioni di due corpi coincidano.

**Nota 1:** considerando il corpo i-esimo, andiamo a calcolare direttamente la sua accelerazione, perciò non moltiplichiamo per la sua massa $m_i$ nelle equazioni.

**Nota 2:** Durante il singolo step della simulazione fare attenzione a eseguire le operazioni in questo ordine perché altrimenti la dinamica non funzionerà correttamente.

## Conservazione dell'energia

Per ogni sistema meccanico una delle grandezze più importanti è l'energia, che secondo le leggi di Newton è conservata nel caso il sistema sia isolato.

Per un sistema di N-corpi l'energia cinetica è data dalla somma delle energie cinetiche dei singoli corpi

$$
K = \sum_{i=1}^N \frac{1}{2}m_i v_i^2
$$

mentre l'energia potenziale è ottenuta sommando i contributi dati da ogni coppia di corpi

$$
U = - \sum_{i < j} G \frac{m_i m_j}{\left| \vec{r}_i - \vec{r}_j \right|}
$$

notando che sommiamo su $i<j$ per evitare di considerare le coppie due volte.

Infine, l'energia meccanica totale è data semplicemente dalla somma di questi due termini: $E=K+U$.

Come menzionato in precedenza, il metodo numerico utilizzato per simulare il sistema è in grado di conservare l'energia in modo approssimato, ossia dovreste osservare una oscillazione nel tempo del valore dell'energia attorno al valore vero, senza mai però vederla aumentare o diminuire a dismisura.

Ricordiamo, inoltre, che nei sistemi meccanici isolati sono presenti altre due grandezze conservate: la quantità di moto e il momento angolare.

## Implementazione del progetto

Il corpo principale del progetto consiste nello sviluppare un programma che sia in grado di svolgere la simulazione numerica di un sistema di N-corpi ricevendo come parametri in input il numero N di corpi, le loro masse ed eventualmente il numero di step da eseguire.

A seconda della propria preferenza posizioni e velocità iniziali degli N corpi
possono essere assegnate casualmente (facendo attenzione a selezionare
intervalli accettabili) oppure date in input al programma insieme agli altri
parametri (in tal caso si suggerisce di impostarli tramite un file di
configurazione, dato il gran numero di parametri necessari).

Successivamente si richiede di calcolare l'energia del sistema per ogni step in modo da confermare che sia approssimativamente conservata.

Infine, il programma deve stampare a schermo o effettuare una visualizzazione grafica (ad esempio tramite la libreria SFML) dei valori richiesti.

**Curiosità:** Considerando un sistema di 3 corpi con massa uguale, inizializzati con le posizioni e velocità riportate qui sotto, dovreste osservare la famosa Figure-8, un'orbita speciale scoperta da Chenciner e Montgomery nel 2000.

$$
\begin{align*}
   \vec r_1&=(-0.97000436,\,0.24308753),\quad\vec r_2=(0.97000436,\,-0.24308753),\quad\vec r_3=(0,0) \\
   \vec v_1&=(0.4662036850,\,0.4323657300),\quad\vec v_2=(0.4662036850,\,0.4323657300),\quad\vec v_3=(-0.93240737,\,-0.86473146)
\end{align*}
$$

## Approfondimenti (opzionali)

### Gestione delle collisioni

Al posto di utilizzare il parametro di softening $\epsilon$, si può implementare una gestione delle collisioni più realistica, dove ogni corpo possiede anche un parametro $k_i$ che rappresenta il suo raggio. Poi, successivamente a ogni step, si controlla per ogni coppia di corpi (i, j) se la loro distanza soddisfa

$$
   |\vec{r}_i - \vec{r}_j| \leq k_i+k_j
$$

In tal caso i due corpi collidono e perciò devono essere sostituiti da un unico corpo che possiede come massa la somma delle loro masse $m_i+m_j$, come posizione e velocità rispettivamente il centro di massa dei due corpi e la sua velocità, ossia

$$
\begin{align*}
   \vec{r}_{new} &= \frac{m_i\vec{r}_i+m_j\vec{r}_j}{m_i+m_j} \\
   \vec{v}_{new} &= \frac{m_i\vec{v}_i+m_j\vec{v}_j}{m_i+m_j}
\end{align*}
$$

**Nota:** Questo procedimento rappresenta un urto anelastico, di conseguenza la quantità di moto sarà conservata mentre l'energia del sistema potrà variare.

**Nota:** Implementando questo meccanismo fare attenzione a tenere traccia del corretto numero di corpi presente all'interno della simulazione.

### Quantità conservate

Come menzionato precedentemente, anche la quantità di moto e il momento angolare dovrebbero essere conservati durante la simulazione, quindi può essere utili calcolare tali quantità e tenerne traccia.

La quantità di moto totale si calcola sommando le quantità di moto di ciascun corpo:

$$
\vec{P} = \sum_{i=1}^N m_i \vec{v}_i.
$$

Il momento angolare totale in 2D invece è perpendicolare al piano $(x, y)$ dove si svolge il moto e si ottiene da

$$
L_z = \sum_{i=1}^N m_i(r_{i,x}v_{i,y}-r_{i,y}v_{i,x})
$$

### Punti di Lagrange

Nel problema dei tre corpi ristretto, ossia dove due corpi sono molto più massivi rispetto al terzo (ossia $m_3\ll m_1,m_2$), esistono dei punti spaziali di grande interesse: i punti di Lagrange. Essi rappresentano le posizioni dove il terzo corpo è in grado di restare in un equilibrio gravitazionale rispetto agli altri due corpi. Il classico esempio di questo è il sistema Terra-Sole, i cui punti di Lagrange vengono utilizzati per posizionare satelliti o altri oggetti e minimizzare il consumo di carburante.

I punti di Lagrange per ogni sistema sono cinque: $L_1, L_2, L_3, L_4, L_5$. Ottenerli non è semplice; a [questo link](https://wiki.astroclubiitk.in/theory/celestial%20mechanics/lagrange%20points.html) potete trovare una guida sul loro calcolo. Sarebbe interessate provare a calcolarne alcuni e verificare che sono effettivamente punti di equilibrio.

## Riferimenti utili

- Per avere un'idea del concetto che sta dietro all'integratore Velocity Verlet si può visualizzare [questo video](https://www.youtube.com/watch?v=nCg3aXn5F3M).

- Una panoramica avanzata del problema può essere letta [qui](http://www.scholarpedia.org/article/N-body_simulations_%28gravitational%29).

- In [questo sito](https://trisolarchaos.com/) si può trovare un simulatore 3D del sistema con alcuni preset iniziali, fra cui la Figure-8 menzionata precedentemente.
