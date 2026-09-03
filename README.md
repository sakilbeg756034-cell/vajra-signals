# VAJRA signals

Roz ka momentum signal, GitHub ke server par banta hai. **Yahan koi laptop nahi
chahiye.** Google Sheet seedha isi repo se padhti hai.

Code yahan nahi hai — wo [`vajra-market-data`](https://github.com/sakilbeg756034-cell/vajra-market-data)
me hai (`src/vajra_regime/cloud/`). Ye repo sirf **data aur natija** rakhta hai.

---

## Sheet ke liye kya padhna hai

| file | kya hai |
|---|---|
| [`out/latest_signals.csv`](out/latest_signals.csv) | **poora universe (~740 naam), har parameter ke saath** — RANK, SCORE, R12, R6, VOLATILITY, VAM, AGREE, ADTV, STALE_SESSIONS, FROZEN_RATE, ELIGIBLE |
| [`out/universe_current.csv`](out/universe_current.csv) | aaj ke universe ke SYMBOL + ISIN |
| [`out/status.json`](out/status.json) | kis session ka data, kab bana, top-12 kaun |

Seedha link (Sheet isi ko `UrlFetchApp` se padhti hai):

```
https://raw.githubusercontent.com/sakilbeg756034-cell/vajra-signals/main/out/latest_signals.csv
```

**Isme BUY/SELL kahin nahi likha.** Sirf number aur RANK. Kya kharidna hai wo
aapka faisla hai — Sheet aapke apne holdings se milakar dikhati hai.

---

## Roz kya hota hai

Har trading din **20:05 IST** ke aas-paas ([`daily.yml`](.github/workflows/daily.yml)):

1. NSE se us din ka bhavcopy utha kar `state/prices.parquet` me joda jaata hai
2. Corporate action calendar dobara kheencha jaata hai
3. Bhaav point-in-time adjust hote hain, universe banta hai, ranking nikalti hai
4. `out/` commit ho jaata hai

**Fail hone par kuch commit nahi hota** aur purani `out/` file waisi ki waisi
rehti hai. Galat nayi file likhne se purani sahi file behtar hai. GitHub aapko
mail bhej dega.

GitHub ke scheduled job bheed me 10–30 minute late chal sakte hain aur kabhi ek
din chhoot bhi sakta hai — isliye pipeline 45 session tak peeche ka data khud
bhar leti hai. Ek din ka miss koi aapda nahi.

---

## `state/` me kya hai

| file | kya |
|---|---|
| `prices.parquet` | pichhle ~500 session. **Do tarah ki rows ek hi file me** — neeche padho |
| `ca_events.parquet` | poora NSE corporate action calendar + har event ka price factor |
| `history_counts.parquet` | store shuru hone se pehle har naam ne kitne session dekhe the |
| `meta.json` | bootstrap kab hua, aakhri run kab, kitni rows judi |

### Sabse zaroori baat — do tarah ki rows

`prices.parquet` me do alag cheezein baithi hain:

- **bootstrap rows** — `VAJRA_DATA` se aayi hain, **pehle se adjusted**
- **live rows** — NSE se roz aati hain, **as-traded**

Isliye har row par `AdjustedThrough` likha hai: wo tareekh jahan tak us row me
corporate action pehle se lage hue hain. Padhte waqt sirf usse **aage** ke
ex-date wale factor lagte hain.

**Ye column hataana mat.** Iske bina factor dobara lagta hai, aur dobara lagna
dikhta nahi — poori series ek saath scale ho jaati hai aur return waise ke waise
rehte hain. Is project me ye galti do baar ho chuki hai (CUPID +406%, HIRECT ka
R12 52% ki jagah 204%), aur dono baar output bilkul saaf dikh raha tha.

---

## Bharosa kis baat ka

2026-09-02 ko cloud ka jawab laptop ke backtest se milaya gaya:

| | |
|---|---|
| SCORE ka fark | **0.0000000000** |
| rank 1–36 ka fark | **0** |
| aisa naam jo cloud le aur backtest na le | **0** |
| cloud jo naam chhod deta hai | 17, sab liquidity rank ~850 par |

Cloud sakht disha me hai — naam **chhodta** hai, **jodta** nahi.

---

## Agar kuch toot jaye

| dikkat | kya karna |
|---|---|
| Sheet purana data dikha rahi hai | `out/status.json` me `as_of_session` dekho. Purana hai to Actions tab kholo, laal run dhoondho |
| Run laal hai | log padho. `CLOUD SIGNAL BUILD ROKA GAYA` matlab gate ne roka — wajah wahin likhi hogi |
| NSE ne block kar diya | `probe_nse_reachability.yml` chalao. 403/000 aaye to NSE ne cloud IP band kiya |
| 60 din koi commit nahi hua | GitHub scheduled workflow apne aap band kar deta hai. Actions tab se dobara enable karo |

Poora system samajhna ho to `D:\VAJRA SYSTEM GATE\00_START_HERE.md` padho.
