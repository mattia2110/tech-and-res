# Formula demografica

Meccanica implementata con il progresso memorizzato sul paese e una journal entry permanente.

## Formula finale

Un'unica barra `X` tra 0 e 900 determina la fase `x = min(8, floor(X/100))` e il relativo modificatore `demographic_stage_x`. Le fasi occupano intervalli di 100 punti; 900 appartiene alla fase 8.

```text
u     = X / 100
k     = clamp((anno - Ks) / Kp, kmin, kmax)
D     = ((SoL(t) / SoL(t-H) - 1) + (PILpc(t) / PILpc(t-H) - 1)) / 2
A(u)  = min(u / S, 1) * (C - u)
drift = Kd * (Ld * L + min(Ls * SoL, Lm) - u + k)
econ  = Ke * L * A(u) * (max(D / D0, Lr) - 1)
F     = drift + econ
dX    = clamp(F + extra, Lo, Hi)
X(t+1)= clamp(X(t) + dX, 0, 900)
```

`L` è la literacy tra 0 e 1; `F` indica i punti aggiunti ogni anno. `D` è la media delle variazioni relative sul periodo: 0,05 significa 5%, senza annualizzazione.

Con i valori predefiniti:

```text
k     = clamp((anno - 1880) / 30, 0, 5)
drift = 1.5 * (2L + min(0.15 * SoL, 3) - u + k)
econ  = 3 * L * min(u / 3, 1) * (6 - u) * (max(D / 0.04, -0.5) - 1)
dX    = clamp(drift + econ + extra, -5, +15)
```

### Simboli

| Simbolo | Significato |
|---|---|
| `t`, `t-H` | Pulse corrente e campione più vecchio della finestra, `H` anni prima. |
| `u` | `X` espresso in fasi: 1 equivale a una fase. |
| `k` | Epoca: alza nel tempo la fase verso cui tende il drift. |
| `SoL` | SoL medio del paese (`average_sol`); `SoL(t)` è il valore al pulse corrente. |
| `PILpc(t)` | PIL pro capite (`get_country_gdp_per`). |
| `A(u)` | Peso dell'economia per fase: parte da zero, si annulla a `u=C` e oltre cambia segno; `S` ne smorza le fasi iniziali. |
| `drift` | Richiamo verso `u*`, determinato da literacy, SoL ed epoca. |
| `econ` | Spinta economica: positiva con `D > D0`, negativa sotto, con verso invertito oltre `C`. La recessione pesa fino a `D = Lr*D0` (-2%), dove il fattore tocca `Lr - 1 = -1,5`. |
| `extra` | Correttivi additivi di `ztr_demography_extra_change`: sanità (+), società tradizionale, schiavitù, Nord America e immigrazione (-). |
| `dX` | Variazione annua applicata a `X`. |

## Pannello di controllo

| Parametro | Default | Effetto aumentando il valore |
|---|---:|---|
| `Kd` — scala del drift | 1,5 | Accelera sia l'avanzamento sia il rientro verso `u*`, senza spostare `u*`. |
| `Ld` — peso della literacy nel drift | 2 | Sposta in avanti `u*` per i paesi alfabetizzati. |
| `Ls` — peso del SoL nel drift | 0,15 | Sposta in avanti `u*` per i paesi più ricchi: +1,5 fasi ogni 10 punti di SoL, fino a `Lm`. |
| `Lm` — contributo massimo del SoL | 3 | Alza il tetto del SoL nel drift; con i default si raggiunge a SoL 20. |
| `Ke` — velocità economica | 3 | Rafforza la risposta alla crescita, in entrambi i versi, senza toccare il drift. |
| `D0` — soglia di crescita | 0,04 | Alza la crescita quinquennale richiesta per annullare il termine tra parentesi; la stagnazione resta invariata. Deve essere positiva (minimo tecnico 0,001). |
| `Lr` — pavimento della recessione | -0,5 | Più vicino a 0 riduce la penalità extra della recessione: a 0 si torna a trattare ogni `D` negativo come stagnazione. |
| `H` — finestra in anni | 5 | Fissa nel buffer storico; cambiarla richiede adattare campioni e aggiornamento, oltre a ritarare `D0`. |
| `S` — saturazione iniziale | 3 | Riduce il peso economico iniziale e ne ritarda la saturazione. |
| `C` — inversione | 6 | Sposta più avanti il cambio di segno economico, uguale per tutti i paesi. |
| `Hi` — guadagno massimo | +15 | Permette avanzamenti annuali più rapidi prima della saturazione. |
| `Lo` — perdita massima | -5 | Alza il pavimento annuale e riduce la regressione consentita: -5 significa al massimo 5 punti persi l'anno. |
| `Ks` — anno d'inizio dell'epoca | 1880 | Ritarda la crescita di `k`: prima di questo anno `k = kmin`. |
| `Kp` — anni per un punto di epoca | 30 | Rallenta la crescita di `k`. |
| `kmin` — epoca minima | 0 | Alza la soglia del drift a inizio partita. |
| `kmax` — epoca massima | 5 | Alza la soglia del drift a fine partita; si raggiunge nel 2030, poi `k` resta fermo fino al 2036. |

Per il bilanciamento ordinario modificare soprattutto `Kd`, `Ld`, `Ls` e `Ke`. `D0` sostituisce il precedente prodotto `Q*G`: `D0=1/(Q*G)`. Con `Ld=5` e `Ls=0` si torna al drift basato solo sulla literacy.

I parametri sono in `common/script_values/ztr_demography_values.txt`: `Kd=drift_scale`, `Ld=drift_literacy`, `Ls=drift_sol`, `Lm=drift_sol_max`, `Ke=speed`, `D0=growth_threshold`, `Lr=recession_floor`, `S=saturation`, `C=turning_point`, `Hi=max_gain`, `Lo=max_loss`, `Ks=era_start`, `Kp=era_step`, `kmin=era_min`, `kmax=era_max`, tutti con prefisso `ztr_demography_`. L'epoca `k` è `ztr_demography_era`.

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

Il drift si annulla a `u* = Ld*L + min(Ls*SoL, Lm) + k`, cioè `u* = 2L + min(0,15*SoL, 3) + k`, e oltre quel punto spinge indietro. Non è un tetto alla fase raggiungibile: economia ed extra possono spostare l'equilibrio totale. `k` e SoL sono continui, quindi la soglia si muove gradualmente invece di saltare a date fisse.

Il valore di `X` a cui il drift si annulla è la somma di due parti. La prima dipende da literacy e SoL:

| Literacy | SoL 5 | SoL 10 | SoL 20 e oltre |
|---|---:|---:|---:|
| 0% | 75 | 150 | 300 |
| 50% | 175 | 250 | 400 |
| 100% | 275 | 350 | 500 |

La seconda è l'epoca, `100*k`:

| 1836–1880 | 1900 | 1930 | 1950 | 1980 | dal 2030 |
|---:|---:|---:|---:|---:|---:|
| 0 | 67 | 167 | 233 | 333 | 500 |

Per esempio, un paese con literacy 100% e SoL 20 o più tende a 900 dal 2000, il massimo di `X`. Il tetto `Lm` lascia al SoL solo il compito di distinguere i paesi poveri: oltre SoL 20 contano literacy, epoca ed economia.

### Partenze storiche europee

Con `k=0` la soglia a inizio partita è `200*L + min(15*SoL, 300)`. Le tre transizioni europee precoci sono assegnate per tag. Il movimento effettivo dipende anche da economia ed extra:

| | literacy iniziale | SoL iniziale | soglia 1836 | partenza | drift | totale con D=0, senza extra |
|---|---:|---:|---:|---:|---:|---:|
| Francia | 47% | 11,4 | 265 | 200 | +0,97 | -2,79 |
| Gran Bretagna | 51% | 9,8 | 249 | 150 | +1,49 | -1,96 |
| Spagna | 27% | 9,4 | 195 | 125 | +1,05 | -0,55 |

Literacy e SoL sono quelli del salvataggio di settembre 1836. Tutte e tre partono sotto la propria soglia: la Francia di 65 punti, la Gran Bretagna di 99, la Spagna di 70; un eventuale sorpasso dipende dalla crescita e dagli extra. Con `D=0` tutte e tre arretrano: per avanzare serve crescita economica.

Il termine economico sposta il punto di riposo effettivo: sopra `X=100*C=600` è positivo con `D=0`, quindi un paese stagnante e avanzato scivola oltre la soglia del solo drift. L'inversione è la stessa per tutti i paesi: un paese povero in forte crescita si ferma prima della fase 6 come uno ricco, invece di superarlo.

### Regressione ed equilibri

La regressione è intenzionale: non si limita `F` a zero. Sommando il termine economico, un paese stagnante si ferma dove `F = 0`: nel 1836 attorno a `X=93` con literacy 50%, SoL 10 e senza extra, mantenendo fissi anno, literacy e SoL. Oltre `C` il termine economico cambia segno, quindi la crescita frena il declino mentre la stagnazione lo accompagna. La recessione aggiunge penalità fino a `D = -2%`; oltre, il fattore resta a -1,5.

In stagnazione e per `u>=S`, la pendenza di `F` rispetto a `u` è `-Kd + Ke*L`, cioè `-1,5 + 3L`: è positiva oltre il 50% di literacy; in recessione piena diventa `-1,5 + 4,5L`, positiva oltre il 33%. In quel caso il declino accelera con l'aumento della fase e prosegue fino a 900, frenato solo dal limite annuale. Gli extra possono cambiare alle soglie di fase.

I limiti annuali impongono almeno 6,7 anni per percorrere 100 punti in avanti e 20 all'indietro. Si applicano alla somma di drift, economia ed extra: anche una crescita economica forte può attivarli.

## File della meccanica

| File | Contenuto |
|---|---|
| `common/script_values/ztr_demography_values.txt` | Pannello di controllo, formula, extra e valori derivati senza memoria persistente. |
| `common/scripted_effects/ztr_demography_scripts.txt` | Inizializzazione, aggiornamento annuale, eredità e sincronizzazione della barra. |
| `common/on_actions/ztr_demography_on_actions.txt` | Ciclo di vita dei paesi; i pulse sono registrati in `ztr_on_actions.txt`. |
| `common/journal_entries/ztr_je_population.txt` | `je_demography`. |
| `common/messages/ztr_demography_messages.txt` | Notifiche di cambio fase. |

L'aggiornamento annuale sostituisce l'unico modificatore di fase e invia una notifica nel feed, senza eventi né popup: altrimenti una regressione resterebbe invisibile.
