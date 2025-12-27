# 🐍 Modulo 2: Booleani e logica decisionale

**Sezione:** Python Core  
**Micro-corso:** PY-02  
**Workflow:** DECISIONE  
**Obiettivo:** Usare `bool`, operatori logici (`and`, `or`, `not`) e confronti per prendere decisioni binarie nel codice

---

## 🔧 Engineering focus

In un impianto, ogni allarme è una domanda con risposta sì/no: *"La pressione ha superato il limite?"* Python rappresenta queste risposte con i **booleani** (`True`/`False`). Capire come funzionano significa scrivere codice che decide in modo chiaro e verificabile.

---

## 🎯 Cosa imparerai

- Come Python rappresenta vero/falso con il tipo `bool`
- Come usare operatori di confronto (`>`, `<`, `>=`, `==`, `!=`)
- Come combinare condizioni con `and`, `or`, `not`
- Come evitare l'errore classico `=` vs `==`

---

## 🎬 Scenario reale

Stai scrivendo uno script per monitorare il separatore HP (V-100) del CPF.  
I dati dal DCS ti danno:
- Pressione operativa: **41.2 barg** (design: 47 barg, allarme PAHH: 45 barg)
- Livello olio: **1450 mm** (NLL: 1695 mm, LLLL: 300 mm, HHLL: riempie il demister)
- Temperatura: **58°C** (range operativo: 18–62°C)

Il tuo script deve rispondere: **"È tutto OK oppure devo generare un allarme?"**

---

## 🧠 Concetto Python chiave

### Il tipo `bool`

In Python esistono solo due valori booleani:
```python
True   # vero
False  # falso
```

Ogni confronto restituisce un `bool`:
```python
pressione_barg = 41.2
limite_pahh_barg = 45.0

allarme_pressione = pressione_barg > limite_pahh_barg
print(allarme_pressione)  # False — siamo sotto il limite
```

### Operatori di confronto

| Operatore | Significato | Esempio |
|-----------|-------------|---------|
| `>` | maggiore | `41 > 45` → `False` |
| `<` | minore | `41 < 45` → `True` |
| `>=` | maggiore o uguale | `41 >= 41` → `True` |
| `<=` | minore o uguale | `58 <= 62` → `True` |
| `==` | uguale | `41 == 41` → `True` |
| `!=` | diverso | `41 != 45` → `True` |

### Operatori logici

| Operatore | Significato | Esempio |
|-----------|-------------|---------|
| `and` | entrambi veri | `True and False` → `False` |
| `or` | almeno uno vero | `True or False` → `True` |
| `not` | inverte | `not True` → `False` |

---

## 🧠 PY-02 – Engineering mindset

> **"Un booleano è una decisione congelata in una variabile."**  
> Se il tuo codice deve decidere qualcosa, quel "qualcosa" è (o diventerà) un `bool`.

---

## 🔧 Esempio Python applicato

```python
# Monitoraggio HP Separator V-100 — CPF MLN
# Valori letti dal DCS (simulati)

pressione_barg = 41.2
livello_mm = 1450
temperatura_C = 58.0

# Limiti operativi (da Process Control Philosophy)
limite_pahh_barg = 45.0      # High-High pressure alarm
limite_llll_mm = 300         # Low-Low-Low-Low level (trip)
limite_hhll_mm = 1900        # High-High-Low level (trip)
temp_min_C = 18.0
temp_max_C = 62.0

# Controlli booleani — ogni riga è una domanda sì/no
pressione_ok = pressione_barg < limite_pahh_barg
livello_ok = livello_mm > limite_llll_mm and livello_mm < limite_hhll_mm
temperatura_ok = temperatura_C >= temp_min_C and temperatura_C <= temp_max_C

# Decisione finale: tutto OK solo se TUTTI i controlli sono OK
sistema_ok = pressione_ok and livello_ok and temperatura_ok

print(f"Pressione OK: {pressione_ok}")      # True
print(f"Livello OK: {livello_ok}")          # True
print(f"Temperatura OK: {temperatura_ok}")  # True
print(f"Sistema OK: {sistema_ok}")          # True

# Allarme se NON è tutto OK
if not sistema_ok:
    print("⚠️ ALLARME: verificare parametri!")
else:
    print("✓ Operazione normale")
```

**Output:**
```
Pressione OK: True
Livello OK: True
Temperatura OK: True
Sistema OK: True
✓ Operazione normale
```

---

## ✍️ Esercizio per lo studente

Il separatore V-100 ha anche un controllo sul livello interfaccia HC/acqua (NLL interfaccia: 810 mm).  
Completa il codice per verificare che il livello interfaccia sia nel range 400–1100 mm:

```python
livello_interfaccia_mm = 785

limite_basso_mm = 400
limite_alto_mm = 1100

# Completa: interfaccia_ok deve essere True se il livello è nel range
interfaccia_ok = livello_interfaccia_mm ___ limite_basso_mm ___ livello_interfaccia_mm ___ limite_alto_mm

print(f"Interfaccia OK: {interfaccia_ok}")
```

---

## ✅ Soluzione commentata

```python
livello_interfaccia_mm = 785

limite_basso_mm = 400
limite_alto_mm = 1100

# Il livello è OK se è maggiore del minimo E minore del massimo
# Usiamo > e < (non >= e <=) perché ai limiti esatti scatta l'allarme
interfaccia_ok = livello_interfaccia_mm > limite_basso_mm and livello_interfaccia_mm < limite_alto_mm

print(f"Interfaccia OK: {interfaccia_ok}")  # True (785 è tra 400 e 1100)
```

**Perché `and`?** Entrambe le condizioni devono essere vere contemporaneamente:
- Il livello deve essere sopra 400 mm **E**
- Il livello deve essere sotto 1100 mm

Se usassi `or`, basterebbe una sola condizione vera — e un livello di 2000 mm passerebbe il controllo (è > 400)!

---

## ⚠️ Errori tipici

### Errore 1: Usare `=` invece di `==`

**Sintomo:** `SyntaxError: cannot assign to comparison`  
**Causa:** `=` è assegnamento, `==` è confronto  
**Fix:**
```python
# ❌ Sbagliato
if pressione_barg = 41.2:

# ✓ Corretto
if pressione_barg == 41.2:
```

---

### Errore 2: Dimenticare che `and` ha priorità su `or`

**Sintomo:** La logica sembra funzionare al contrario  
**Causa:** `and` viene valutato prima di `or` (come × prima di +)  
**Fix:** Usa le parentesi per chiarire l'ordine
```python
# ❌ Ambiguo
allarme = pressione_alta or livello_basso and temperatura_alta

# ✓ Esplicito — prima valuta le parentesi
allarme = pressione_alta or (livello_basso and temperatura_alta)
```

---

### Errore 3: Confrontare booleani con `== True`

**Sintomo:** Codice che funziona ma è ridondante  
**Causa:** Un `bool` È già `True` o `False`  
**Fix:**
```python
# ❌ Ridondante
if sistema_ok == True:

# ✓ Pythonic
if sistema_ok:

# Per verificare False:
if not sistema_ok:
```

---

## 📌 Takeaway

> **Un booleano risponde sempre sì o no. Usalo per trasformare dati in decisioni.**

---

## ❓ Verifica finale (5 domande)

1. **(Tecnica)** Qual è il risultato dell’espressione booleana?
```python
True and False or True
```
A. `False`  
B. `True`  
C. `None`  
D. Genera errore  

2. **(Tecnica)** Quale di queste espressioni restituisce `False`?
A. `5 > 3 and 2 < 4`  
B. `not (10 > 5)`  
C. `3 == 3 or 4 < 1`  
D. `7 >= 7`  

3. **(Pratica)** Cosa stampa questo codice?
```python
pressione_bar = 48
allarme = pressione_bar > 50
print(allarme)
```
A. `True`  
B. `False`  
C. `48`  
D. Genera errore  

4. **(Pratica)** Dove sta il problema logico?
```python
p = 55
if p > 30:
    print("OK")
if p > 50:
    print("ALLARME")
```
A. Python non supporta due `if` consecutivi  
B. Le condizioni sono sbagliate  
C. Gli stati non sono mutuamente esclusivi  
D. Manca un `else` obbligatorio  

5. **(Engineering Judgement)** In uno script di processo, perché è pericoloso scrivere condizioni booleane troppo generiche?
A. Perché rallentano l’esecuzione  
B. Perché rendono il codice più lungo  
C. Perché possono attivare stati multipli o sbagliati  
D. Perché Python non ottimizza i booleani