# Formula demografica

Specifica della nuova meccanica; non ancora implementata.

## Formula finale

Un'unica barra `X` tra 0 e 900 determina la fase `x = min(8, floor(X/100))` e il relativo modificatore `demographic_stage_x`. Le fasi occupano intervalli di 100 punti; 900 appartiene alla fase 8.

```text
u = X / 100
D = ((SoL(t) / SoL(t-H) - 1) + (PILpc(t) / PILpc(t-H) - 1)) / 2
A(u) = min(u / S, 1) * (C - u)
F = K * L * [B + A(u) * (Q * G * max(D, 0) - 1)]
X(t+1) = clamp(X(t) + F, 0, 900)
```

`L` è la literacy tra 0 e 1; `F` indica i punti aggiunti ogni anno. `D` è la media delle variazioni relative sul periodo: 0,05 significa 5%, senza annualizzazione.

Con i valori predefiniti:

```text
F = 5 * L * [1 + min(u / 5, 1) * (6 - u) * (20 * max(D, 0) - 1)]
```

La regressione è intenzionale: non si limita `F` a zero. Per esempio, a `X=300`, literacy 50% e `D<=0`, la barra perde 2 punti annui. Dopo `X=600`, il termine economico cambia segno: la stagnazione accelera il declino, mentre la crescita può contrastarlo. Tutti i valori negativi di `D` sono trattati come zero.

## Pannello di controllo

| Parametro | Default | Effetto aumentando il valore |
|---|---:|---|
| `B` — drift | 1 | Favorisce l'avanzamento e contrasta le regressioni; il contributo effettivo è `K*L*B`. |
| `K` — velocità | 5 | Accelera avanzamento e regressione, senza cambiare le soglie di equilibrio. |
| `G` — risposta alla crescita | 1 | Rafforza l'effetto della crescita positiva; non cambia stagnazione e recessione. |
| `Q` — scala economica | 20 | Come `G`: il termine economico si annulla a `D=1/(Q*G)`, ma resta il drift. |
| `H` — finestra in anni | 5 | Misura la crescita su un periodo più lungo; richiede ritarare `Q` o `G`. |
| `S` — saturazione iniziale | 5 | Riduce il peso economico iniziale e ne ritarda la saturazione. |
| `C` — inversione | 6 | Sposta più avanti il cambio di segno, che avviene a `X=100*C`. |

Per il bilanciamento ordinario modificare soprattutto `B`, `K` e `G`. `Q` e `G` compaiono solo come prodotto: tenerne uno fisso.
