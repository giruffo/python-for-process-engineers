# 🐍 Modulo 3: Stringhe in Python

**Sezione:** Python Core  
**Micro-corso:** PY-03  
**Workflow:** DATI  
**Obiettivo:** Manipolare testo con `split()`, `replace()` e f-string per gestire tag, log e messaggi

---

## 🔧 Engineering focus

I dati in impianto arrivano spesso come testo: tag strumentazione (`01-LT-401`), righe di log, export da DCS. Saper estrarre informazioni da una stringa e formattare messaggi chiari è il primo passo per trasformare dati grezzi in informazioni utili.

---

## 🎯 Cosa imparerai

- Come spezzare una stringa in parti con `split()`
- Come sostituire caratteri o parole con `replace()`
- Come formattare messaggi dinamici con le f-string
- Come evitare errori comuni di indice e formattazione

---

## 🎬 Scenario reale

Stai analizzando un export dal DCS del CPF. Ogni riga contiene un tag strumentazione:

```
01-LT-401,Level Transmitter,HP Separator V-100,1695,mm
01-PT-402,Pressure Transmitter,HP Separator V-100,41.2,barg
34-FT-415,Flow Transmitter,TEG Contactor T-475,88490,Sm3/h
```

Devi estrarre:
- L'**area** (es. `01`, `34`)
- Il **tipo** di strumento (es. `LT`, `PT`, `FT`)
- Il **valore** e l'**unità** per generare un report leggibile

---

## 🧠 Concetto Python chiave

### Le stringhe sono sequenze

Una stringa è una sequenza di caratteri. Puoi accedere a ogni carattere con un indice:

```python
tag = "01-LT-401"
print(tag[0])   # '0' — primo carattere (indice 0)
print(tag[3])   # 'L' — quarto carattere (indice 3)
print(tag[-1])  # '1' — ultimo carattere
```

### `split()` — Spezza una stringa

Divide una stringa in una lista di parti, usando un separatore:

```python
tag = "01-LT-401"
parti = tag.split("-")
print(parti)  # ['01', 'LT', '401']

area = parti[0]      # '01'
tipo = parti[1]      # 'LT'
numero = parti[2]    # '401'
```

Se non specifichi il separatore, `split()` usa gli spazi:

```python
descrizione = "Level Transmitter"
parole = descrizione.split()
print(parole)  # ['Level', 'Transmitter']
```

### `replace()` — Sostituisce testo

```python
tag = "01-LT-401"
tag_nuovo = tag.replace("01", "34")
print(tag_nuovo)  # '34-LT-401'

# Utile per pulire dati
valore = "1,695.5"
valore_pulito = valore.replace(",", "")
print(valore_pulito)  # '1695.5'
```

### f-string — Formatta messaggi dinamici

Le f-string (formatted string literals) iniziano con `f` e permettono di inserire variabili direttamente nel testo:

```python
tag = "01-LT-401"
valore = 1695
unita = "mm"

messaggio = f"Tag {tag}: valore = {valore} {unita}"
print(messaggio)  # 'Tag 01-LT-401: valore = 1695 mm'
```

Puoi anche formattare i numeri:

```python
pressione = 41.23456
print(f"Pressione: {pressione:.2f} barg")  # 'Pressione: 41.23 barg'
```

---

## 🧠 PY-03 – Engineering mindset

> **"Una stringa è un dato grezzo. `split()` è il coltello che lo trasforma in informazione."**  
> Prima di calcolare, devi estrarre. Prima di decidere, devi leggere.

---

## 🔧 Esempio Python applicato

```python
# Parsing di una riga export DCS — CPF MLN
# Formato: tag,descrizione,equipment,valore,unità

riga_dcs = "01-PT-402,Pressure Transmitter,HP Separator V-100,41.2,barg"

# Step 1: Spezza la riga nei suoi campi
campi = riga_dcs.split(",")
# campi = ['01-PT-402', 'Pressure Transmitter', 'HP Separator V-100', '41.2', 'barg']

tag = campi[0]           # '01-PT-402'
descrizione = campi[1]   # 'Pressure Transmitter'
equipment = campi[2]     # 'HP Separator V-100'
valore_str = campi[3]    # '41.2' — ancora stringa!
unita = campi[4]         # 'barg'

# Step 2: Estrai area e tipo dal tag
parti_tag = tag.split("-")
area = parti_tag[0]      # '01'
tipo = parti_tag[1]      # 'PT'
numero = parti_tag[2]    # '402'

# Step 3: Converti il valore in numero per poterlo usare
valore = float(valore_str)

# Step 4: Genera un messaggio leggibile
print(f"[Area {area}] {descrizione} ({tag})")
print(f"  Equipment: {equipment}")
print(f"  Lettura: {valore:.1f} {unita}")

# Step 5: Decisione basata sul valore
limite_pahh_barg = 45.0
if valore > limite_pahh_barg:
    print(f"  ⚠️ ALLARME: {valore:.1f} > {limite_pahh_barg} {unita}")
else:
    print(f"  ✓ OK: sotto limite PAHH")
```

**Output:**
```
[Area 01] Pressure Transmitter (01-PT-402)
  Equipment: HP Separator V-100
  Lettura: 41.2 barg
  ✓ OK: sotto limite PAHH
```

---

## ✍️ Esercizio per lo studente

Hai questa riga dal log allarmi:

```python
riga_allarme = "34-LZT-412;LAHH;TEG Contactor T-475;1950;mm"
```

Il separatore qui è `;` (punto e virgola). Completa il codice per estrarre le informazioni e stampare un messaggio:

```python
riga_allarme = "34-LZT-412;LAHH;TEG Contactor T-475;1950;mm"

# Spezza la riga usando il separatore corretto
campi = riga_allarme.split(___)

tag = campi[0]
tipo_allarme = campi[1]
equipment = campi[2]
valore = ___(campi[3])  # Converti in numero
unita = campi[4]

# Stampa il messaggio
print(f"ALLARME {___}: {___} su {___} = {___} {___}")
```

---

## ✅ Soluzione commentata

```python
riga_allarme = "34-LZT-412;LAHH;TEG Contactor T-475;1950;mm"

# Spezza la riga usando ";" come separatore
campi = riga_allarme.split(";")

tag = campi[0]           # '34-LZT-412'
tipo_allarme = campi[1]  # 'LAHH'
equipment = campi[2]     # 'TEG Contactor T-475'
valore = int(campi[3])   # 1950 — usiamo int perché è un livello in mm
unita = campi[4]         # 'mm'

# Stampa il messaggio formattato
print(f"ALLARME {tipo_allarme}: {tag} su {equipment} = {valore} {unita}")
# Output: ALLARME LAHH: 34-LZT-412 su TEG Contactor T-475 = 1950 mm
```

**Perché `int()` e non `float()`?** Il livello è un numero intero in millimetri. Usare il tipo corretto rende il codice più chiaro e evita decimali inutili (es. `1950.0`).

---

## ⚠️ Errori tipici

### Errore 1: Indice fuori range

**Sintomo:** `IndexError: list index out of range`  
**Causa:** Stai accedendo a un elemento che non esiste  
**Fix:** Controlla quanti elementi ha la lista
```python
tag = "01-LT-401"
parti = tag.split("-")  # ['01', 'LT', '401'] — 3 elementi

# ❌ Sbagliato — indice 3 non esiste (gli indici vanno da 0 a 2)
quarto = parti[3]

# ✓ Corretto — verifica prima
print(len(parti))  # 3
ultimo = parti[2]  # '401'
```

---

### Errore 2: Dimenticare di convertire stringa in numero

**Sintomo:** `TypeError: '>' not supported between instances of 'str' and 'float'`  
**Causa:** Stai confrontando una stringa con un numero  
**Fix:** Converti con `float()` o `int()`
```python
valore_str = "41.2"
limite = 45.0

# ❌ Sbagliato — valore_str è ancora una stringa
if valore_str > limite:

# ✓ Corretto
valore = float(valore_str)
if valore > limite:
```

---

### Errore 3: Usare il separatore sbagliato in `split()`

**Sintomo:** La lista contiene un solo elemento (la stringa intera)  
**Causa:** Il separatore non corrisponde al contenuto  
**Fix:** Verifica quale carattere separa effettivamente i campi
```python
riga = "01-LT-401;Level;1695"

# ❌ Sbagliato — la riga usa ";" non ","
campi = riga.split(",")
print(campi)  # ['01-LT-401;Level;1695'] — un solo elemento!

# ✓ Corretto
campi = riga.split(";")
print(campi)  # ['01-LT-401', 'Level', '1695']
```

---

## 📌 Takeaway

> **`split()` estrae, `replace()` pulisce, f-string comunica. Tre strumenti per trasformare testo in informazione.**

---

## ❓ Verifica finale (5 domande)

1. **(Tecnica)** Cosa restituisce `split()` se il separatore non è presente nella stringa?
A. Una lista vuota  
B. La stringa originale  
C. Una lista con un solo elemento (la stringa originale)  
D. Genera errore  

2. **(Tecnica)** Qual è il risultato di questo codice?
```python
tag = "01-PT-402"
print(tag.split("-")[1])
```
A. `01`  
B. `PT`  
C. `402`  
D. Genera errore  

3. **(Pratica)** Cosa succede qui?
```python
valore_str = "41.2"
valore = float(valore_str)
print(valore + 1)
```
A. Stampa `"41.21"`  
B. Stampa `42.2`  
C. Stampa `"42.2"`  
D. Genera errore  

4. **(Pratica)** Qual è il problema principale di questo codice?
```python
riga = "PT-101;Pressure;HP SEP"
campi = riga.split(",")
```
A. `split()` non funziona con stringhe  
B. Il separatore è sbagliato rispetto al formato reale  
C. Manca un cast a `str`  
D. `split()` restituisce un dizionario  

5. **(Engineering Judgement)** Perché è buona pratica validare il numero di campi dopo uno `split()`?
A. Per migliorare le prestazioni  
B. Per evitare errori di sintassi  
C. Per fermare lo script se il formato dei dati non è quello atteso (fail fast)  
D. Per rendere il codice più compatto
