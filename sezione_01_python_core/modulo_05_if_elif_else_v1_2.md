# 🐍 Modulo 5: if / elif / else

**Sezione:** Python Core  
**Micro-corso:** PY-05  
**Workflow:** DECISIONE  
**Obiettivo:** Costruire logiche decisionali ramificate per gestire scenari multipli

---

## 🔧 Engineering focus

Nel CPF, ogni parametro di processo può trovarsi in stati diversi: normale, allarme basso, allarme alto, trip. Il sistema di controllo deve decidere l'azione corretta in base al valore letto. Una logica decisionale mal costruita può ignorare condizioni critiche o attivare allarmi sbagliati. Python replica esattamente questa struttura con `if/elif/else`.

---

## 🎯 Cosa imparerai

- Usare `if` per decisioni semplici (vero/falso)
- Concatenare `elif` per gestire casi multipli esclusivi
- Usare `else` come fallback per i casi non previsti
- Combinare condizioni con `and`, `or`, `not`

---

## 🎬 Scenario reale

L'operatore del CPF legge il livello del HP Separator V-100. Il sistema deve classificare la lettura secondo le soglie di allarme definite nel Process Control Philosophy:

| Soglia | Valore (mm) | Significato | Azione |
|--------|-------------|-------------|--------|
| LSLL | 1200 | Low-Low Level | TRIP - Chiudi SDV |
| LAL | 1400 | Low Alarm Level | Allarme - Verifica |
| NLL | 1695 | Normal Level | OK |
| LAH | 1900 | High Alarm Level | Allarme - Verifica |
| LSHH | 2100 | High-High Level | TRIP - Attiva scarico |

Il livello letto è **1720 mm**. Qual è lo stato?

---

## 🧠 Concetto Python chiave

### `if` – La decisione base

```python
if condizione:
    # esegui se condizione è True
```

La condizione è un'espressione che Python valuta come `True` o `False`:

```python
livello_mm = 1720
if livello_mm > 1900:
    print("Allarme alto")
```

### `elif` – Casi alternativi

Quando hai più condizioni mutualmente esclusive, usa `elif` (else if):

```python
if livello_mm > 2100:
    print("TRIP - LSHH")
elif livello_mm > 1900:
    print("Allarme LAH")
elif livello_mm < 1200:
    print("TRIP - LSLL")
elif livello_mm < 1400:
    print("Allarme LAL")
else:
    print("Livello OK")
```

**Attenzione:** L'ordine conta! Python valuta le condizioni dall'alto verso il basso e si ferma alla prima vera.

### `else` – Il caso di default

`else` cattura tutto ciò che non è stato intercettato dai rami precedenti:

```python
if pressione_barg > 47:
    stato = "TRIP"
elif pressione_barg > 45:
    stato = "ALLARME"
else:
    stato = "OK"  # tutti gli altri casi
```

### Combinare condizioni

| Operatore | Significato | Esempio |
|-----------|-------------|---------|
| `and` | Entrambe vere | `T > 50 and P > 40` |
| `or` | Almeno una vera | `T > 80 or P > 47` |
| `not` | Inverte il valore | `not is_running` |

```python
# Allarme se pressione E temperatura sono alte
if pressione_barg > 45 and temperatura_C > 70:
    print("Condizione critica")
```

---

## 🧠 PY-05 – Engineering mindset

> "In una catena `if/elif/else`, metti sempre le condizioni più critiche per prime. Un TRIP deve essere valutato prima di un semplice allarme."

---

## 🔧 Esempio Python applicato

```python
# === Sistema di classificazione allarmi livello ===
# HP Separator V-100 - CPF Menzel Lejmat Nord

# Soglie di livello (da Process Control Philosophy)
LSLL_mm = 1200  # Low-Low Level → TRIP
LAL_mm = 1400   # Low Alarm Level
NLL_mm = 1695   # Normal Level
LAH_mm = 1900   # High Alarm Level
LSHH_mm = 2100  # High-High Level → TRIP

# --- Lettura dal campo ---
livello_mm = int(input("Livello HC V-100 [mm]: "))

# --- Classificazione con if/elif/else ---
# IMPORTANTE: le condizioni TRIP vanno per prime!

print()
print(f"Livello letto: {livello_mm} mm")
print("-" * 40)

if livello_mm >= LSHH_mm:
    # Condizione più critica (alta) → valutata per prima
    stato = "TRIP"
    messaggio = f"🚨 LSHH TRIP! Livello {livello_mm} >= {LSHH_mm} mm"
    azione = "Attivare scarico emergenza, verificare LV-401"
    
elif livello_mm >= LAH_mm:
    # Allarme alto (ma non ancora trip)
    stato = "ALLARME"
    messaggio = f"⚠️  LAH - Livello alto: {livello_mm} mm"
    azione = "Verificare valvola di scarico, monitorare trend"
    
elif livello_mm <= LSLL_mm:
    # Condizione più critica (bassa)
    stato = "TRIP"
    messaggio = f"🚨 LSLL TRIP! Livello {livello_mm} <= {LSLL_mm} mm"
    azione = "Chiudere SDV, rischio blow-by gas"
    
elif livello_mm <= LAL_mm:
    # Allarme basso (ma non ancora trip)
    stato = "ALLARME"
    messaggio = f"⚠️  LAL - Livello basso: {livello_mm} mm"
    azione = "Verificare alimentazione, controllare pompe"
    
else:
    # Tutti gli altri casi → livello normale
    stato = "OK"
    messaggio = f"✓ Livello normale: {livello_mm} mm"
    azione = "Nessuna azione richiesta"

# --- Output strutturato ---
print(f"Stato: {stato}")
print(f"Messaggio: {messaggio}")
print(f"Azione: {azione}")

# --- Logica aggiuntiva basata sullo stato ---
if stato == "TRIP":
    print()
    print("=" * 40)
    print("ATTENZIONE: Notificare supervisore di turno!")
    print("=" * 40)
```

**Output esempio (livello = 1720):**
```
Livello letto: 1720 mm
----------------------------------------
Stato: OK
Messaggio: ✓ Livello normale: 1720 mm
Azione: Nessuna azione richiesta
```

**Output esempio (livello = 2150):**
```
Livello letto: 2150 mm
----------------------------------------
Stato: TRIP
Messaggio: 🚨 LSHH TRIP! Livello 2150 >= 2100 mm
Azione: Attivare scarico emergenza, verificare LV-401

========================================
ATTENZIONE: Notificare supervisore di turno!
========================================
```

---

## ✍️ Esercizio per lo studente

Implementa la logica di classificazione per la **pressione** del HP Separator V-100:

| Soglia | Valore (barg) | Stato |
|--------|---------------|-------|
| PSLL | 35.0 | TRIP basso |
| PAL | 38.0 | Allarme basso |
| Normale | 41.0 | OK |
| PAH | 45.0 | Allarme alto |
| PSHH | 47.0 | TRIP alto |

Completa il codice:

```python
# Classificazione pressione HP Separator V-100

# Soglie
PSLL_barg = 35.0
PAL_barg = 38.0
PAH_barg = 45.0
PSHH_barg = 47.0

# Input
pressione_barg = float(input("Pressione V-100 [barg]: "))

# Classificazione
if pressione_barg >= ___:
    stato = "TRIP"
    print(f"🚨 PSHH - Pressione {pressione_barg} barg")
elif ___:
    stato = "ALLARME"
    print(f"⚠️  PAH - Pressione alta")
elif pressione_barg <= ___:
    stato = "TRIP"
    print(f"🚨 PSLL - Pressione {pressione_barg} barg")
elif ___:
    stato = ___
    print(f"⚠️  PAL - Pressione bassa")
else:
    stato = "OK"
    print(f"✓ Pressione normale: {pressione_barg} barg")

# Azione finale
if stato == ___:
    print("Attivare procedura ESD!")
```

---

## ✅ Soluzione commentata

```python
# Classificazione pressione HP Separator V-100

# Soglie (da Process Control Philosophy e Design Criteria)
PSLL_barg = 35.0  # Pressure Switch Low-Low → TRIP
PAL_barg = 38.0   # Pressure Alarm Low
PAH_barg = 45.0   # Pressure Alarm High
PSHH_barg = 47.0  # Pressure Switch High-High → TRIP (= Design Pressure)

# Input operatore
pressione_barg = float(input("Pressione V-100 [barg]: "))

# Classificazione - TRIP per primi!
if pressione_barg >= PSHH_barg:
    stato = "TRIP"
    print(f"🚨 PSHH - Pressione {pressione_barg} barg >= {PSHH_barg}")
elif pressione_barg >= PAH_barg:
    stato = "ALLARME"
    print(f"⚠️  PAH - Pressione alta: {pressione_barg} barg")
elif pressione_barg <= PSLL_barg:
    stato = "TRIP"
    print(f"🚨 PSLL - Pressione {pressione_barg} barg <= {PSLL_barg}")
elif pressione_barg <= PAL_barg:
    stato = "ALLARME"
    print(f"⚠️  PAL - Pressione bassa: {pressione_barg} barg")
else:
    stato = "OK"
    print(f"✓ Pressione normale: {pressione_barg} barg")

# Azione basata sullo stato
if stato == "TRIP":
    print("Attivare procedura ESD!")
```

**Nota:** L'ordine è cruciale. Se mettessimo `pressione_barg >= PAH_barg` prima di `>= PSHH_barg`, una pressione di 48 barg verrebbe classificata come "ALLARME" invece che "TRIP", perché 48 >= 45 è True e Python si fermerebbe lì.

---

## ⚠️ Errori tipici

### 1. Ordine sbagliato delle condizioni

```python
# ❌ SBAGLIATO: allarme prima di trip
if livello >= LAH_mm:      # 1950 >= 1900 → True, si ferma qui!
    print("Allarme")
elif livello >= LSHH_mm:   # Mai raggiunto per livelli > 2100
    print("TRIP")
```

**Sintomo:** I TRIP non vengono mai rilevati  
**Causa:** La condizione meno restrittiva intercetta anche i casi critici  
**Fix:** Metti sempre le condizioni più critiche (TRIP) per prime

### 2. Usare `if` multipli invece di `elif`

```python
# ❌ SBAGLIATO: if multipli
if livello > 2100:
    print("TRIP alto")
if livello > 1900:    # Anche questo è True se livello = 2150!
    print("Allarme alto")
```

**Sintomo:** Stampa sia "TRIP alto" che "Allarme alto"  
**Causa:** Ogni `if` viene valutato indipendentemente  
**Fix:** Usa `elif` per condizioni mutualmente esclusive

### 3. Dimenticare `else`

```python
# ⚠️ RISCHIOSO: nessun else
if pressione > 47:
    stato = "TRIP"
elif pressione > 45:
    stato = "ALLARME"
# E se pressione = 40? stato non è definito!
```

**Sintomo:** `NameError: name 'stato' is not defined`  
**Causa:** Nessun ramo copre il caso "normale"  
**Fix:** Aggiungi sempre `else` per gestire i casi non previsti

---

## 📌 Takeaway

> "In una catena `if/elif/else`, l'ordine delle condizioni definisce la priorità. Le condizioni critiche vanno sempre per prime."

---

## ❓ Verifica finale (5 domande)

1. **(Tecnica)** Qual è la differenza principale tra `if` multipli e una catena `if / elif / else`?
A. Nessuna, sono equivalenti  
B. `elif` è più veloce ma meno chiaro  
C. Con `elif` solo il primo blocco vero viene eseguito  
D. `if` multipli non sono supportati in Python  

2. **(Tecnica)** Quale condizione viene valutata per prima?
```python
if livello > 1900:
    print("ALLARME")
elif livello > 2100:
    print("TRIP")
```
A. `livello > 2100`  
B. Dipende dal valore di `livello`  
C. `livello > 1900`  
D. Nessuna, Python valuta tutte  

3. **(Pratica)** Cosa stampa questo codice se `livello = 2200`?
```python
if livello > 1900:
    print("ALLARME")
elif livello > 2100:
    print("TRIP")
else:
    print("OK")
```
A. `TRIP`  
B. `ALLARME`  
C. `OK`  
D. Genera errore  

4. **(Pratica)** Qual è la correzione giusta per gestire correttamente il TRIP?
A. Invertire l’ordine delle condizioni  
B. Usare due `if` separati  
C. Eliminare `elif`  
D. Aggiungere un `print`  

5. **(Engineering Judgement)** Perché l’ordine delle condizioni è critico in logiche di sicurezza?
A. Per motivi di performance  
B. Perché Python legge dall’alto verso il basso  
C. Perché una condizione più generica può “mangiare” una più grave  
D. Perché `elif` è opzionale