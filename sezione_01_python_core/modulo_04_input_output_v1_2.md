# 🐍 Modulo 4: Input e output in Python

**Sezione:** Python Core  
**Micro-corso:** PY-04  
**Workflow:** DATI  
**Obiettivo:** Acquisire dati dall'operatore con `input()` e comunicare risultati con `print()`

---

## 🔧 Engineering focus

Nel CPF, ogni turno richiede l'inserimento manuale di letture, setpoint o parametri operativi. Uno script che acquisisce dati dall'operatore deve essere chiaro su cosa chiede, deve convertire i valori nel tipo giusto e deve confermare cosa ha ricevuto. Un input mal gestito genera errori silenziosi che si propagano in tutto il calcolo.

---

## 🎯 Cosa imparerai

- Usare `input()` per acquisire dati dall'operatore
- Convertire stringhe in numeri con `float()` e `int()`
- Formattare output leggibili con `print()` e f-string
- Gestire il caso in cui l'operatore inserisce valori non validi

---

## 🎬 Scenario reale

Durante il turno notturno al CPF, l'operatore di sala controllo deve registrare i parametri operativi del HP Separator V-100:

| Parametro | Valore letto | Unità |
|-----------|--------------|-------|
| Pressione operativa | 41.2 | barg |
| Temperatura inlet | 18.0 | °C |
| Livello HC (L/G) | 1720 | mm |
| Portata gas outlet | 89946 | kg/h |

Lo script deve chiedere i valori, confermare cosa ha ricevuto e segnalare se qualche parametro è fuori range.

---

## 🧠 Concetto Python chiave

### `input()` – Acquisire dati dall'operatore

La funzione `input()` mostra un messaggio e attende che l'operatore digiti qualcosa:

```python
risposta = input("Inserisci il valore: ")
```

**Attenzione critica:** `input()` restituisce **sempre una stringa**, anche se l'operatore digita un numero.

```python
valore = input("Pressione [barg]: ")
print(type(valore))  # <class 'str'> — è una stringa!
```

Per usare il valore in calcoli, devi convertirlo:

```python
pressione_barg = float(input("Pressione [barg]: "))  # ora è un numero
```

### `print()` – Comunicare risultati

La funzione `print()` mostra messaggi all'operatore:

```python
print("Valore registrato:", pressione_barg, "barg")
```

Con le f-string (viste nel Modulo 3), l'output diventa più leggibile:

```python
print(f"Pressione registrata: {pressione_barg:.1f} barg")
```

### Quando usare `int()` vs `float()`

| Tipo di dato | Funzione | Esempio |
|--------------|----------|---------|
| Valori con decimali | `float()` | Pressione: 41.2 barg |
| Valori interi | `int()` | Livello: 1720 mm |
| Conteggi | `int()` | Numero pozzi: 12 |

---

## 🧠 PY-04 – Engineering mindset

> "Un `input()` senza conversione è una bomba a orologeria. Prima o poi qualcuno confronterà `"41"` con `45` e il codice non farà quello che pensi."

---

## 🔧 Esempio Python applicato

```python
# === Registrazione parametri HP Separator V-100 ===
# Turno notturno - CPF Menzel Lejmat Nord

# Limiti operativi (da Process Control Philosophy)
PRESSIONE_MIN_barg = 38.0
PRESSIONE_MAX_barg = 45.0
LIVELLO_NLL_mm = 1695
LIVELLO_HLL_mm = 1900

# --- Acquisizione dati operatore ---
print("=" * 50)
print("REGISTRAZIONE PARAMETRI - HP Separator V-100")
print("=" * 50)

# Pressione: valore con decimali → float()
pressione_str = input("Pressione operativa [barg]: ")
pressione_barg = float(pressione_str)

# Livello: valore intero → int()
livello_str = input("Livello HC (L/G) [mm]: ")
livello_mm = int(livello_str)

# Temperatura: valore con decimali → float()
temperatura_str = input("Temperatura inlet [°C]: ")
temperatura_C = float(temperatura_str)

# --- Conferma valori ricevuti ---
print()
print("--- VALORI REGISTRATI ---")
print(f"Pressione:   {pressione_barg:.1f} barg")
print(f"Livello:     {livello_mm} mm")
print(f"Temperatura: {temperatura_C:.1f} °C")

# --- Verifica limiti ---
print()
print("--- VERIFICA LIMITI ---")

# Check pressione
if pressione_barg < PRESSIONE_MIN_barg:
    print(f"⚠️  ATTENZIONE: Pressione BASSA ({pressione_barg:.1f} < {PRESSIONE_MIN_barg} barg)")
elif pressione_barg > PRESSIONE_MAX_barg:
    print(f"🚨 ALLARME: Pressione ALTA ({pressione_barg:.1f} > {PRESSIONE_MAX_barg} barg)")
else:
    print(f"✓ Pressione OK (range {PRESSIONE_MIN_barg}-{PRESSIONE_MAX_barg} barg)")

# Check livello
if livello_mm < LIVELLO_NLL_mm:
    print(f"⚠️  ATTENZIONE: Livello sotto NLL ({livello_mm} < {LIVELLO_NLL_mm} mm)")
elif livello_mm > LIVELLO_HLL_mm:
    print(f"🚨 ALLARME: Livello sopra HLL ({livello_mm} > {LIVELLO_HLL_mm} mm)")
else:
    print(f"✓ Livello OK (range {LIVELLO_NLL_mm}-{LIVELLO_HLL_mm} mm)")

print()
print("Registrazione completata.")
```

**Output esempio (con input 41.2, 1720, 18.0):**
```
==================================================
REGISTRAZIONE PARAMETRI - HP Separator V-100
==================================================
Pressione operativa [barg]: 41.2
Livello HC (L/G) [mm]: 1720
Temperatura inlet [°C]: 18.0

--- VALORI REGISTRATI ---
Pressione:   41.2 barg
Livello:     1720 mm
Temperatura: 18.0 °C

--- VERIFICA LIMITI ---
✓ Pressione OK (range 38.0-45.0 barg)
✓ Livello OK (range 1695-1900 mm)

Registrazione completata.
```

---

## ✍️ Esercizio per lo studente

L'operatore deve inserire i dati del TEG Contactor T-475:

| Parametro | Unità | Limite min | Limite max |
|-----------|-------|------------|------------|
| Pressione | barg | 38.0 | 42.0 |
| Portata gas | kg/h | 70000 | 90000 |
| Temperatura TEG inlet | °C | 55.0 | 70.0 |

Completa lo script:

```python
# Registrazione parametri TEG Contactor T-475

# Limiti operativi
PRESSIONE_MIN_barg = 38.0
PRESSIONE_MAX_barg = 42.0
PORTATA_MIN_kgh = 70000
PORTATA_MAX_kgh = 90000
TEMP_TEG_MIN_C = 55.0
TEMP_TEG_MAX_C = 70.0

# Acquisizione dati
pressione_barg = ___(input("Pressione [barg]: "))
portata_kgh = ___(input("Portata gas [kg/h]: "))
temp_TEG_C = ___(input("Temperatura TEG inlet [°C]: "))

# Conferma valori
print()
print(f"Pressione: {___} barg")
print(f"Portata: {___} kg/h")
print(f"Temp TEG: {___} °C")

# Verifica pressione
if pressione_barg < PRESSIONE_MIN_barg:
    print("⚠️  Pressione BASSA")
elif ___:
    print("🚨 Pressione ALTA")
else:
    print("✓ Pressione OK")

# Verifica portata (completa tu)
___
```

---

## ✅ Soluzione commentata

```python
# Registrazione parametri TEG Contactor T-475

# Limiti operativi (da Duty Specifications documento24)
PRESSIONE_MIN_barg = 38.0
PRESSIONE_MAX_barg = 42.0
PORTATA_MIN_kgh = 70000
PORTATA_MAX_kgh = 90000
TEMP_TEG_MIN_C = 55.0
TEMP_TEG_MAX_C = 70.0

# Acquisizione dati
# float() per pressione e temperatura (hanno decimali)
# int() per portata (valore intero in kg/h)
pressione_barg = float(input("Pressione [barg]: "))
portata_kgh = int(input("Portata gas [kg/h]: "))
temp_TEG_C = float(input("Temperatura TEG inlet [°C]: "))

# Conferma valori
print()
print(f"Pressione: {pressione_barg:.1f} barg")
print(f"Portata: {portata_kgh} kg/h")
print(f"Temp TEG: {temp_TEG_C:.1f} °C")

# Verifica pressione
if pressione_barg < PRESSIONE_MIN_barg:
    print("⚠️  Pressione BASSA")
elif pressione_barg > PRESSIONE_MAX_barg:
    print("🚨 Pressione ALTA")
else:
    print("✓ Pressione OK")

# Verifica portata
if portata_kgh < PORTATA_MIN_kgh:
    print("⚠️  Portata BASSA")
elif portata_kgh > PORTATA_MAX_kgh:
    print("🚨 Portata ALTA")
else:
    print("✓ Portata OK")

# Verifica temperatura TEG
if temp_TEG_C < TEMP_TEG_MIN_C:
    print("⚠️  Temperatura TEG BASSA - rischio condensazione")
elif temp_TEG_C > TEMP_TEG_MAX_C:
    print("🚨 Temperatura TEG ALTA - rischio perdita TEG")
else:
    print("✓ Temperatura TEG OK")
```

**Nota:** Per la portata usiamo `int()` perché 81000 kg/h è più leggibile di 81000.0 kg/h. I decimali non aggiungono informazione utile per portate di questo ordine di grandezza.

---

## ⚠️ Errori tipici

### 1. Dimenticare la conversione

```python
pressione = input("Pressione [barg]: ")
if pressione > 45:  # ❌ ERRORE!
    print("Allarme")
```

**Sintomo:** Nessun errore visibile, ma il confronto non funziona come atteso  
**Causa:** `pressione` è una stringa, `"41.2" > 45` usa l'ordine alfabetico  
**Fix:** `pressione = float(input("Pressione [barg]: "))`

### 2. Usare `int()` su un numero con decimali

```python
temperatura = int(input("Temperatura [°C]: "))
# Operatore digita: 18.5
```

**Sintomo:** `ValueError: invalid literal for int() with base 10: '18.5'`  
**Causa:** `int()` non accetta stringhe con il punto decimale  
**Fix:** Usa `float()` per valori che possono avere decimali

### 3. Input vuoto o non numerico

```python
pressione = float(input("Pressione [barg]: "))
# Operatore preme Enter senza digitare nulla
```

**Sintomo:** `ValueError: could not convert string to float: ''`  
**Causa:** L'operatore non ha inserito un valore  
**Fix:** Nel Modulo 7 vedremo `try/except` per gestire questi casi

---

## 📌 Takeaway

> "`input()` restituisce sempre una stringa. Se ti serve un numero, convertilo subito. Se ti serve una decisione, verificalo subito."

---

## ❓ Verifica finale (5 domande)

1. **(Tecnica)** Cosa restituisce sempre la funzione `input()` in Python?
A. Un numero  
B. Un booleano  
C. Una stringa  
D. Dipende dal contenuto digitato  

2. **(Tecnica)** Qual è il modo corretto per leggere una pressione inserita dall’operatore e usarla come numero?
A. `p = input()`  
B. `p = int(input())` sempre  
C. `p = float(input())`  
D. `p = str(input())`  

3. **(Pratica)** Cosa succede qui?
```python
livello = input("Livello [mm]: ")
print(livello + 100)
```
A. Stampa il valore corretto  
B. Stampa una stringa concatenata  
C. Crasha con `TypeError`  
D. Converte automaticamente il valore  

4. **(Pratica)** Qual è il vantaggio di usare `try/except` quando converti un input?
A. Rende il codice più veloce  
B. Evita di dover validare i dati  
C. Permette di intercettare input non validi e fermare lo script  
D. Serve solo per i file  

5. **(Engineering Judgement)** Perché è pericoloso fidarsi ciecamente di un input operatore?
A. Perché gli operatori sbagliano sempre  
B. Perché Python non gestisce input complessi  
C. Perché input errati possono portare a decisioni di processo sbagliate  
D. Perché `input()` è deprecata