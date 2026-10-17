# Formula demografica

## Basi e significato del modello

Tech & Res distingue tre componenti della demografia: **condizioni di vita**, **transizione della natalità** e **progresso medico**. Indichiamo con `B` il tasso di natalità e con `M` il tasso di mortalità. Il saldo naturale dipende dalla loro differenza; la migrazione modifica separatamente la popolazione.

1. **Condizioni di vita — B e M in funzione del SoL.** Le curve in `common/defines/ztr_defines.txt` descrivono la componente di natalità e mortalità legata alle condizioni materiali: povertà, rischio di fame e sopravvivenza. Nel significato attribuito dal modello, condizioni migliori riducono la mortalità e la necessità di avere più figli per compensare l'incertezza della sopravvivenza. Questo è il compito delle curve di base; la transizione demografica complessiva è affidata agli stadi. Rispetto all'impianto vanilla basato sulle curve SoL e sui correttivi come alfabetizzazione e disoccupazione, il centro della simulazione viene quindi spostato sul sistema descritto sotto. I correttivi del motore continuano ad applicarsi dove previsti: questa distinzione concettuale non li disattiva.
2. **Evoluzione demografica — riduzione di B.** Il progresso nazionale `X` rappresenta la fase della transizione della natalità. Gli stadi applicano un modificatore di natalità progressivamente più basso: dagli stadi iniziali ad alta natalità a quelli avanzati a bassa natalità. Il modello usa alfabetizzazione, SoL, epoca, dinamica economica, sviluppo e correttivi sociali per determinare il movimento di `X`. Questi sono fattori della transizione; `X` non è il numero di abitanti, un tasso di crescita della popolazione o una misura dell'età media. Per i tassi demografici, gli stadi agiscono su `B`, non su `M`; gli effetti accessori su forza lavoro e costi scolastici non costituiscono una simulazione per coorti.
3. **Medicina — riduzione di M.** La componente medica è separata dagli stadi: i modificatori globali di medicina scandiscono il progresso generale nel tempo, mentre le tecnologie rappresentano i progressi raggiunti localmente dal paese. Il loro ruolo demografico è ridurre `M`. Nei modificatori globali questo avviene anche attraverso la progressiva rimozione della mortalità aggiuntiva iniziale. Restano inoltre gli altri effetti di leggi, istituzioni e modificatori, comprese le riduzioni di mortalità dei livelli di sviluppo avanzati.

La separazione consente alla mortalità di diminuire prima della natalità, producendo una fase di crescita naturale elevata; quando anche la natalità si riduce, il saldo naturale rallenta e può diventare negativo. Il risultato dipende dalla combinazione effettiva di `B` e `M`: uno stadio avanzato non garantisce da solo un saldo naturale negativo. Analogamente, un correttivo sanitario nella formula di `X` influenza il ritmo della transizione della natalità, mentre il suo effetto sulla mortalità resta nel sistema medico.

**Ambito della taratura avanzata.** La componente aggiuntiva del SoL entra gradualmente tra X=500 e X=600. Il contributo economico negativo è dimezzato solo sotto X=600. L'obiettivo è facilitare l'ingresso nelle fasi avanzate e ridurne il plateau nei paesi in crescita, mantenendo la possibilità di regressione.

La meccanica è implementata con il progresso memorizzato sul paese e una journal entry permanente. I modificatori dei tassi si combinano nel motore: i contributi in punti descritti qui sotto aggiornano `X`, non si sommano direttamente a `B` o `M`.

## Formula finale

Un'unica barra `X` tra 0 e 900 determina la fase `x = min(8, floor(X/100))` e il relativo modificatore `demographic_stage_x`. Le fasi occupano intervalli di 100 punti; 900 appartiene alla fase 8.

```text
u     = X / 100
k     = clamp((anno - Ks) / Kp, kmin, kmax)
D     = ((SoL(t) / SoL(t-H) - 1) + (PILpc(t) / PILpc(t-H) - 1)) / 2
s(y)  = Sm * clamp((anno - Ss) / Sp, 0, 1)
A(u,y)= min(u / (S + s(y)), 1) * (C - u)
P(SoL)= bonus potenziale del tenore di vita (raccordo e iperbole sotto)
w     = clamp(u - 5, 0, 1)
V     = P(SoL) * w
drift = Kd * (Ld * L + min(Ls * SoL, Lm) - u + k) + V
econ  = (Ke / D0) * L * A(u,y) * max(D - D0, (Lr - 1) * D0)
econ  = econ * R se econ < 0 e u < 6
F     = drift + econ
dX    = clamp(F + extra, Lo, Hi)
X(t+1)= clamp(X(t) + dX, 0, 900)
```

`L` è la literacy tra 0 e 1; `F` è il contributo annuo della formula prima degli extra e dei limiti. `D` è la media delle variazioni relative sul periodo: 0,05 significa 5%, senza annualizzazione. `V` è espresso in punti di `X` all'anno: +1,5 equivale a +0,015 a `u`, non a +1,5 fasi. Si aggiunge dopo la moltiplicazione per `Kd`. Il SoL corrente determina P(SoL); la variazione quinquennale di SoL e PIL pro capite determina D. Il contributo diretto del SoL nel drift raggiunge il massimo a 20; il bonus aggiuntivo entra sopra quella soglia.

Con i valori predefiniti:

```text
k     = clamp((anno - 1880) / 30, 0, 5)
V     = P(SoL) * clamp((X - 500) / 100, 0, 1)
drift = 1.5 * (2L + min(0.15 * SoL, 3) - u + k) + V
s(y)  = 3 * clamp((anno - 1836) / 100, 0, 1)
econ  = 75 * L * min(u / (3 + s(y)), 1) * (6 - u) * max(D - 0.04, -0.06)
econ  = econ * 0.5 se econ < 0 e u < 6
dX    = clamp(drift + econ + extra, -5, +15)
```

Il contributo economico negativo viene moltiplicato per R solo sotto X=600; quello positivo resta invariato. Con C=6 e R=0,5, sotto 600 attenua recessione, stagnazione e crescita inferiore al 4%; da 600 il freno della crescita superiore al 4% si applica integralmente. A X=600 il termine economico è zero. Il coefficiente agisce prima della somma con drift ed extra e dei limiti annuali.

La forma compatta usa lo scostamento D-D0 dalla crescita di riferimento. È algebricamente equivalente a `Ke*L*A*(max(D/D0,Lr)-1)` per D0>0; 75 è il rapporto Ke/D0 e -0,06 è (Lr-1)*D0 con i default. Il codice ricava questi valori dai parametri e usa sempre D0 effettivo = max(growth_threshold, 0,001), anche nella sottrazione e nel limite inferiore. Durante la raccolta dello storico il contributo economico resta zero.

### Componente aggiuntiva del SoL

Il bonus potenziale usa il SoL medio nazionale:

```text
P(SoL) = 0                                se SoL <= 20
P(SoL) = 1.5 * (SoL - 20) / 5             se 20 < SoL < 25
P(SoL) = min(1.5, 10 / (SoL - 20))        se SoL >= 25
```

| SoL | P(SoL), punti X all'anno |
|---|---:|
| <=20 | 0 |
| 22,5 | 0,75 |
| 25 | 1,5 |
| 30 | 1 |
| 35 | circa 0,667 |
| 40 | 0,5 |
| 75 | circa 0,182 |

Il raccordo cresce da zero a 1,5 fra SoL 20 e 25. L'iperbole resta limitata a 1,5 fino a SoL circa 26,667, poi diminuisce e tende a zero senza diventare negativa. La divisione avviene solo da SoL 25, evitando la singolarità a SoL 20. Con i default la funzione è continua ai raccordi, con cambi di pendenza.

L'attivazione w vale 0 fino a X=500, 0,5 a X=550 e 1 da X=600. Per esempio, con SoL 25 il contributo effettivo è +0,75 punti annui a X=550 e +1,5 da X=600. Il bonus è disponibile anche durante la raccolta dello storico economico.

Il bonus non dipende dal livello del PIL pro capite o dai modificatori `dev_*`. Il PIL pro capite resta nella crescita quinquennale D e nei sistemi economici; i modificatori dev mantengono i loro altri effetti economici e di mortalità.

La soglia di saturazione economica sale da 3 nel 1836 a 4,5 nel 1886 e 6 nel 1936, poi resta a 6. Prima del 1836 resta a 3. Per `u >= S+s(y)` il fattore di saturazione vale 1; sotto la soglia attenua sia la spinta della crescita sia il freno della stagnazione/recessione. Con i default, dal 1936 questo fattore dimezza il peso rispetto al 1836 per `0<u<=3`, mentre da `u>=6` non lo modifica. Il dimezzamento dei contributi negativi è un passaggio separato. Lo stadio demografico e il livello di sviluppo economico sono distinti: quest'ultimo interviene attraverso `V`.

### Simboli

| Simbolo | Significato |
|---|---|
| `t`, `t-H` | Pulse corrente e campione più vecchio della finestra, `H` anni prima. |
| `u` | `X` espresso in fasi: 1 equivale a una fase. |
| `k` | Epoca: alza nel tempo la fase verso cui tende il drift. |
| `SoL` | SoL medio del paese (`average_sol`); `SoL(t)` è il valore al pulse corrente. |
| `PILpc(t)` | PIL pro capite (`get_country_gdp_per`). |
| `A(u,y)` | Peso dell'economia per fase: parte da zero, si annulla a `u=C` e oltre cambia segno; `S+s(y)` ne smorza le fasi iniziali in funzione dell'anno. |
| `s(y)` | Incremento temporale della soglia di saturazione: da 0 a 3 fra 1836 e 1936, poi costante. |
| `P(SoL)` | Bonus potenziale del SoL: raccordo lineare 20–25 e iperbole limitata da 25. |
| `w` | Attivazione lineare da 0 a 1 fra X=500 e X=600. |
| `V` | Bonus annuo effettivo P(SoL)*w, aggiunto al drift dopo la scala `Kd`. |
| `drift` | Richiamo strutturale determinato da literacy, SoL ed epoca, più `V`. |
| `econ` | Spinta economica: positiva con `D > D0`, negativa sotto, con verso invertito oltre `C`. La recessione pesa fino a `D = Lr*D0` (-2%), dove il fattore tocca `Lr - 1 = -1,5`. |
| `R` | Quota del contributo economico negativo conservata sotto X=600: 0,5 con i default; da 600 resta integrale. |
| `extra` | Correttivi additivi di `ztr_demography_extra_change`: sanità (+), società tradizionale, schiavitù, Nord America e immigrazione (-). |
| `dX` | Variazione annua applicata a `X`. |

## Pannello di controllo

| Parametro | Default | Effetto aumentando il valore |
|---|---:|---|
| `Kd` — scala del drift | 1,5 | Rafforza il richiamo strutturale; non moltiplica `V`. Con V=0 non sposta il suo equilibrio; nel tratto da X=600 riduce lo spostamento V/Kd. |
| `Ld` — peso della literacy nel drift | 2 | Sposta in avanti `u*` per i paesi alfabetizzati. |
| `Ls` — peso del SoL nel drift | 0,15 | Sposta in avanti `u*` per i paesi più ricchi: +1,5 fasi ogni 10 punti di SoL, fino a `Lm`. |
| `Lm` — contributo massimo del SoL | 3 | Alza il tetto del SoL nel drift; con i default si raggiunge a SoL 20. |
| `Ke` — velocità economica | 3 | Rafforza la risposta alla crescita, in entrambi i versi, senza toccare il drift. |
| `D0` — soglia di crescita | 0,04 | Alza la crescita quinquennale richiesta per annullare il termine tra parentesi; la stagnazione resta invariata. Deve essere positiva (minimo tecnico 0,001). |
| `Lr` — pavimento della recessione | -0,5 | Più vicino a 0 riduce la penalità extra della recessione: a 0 si torna a trattare ogni `D` negativo come stagnazione. |
| `H` — finestra in anni | 5 | Fissa nel buffer storico; cambiarla richiede adattare campioni e aggiornamento, oltre a ritarare `D0`. |
| `S` — saturazione iniziale | 3 | Riduce il peso economico iniziale e ne ritarda la saturazione. |
| `Ss` — inizio della saturazione temporale | 1836 | Ritarda l'inizio dell'attenuazione economica. |
| `Sp` — durata della transizione temporale | 100 | Rallenta la crescita della soglia; minimo tecnico 1 anno. |
| `Sm` — incremento massimo della saturazione | 3 | Attenua maggiormente la risposta economica degli stadi sotto la soglia. |
| `C` — inversione | 6 | Sposta più avanti il cambio di segno economico, uguale per tutti i paesi. |
| `R` — freno economico | 0,5 | Rafforza il contributo economico negativo sotto X=600; 1 lo conserva integralmente, 0 lo annulla. Da 600 non ha effetto. |
| `Vp` — bonus massimo | 1,5 | Alza il raccordo 20–25 e il limite superiore dell’iperbole; mantenere 0<=Vp<=2 per continuità a SoL 25 con numeratore 10. |
| `Hi` — guadagno massimo | +15 | Permette avanzamenti annuali più rapidi prima della saturazione. |
| `Lo` — perdita massima | -5 | Alza il pavimento annuale e riduce la regressione consentita: -5 significa al massimo 5 punti persi l'anno. |
| `Ks` — anno d'inizio dell'epoca | 1880 | Ritarda la crescita di `k`: prima di questo anno `k = kmin`. |
| `Kp` — anni per un punto di epoca | 30 | Rallenta la crescita di `k`. |
| `kmin` — epoca minima | 0 | Alza la soglia del drift a inizio partita. |
| `kmax` — epoca massima | 5 | Alza la soglia del drift a fine partita; si raggiunge nel 2030, poi `k` resta fermo fino al 2036. |

Per il bilanciamento ordinario modificare soprattutto `Kd`, `Ld`, `Ls` e `Ke`. Con `Ld=5` e `Ls=0` il richiamo strutturale usa literacy ed epoca; il bonus aggiuntivo P(SoL) resta attivo.

I parametri sono in `common/script_values/ztr_demography_values.txt`: `Kd=drift_scale`, `Ld=drift_literacy`, `Ls=drift_sol`, `Lm=drift_sol_max`, `Ke=speed`, `D0=growth_threshold`, `Lr=recession_floor`, `S=saturation`, `Ss=saturation_start`, `Sp=saturation_years`, `Sm=saturation_gain`, `C=turning_point`, `Hi=max_gain`, `Lo=max_loss`, `Ks=era_start`, `Kp=era_step`, `kmin=era_min`, `kmax=era_max`, tutti con prefisso `ztr_demography_`. L'epoca `k` è `ztr_demography_era`.

Anche `R=late_economy_brake` e `Vp=development_peak_gain` usano lo stesso prefisso. Le soglie SoL=20/25, il numeratore iperbolico 10 e l'attivazione X=500–600 sono espliciti nei value `development_potential` e `development_change`. La chiave storica `late_economy_brake` è conservata per il pannello di controllo, ma ora R si applica ai contributi economici negativi solo sotto X=600.

### Variabili persistenti

Tutte sul paese, con prefisso `ztr_demography_`. La fase applicata non è una variabile: si legge dal modificatore `demographic_stage_x`.

| Variabile | Contenuto |
|---|---|
| `progress_var` | `X`. |
| `sol_1_var` … `sol_5_var` | SoL degli ultimi cinque pulse; 1 è il più recente. |
| `gdp_1_var` … `gdp_5_var` | PIL pro capite degli stessi pulse. |
| `history_samples_var` | Campioni raccolti, fino a 5; con 5 si attiva `econ`. |
| `growth_var` | `D` dell'ultimo pulse; assente durante la raccolta, quando `econ = 0`. |
| `last_year_var` | Anno dell'ultimo pulse con dati validi. Al pulse valido successivo ogni anno mancante, saltato o senza SoL e PIL, ripete l'ultima osservazione, fino a cinque. |
| `formula_change_var`, `extra_change_var` | `F` ed `extra` dell'ultimo pulse; la loro somma, limitata a `[Lo, Hi]`, è `dX`. |

### Storico nelle guerre civili

| Caso | Progresso | Storico quinquennale |
|---|---|---|
| Rivoluzione | copiato dal paese d'origine | copiato dal paese d'origine |
| Secessione | copiato dal paese d'origine | nuovo, parte dall'anno corrente |
| Vincitore di una guerra civile, compreso il paese d'origine che reprime la rivolta | nessun intervento, conservato | nessun intervento, conservato |
| Paese nuovo, liberato o ricreato da evento | partenza storica o imposta | nuovo |

SoL e PIL pro capite restano confrontabili dopo i cambi di territorio di una guerra civile, quindi azzerarli costerebbe cinque anni senza termine economico.

### Il punto di equilibrio del drift

Il drift strutturale, prima del bonus di sviluppo, si annulla a `u0 = Ld*L + min(Ls*SoL, Lm) + k`, cioè `u0 = 2L + min(0,15*SoL, 3) + k`. Con SoL fisso il drift completo ha tre tratti:

- Sotto X=500: `drift = Kd*(u0-u)`; riferimento `u*=u0`.
- Fra 500 e 600: `drift = Kd*(u0-u) + P(SoL)*(u-5)`; riferimento `u*=(Kd*u0-5*P(SoL))/(Kd-P(SoL))`, se il denominatore non è zero.
- Da 600: `drift = Kd*(u0-u) + P(SoL)`; riferimento `u*=u0+P(SoL)/Kd`.

Ogni riferimento è un equilibrio del tratto soltanto se vi appartiene; altrimenti è un'estrapolazione. Fra 500 e 600, se P(SoL)=Kd, il drift è costante: se non è zero non esiste uno zero nel tratto, mentre se è zero ogni punto del tratto annulla il drift. La dashboard non mostra un riferimento isolato quando la pendenza è nulla. Il contributo economico e gli extra restano attivi e possono muovere X anche con drift nullo. Il bonus non presenta salti né a 500 né a 600.

Il riferimento strutturale `100*u0`, senza bonus di sviluppo, è la somma di due parti. La prima dipende da literacy e SoL:

| Literacy | SoL 5 | SoL 10 | SoL 20 e oltre |
|---|---:|---:|---:|
| 0% | 75 | 150 | 300 |
| 50% | 175 | 250 | 400 |
| 100% | 275 | 350 | 500 |

La seconda è l'epoca, `100*k`:

| 1836–1880 | 1900 | 1930 | 1950 | 1980 | dal 2030 |
|---:|---:|---:|---:|---:|---:|
| 0 | 67 | 167 | 233 | 333 | 500 |

Per esempio, un paese con literacy 100% e SoL 20 o più ha riferimento strutturale 900 nel 2000. Il bonus di sviluppo può alzare ulteriormente il riferimento del drift, mentre X rimane limitato a 900; l'equilibrio complessivo dipende anche da economia ed extra. Il tetto `Lm` lascia al SoL solo il compito di distinguere i paesi poveri: oltre SoL 20 contano literacy, epoca, economia e sviluppo.

### Partenze storiche europee

Con `k=0` la soglia a inizio partita è `200*L + min(15*SoL, 300)`. Le tre transizioni europee precoci sono assegnate per tag. Il movimento effettivo dipende anche da economia ed extra:

| | literacy iniziale | SoL iniziale | soglia 1836 | partenza | drift | totale con D=0, senza extra |
|---|---:|---:|---:|---:|---:|---:|
| Francia | 47% | 11,4 | 265 | 200 | +0,97 | -0,91 |
| Gran Bretagna | 51% | 9,8 | 249 | 150 | +1,49 | -0,24 |
| Spagna | 27% | 9,4 | 195 | 125 | +1,05 | +0,25 |

Literacy e SoL sono quelli del salvataggio di settembre 1836. Tutte e tre partono sotto la propria soglia; un eventuale sorpasso dipende dalla crescita e dagli extra. Con `D=0`, senza extra, Francia e Gran Bretagna arretrano lentamente; la Spagna avanza leggermente grazie al dimezzamento del freno economico.

### Regressione ed equilibri

La regressione è intenzionale: non si limita `F` a zero. Senza extra, un paese stagnante si ferma dove `F = 0`: nel 1836 attorno a `X=142` con literacy 50%, SoL 10, mantenendo fissi anno, literacy e SoL. Oltre `C` la crescita frena l'avanzamento verso una natalità più bassa, mentre la stagnazione lo accelera. Il contributo economico negativo è dimezzato con R=0,5 solo sotto X=600. La recessione intensifica la risposta fino a `D = -2%`; oltre, il fattore resta a -1,5.

In stagnazione, per `S+s(y)<=u<6`, senza bonus di sviluppo la pendenza di `F` è `-Kd + 0,5*Ke*L`, cioè `-1,5 + 1,5L`; in recessione piena diventa `-1,5 + 2,25L`. Nel tratto 5<u<6 l'attivazione del bonus aggiunge P(SoL) a questa pendenza, a SoL fisso. La transizione può quindi accelerare mentre il bonus entra a regime.

Da `u>=6`, in stagnazione, la pendenza di `F` rispetto a `u` è `-Kd + Ke*L`, cioè `-1,5 + 3L`: è positiva oltre il 50% di literacy; in recessione piena diventa `-1,5 + 4,5L`, positiva oltre il 33%. Quando il saldo è positivo e le condizioni restano fisse, questa pendenza può accelerare l'avanzamento verso 900, entro il limite annuale. Una pendenza positiva da sola non garantisce un saldo positivo; inoltre gli extra possono cambiare alle soglie di fase.

Da u>=6 il bonus `V=P(SoL)` sposta il livello di F senza cambiarne la pendenza, a SoL fisso. Con anno>=1936, u>=6, D>D0 e C=6, l'equilibrio senza extra è:

```text
u_eq = 6 + (Kd * (u0 - 6) + V) / (Kd + Ke * L * (D / D0 - 1))
```

La formula vale se l'equilibrio appartiene al ramo considerato; X resta limitato a 900. Nel 2000, con literacy 100%, SoL>=20, D=12% ed extra nulli, l'equilibrio è X=660 senza bonus, 680 con SoL=25, circa 673,33 con SoL=30 e 666,67 con SoL=40. R non interviene in questo ramo. Sono equilibri a condizioni congelate, non valori garantiti a una data di partita. Il tempo di avvicinamento e i correttivi sociali restano rilevanti.

I limiti annuali impongono almeno 6,7 anni per percorrere 100 punti in avanti e 20 all'indietro. Si applicano alla somma di drift, economia ed extra: anche una crescita economica forte può attivarli.

## Natalità, mortalità e figli per donna

Valori mostrati, non simulati: non modificano la popolazione e non vengono salvati. Si calcolano solo quando il testo è visibile: nel tooltip della mappa demografica (per ogni paese) e nel tooltip della popolazione in alto. I calcoli usano tassi per 1.000 abitanti all'anno; nascite e morti sono mostrate come valori assoluti annui (B e M × popolazione/1.000, in formato K/M).

`G` è la crescita naturale annua del motore, la stessa dell'UI: `$NETGROWTHRATE|5$` dentro la chiave vanilla `TOOLTIP_COUNTRY_POP_GROWTH`, sovrascritta in `localization/english/replace/`. Senza `|5` la macro arriva arrotondata a 2 decimali (0,0063 → 0,00). Esclude migrazione e caduti in guerra. Il paese per gli script value non viene dal motore ma dal contesto di chi richiama la chiave: la barra in alto fornisce `Country`, il riquadro della mappa solo `State`. Ogni valore è quindi scritto due volte, con `Country` e con `State.GetOwner`: quella senza oggetto resta vuota. Un `datacontext` sul riquadro della mappa rompe la GUI.

```text
B̂ = B0(SoL) * (1 + state_birth_rate_mult)      # include demographic_stage_x
M̂ = M0(SoL) * (1 + state_mortality_mult)       # medicina, leggi, istituzioni
w = B̂ / (B̂ + M̂)
c = 2 * B̂ * M̂ / (B̂ + M̂)
B = max(c + w*G, G, 0)
M = B - G                                       # B - M = G esatto
TFR = B/1000 * 35 / f(stadio)
```

`B0` e `M0` replicano le curve di `common/defines/ztr_defines.txt` al SoL medio (mensili × 12.000): se si modificano le curve, aggiornare `ztr_demography_base_birth_rate` e `ztr_demography_base_death_rate`. I moltiplicatori sono quelli del paese, letti con `modifier:`, con un minimo di 0,1. Restano fuori i correttivi per pop o per stato (literacy, disoccupazione, epidemie, devastazione, lavori pericolosi) e l'errore dovuto all'uso del SoL medio. Questa differenza `G - (B̂ - M̂)` viene distribuita fra B e M in proporzione a B̂ e M̂: una carestia abbassa B e alza M. `c` è il valore di B quando G = 0. I due `max` intervengono solo nei casi estremi (G < -2M̂ circa) e mantengono B e M non negativi.

`f` è la quota *effettiva* di donne fra 15 e 49 anni: comprende anche l'effetto della struttura per età, calibrato sul rapporto TFR/CBR reale. Sale nel dividendo demografico, perché ci sono molte donne giovani adulte, e scende con l'invecchiamento.

| Stadio | f | TFR/CBR | Riferimenti reali |
|---|---:|---:|---|
| 0–1 | 24% | ~146 | Svezia 1800 (CBR 29, TFR 4,1), Niger 2023 (41,9 / 6,06) |
| 2 | 26% | ~135 | Nigeria, Etiopia, Pakistan |
| 3 | 27% | ~130 | Egitto, Pakistan 2023 |
| 4 | 28% | ~125 | India 2010, Messico |
| 5 | 29% | ~121 | India 2023, Bangladesh, Brasile 2015 |
| 6 | 26% | ~135 | Cina 2010, USA 2008 |
| 7 | 22% | ~159 | Francia, Germania |
| 8 | 18,5% | ~189 | Italia 2023 (6,4 / 1,20), Giappone |

Con SoL ≥ 18 e senza altri modificatori: stadio 4 → B̂ 21,1, TFR 2,6; stadio 6 → 13,4 e 1,8; stadio 7 → 9,6 e 1,5; stadio 8 → 6,7 e 1,3.

Limiti: `G` passa per un `CFixedPoint` a 5 decimali, quindi l'arrotondamento è trascurabile. Nelle altre lingue l'override compare solo dopo la sincronizzazione con `--include-replace`.

## File della meccanica

| File | Contenuto |
|---|---|
| `common/script_values/ztr_demography_values.txt` | Pannello di controllo, formula, extra e valori derivati senza memoria persistente. |
| `common/scripted_effects/ztr_demography_scripts.txt` | Inizializzazione, aggiornamento annuale, eredità e sincronizzazione della barra. |
| `common/on_actions/ztr_demography_on_actions.txt` | Ciclo di vita dei paesi; i pulse sono registrati in `ztr_on_actions.txt`. |
| `common/journal_entries/ztr_je_population.txt` | `je_demography`. |
| `common/messages/ztr_demography_messages.txt` | Notifiche di cambio fase. |

L'aggiornamento annuale sostituisce l'unico modificatore di fase e invia una notifica nel feed, senza eventi né popup.
