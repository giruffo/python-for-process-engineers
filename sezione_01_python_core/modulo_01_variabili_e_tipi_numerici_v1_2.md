# 🐍 Modulo 1: Variabili e Tipi Numerici in Python

> **Sezione:** Python Core  
> **Micro-corso:** PY-01  
> **Workflow:** DATI  
> **Obiettivo:** Imparare a rappresentare numeri reali di impianto in Python in modo chiaro, leggibile e affidabile.

---

## 🚪 Benvenuto nel corso (leggilo, davvero)

Questo **non** è un corso di Python per programmatori.

È un corso di **Python per process engineers**.

Qui Python non serve a “scrivere codice carino”, ma a:
- rappresentare numeri reali
- evitare errori silenziosi
- prendere decisioni tecniche sensate

Se sbagli **qui**, tutto il resto del corso crolla.

---

## 🔧 Engineering focus

Questo modulo lavora sul pilastro **DATI** dell’Engineering Workflow.

> Un numero senza nome, senza unità e senza contesto è solo un numero.
> Un numero sbagliato porta sempre a una decisione sbagliata.

Python non conosce la fisica. **Tu sì.**

---

## 🎯 Cosa imparerai

- Cos’è davvero una variabile in Python
- La differenza tra `int` e `float`
- Come Python decide il tipo di un numero
- Come fare calcoli semplici **senza perdere significato fisico**
- Perché il nome di una variabile è già documentazione tecnica

---

## 🎬 Scenario reale

Stai controllando alcuni valori operativi di un’unità di processo.
Dal sistema arrivano numeri: pressione, temperatura, portata.

Prima di fare qualsiasi calcolo devi rispondere a una domanda semplice:

> **So davvero cosa rappresentano questi numeri?**

Python sarà lo specchio della tua risposta.

---

## 🧠 Concetto Python chiave: le variabili

In Python una **variabile** è un **nome** che punta a un valore.

```python
temperatura_reattore_C = 78.5
```

- `78.5` è il numero
- `_C` è l’unità
- `temperatura_reattore` è il significato fisico

👉 Questo nome racconta già una storia.

Confronta con:
```python
t = 78.5
```

Tecnicamente corretto. Ingegneristicamente pericoloso.

---

## 🧠 PY-X – Engineering mindset

> **In Python i numeri non hanno unità.**  
> Le unità vivono nei nomi delle variabili e nei controlli che scrivi.

Se perdi le unità, perdi il controllo.

---

## 🔢 I tipi numerici fondamentali

### `int` — Numeri interi

Usali per **contare**:
- cicli
- batch
- elementi discreti

```python
numero_batch = 42
operatori_turno = 3
cicli_completati = 1500
```

---

### `float` — Numeri decimali

Usali per **misurare**:
- temperature
- pressioni
- portate

```python
pressione_bar = 2.5
temperatura_C = 58.0
portata_kg_h = 1250.75
```

Regola pratica:
> Se può avere la virgola nella realtà → è un `float`.

---

## 🧪 Python decide il tipo per te

Python assegna il tipo automaticamente:

```python
x = 100      # int
y = 95.7     # float
```

Vuoi verificare?

```python
print(type(x))
print(type(y))
```

Sapere che tipo stai usando **non è opzionale**.

---

## ➕ Operazioni matematiche (con giudizio)

Python sa fare i conti. Tu devi sapere **cosa stai contando**.

```python
massa_ingresso_kg = 1000
massa_uscita_kg = 850

resa_perc = (massa_uscita_kg / massa_ingresso_kg) * 100
print(resa_perc)  # 85.0
```

Ora una decisione:

```python
if resa_perc < 80:
    print("⚠️ Resa sotto soglia — verificare perdite")
```

Calcolo + controllo = codice utile.

---

## ⚠️ Attenzione: `int` + `float` → `float`

```python
pezzi = 10            # int
peso_medio_kg = 2.5  # float

peso_totale_kg = pezzi * peso_medio_kg
print(peso_totale_kg)        # 25.0
print(type(peso_totale_kg)) # float
```

Non è un bug. Python **protegge la precisione**.

---

## 🧪 Sanity check (obbligatorio)

Un numero va sempre controllato:

```python
if temperatura_C < -50 or temperatura_C > 300:
    print("⚠️ Valore non plausibile")
```

Meglio fermarsi che andare avanti con dati sbagliati.

---

## ✍️ Esercizio

Un vessel ha:
- capacità: **5000 L**
- volume attuale: **3750 L**

Calcola:
1. percentuale di riempimento
2. volume disponibile
3. avviso se > 90%

```python
capacita_totale_L = 5000
volume_attuale_L = 3750

# completa
percentuale_riempimento = ___
volume_disponibile_L = ___

if ___:
    print(___)
```

---

## ✅ Soluzione commentata

```python
capacita_totale_L = 5000
volume_attuale_L = 3750

percentuale_riempimento = (volume_attuale_L / capacita_totale_L) * 100
volume_disponibile_L = capacita_totale_L - volume_attuale_L

print(f"Riempimento: {percentuale_riempimento}%")
print(f"Disponibile: {volume_disponibile_L} L")

if percentuale_riempimento > 90:
    print("⚠️ Riempimento oltre il limite")
```

Ogni riga ha:
- un significato fisico
- un nome chiaro
- una decisione implicita

---

## ⚠️ Errori tipici

- **Sintomo:** codice corretto ma incomprensibile  
  **Causa:** nomi vaghi (`x`, `t`, `val`)  
  **Fix:** nomi espliciti + unità

- **Sintomo:** risultati apparentemente corretti  
  **Causa:** nessun controllo sui range  
  **Fix:** sanity check

- **Sintomo:** bug dopo settimane  
  **Causa:** unità perse  
  **Fix:** unità nei nomi

---

## 📌 Takeaway

> **In Python, dare il nome giusto a un numero è già metà del lavoro di un ingegnere.**

---

## ❓ Verifica finale (5 domande)

1. **(Tecnica)** Quale di queste variabili è la più corretta in un contesto di processo?  
A. `x = 78.5`  
B. `temp = 78.5`  
C. `temperatura_reattore_C = 78.5`  
D. `t1 = 78.5`  

2. **(Tecnica)** Qual è la differenza principale tra `int` e `float` in Python?  
A. `int` è più preciso di `float`  
B. `float` può rappresentare valori decimali, `int` no  
C. `int` può contenere stringhe numeriche  
D. Non c’è differenza, Python converte automaticamente  

3. **(Pratica)** Cosa stampa questo codice?
```python
massa_ingresso_kg = 1000
massa_uscita_kg = 850
resa = massa_uscita_kg / massa_ingresso_kg * 100
print(resa)
```
A. `0.85`  
B. `85`  
C. `85.0`  
D. Genera errore  

4. **(Pratica)** Qual è il problema principale di questo codice?
```python
a = 78.5
b = 120
c = a / b
```
A. Python non supporta la divisione tra numeri  
B. Manca il controllo sul valore di `b`  
C. Le variabili non hanno nomi né unità significative  
D. Il risultato è un `float`  

5. **(Engineering Judgement)** Perché è buona pratica includere l’unità nel nome della variabile?  
A. Per rendere il codice più lungo  
B. Perché Python richiede le unità  
C. Per evitare ambiguità fisiche e decisioni errate  
D. Per migliorare le prestazioni


📘 Notebook interattivo (Colab)  
👉 Apri in Colab:  
https://colab.research.google.com/github/giruffo/python-for-process-engineers/blob/main/notebooks/sezione_01_python_core/M01_variabili.ipynb
