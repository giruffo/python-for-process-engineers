# 📘 Scheda 1 – Python Core (Ripasso rapido)

Questa scheda riassume **tutti i concetti chiave** della **Sezione 1 – Python Core**.  
Usala per:
- ripasso veloce
- consultazione “al volo”
- riferimento durante esercizi e librerie successive

---

## 🧱 1. Variabili e tipi numerici

- Una **variabile** è un contenitore con significato.
- Buona pratica: **nome + unità**.

```python
temperatura_reattore_C = 78.5
portata_gas_kgh = 1500.0
```

### Tipi principali
- `int` → numeri interi (contatori, stati discreti)
- `float` → numeri decimali (misure, calcoli)

❗ I numeri **non hanno significato** senza contesto.

---

## 🔁 2. Booleani (True / False)

Usati per:
- stati
- condizioni
- decisioni

```python
pressione_alta = pressione_bar > 50
```

### Operatori logici
- `and` → entrambe vere
- `or` → almeno una vera
- `not` → negazione

⚠️ Precedenza: `not` → `and` → `or`

---

## 🧵 3. Stringhe

Usate per:
- tag
- righe di file
- input utente

```python
tag = "01-PT-402"
parti = tag.split("-")   # ['01', 'PT', '402']
```

### Regole chiave
- `split()` restituisce **una lista**
- Dopo uno split, **valida sempre il formato**

```python
if len(parti) != 3:
    raise ValueError("Formato tag non valido")
```

---

## ⌨️ 4. Input / Output

- `input()` restituisce **sempre una stringa**
- Va **convertita esplicitamente**

```python
val = float(input("Pressione [bar]: "))
```

### Gestione robusta
```python
try:
    p = float(input())
except ValueError:
    raise ValueError("Input non valido")
```

❗ Mai fidarsi ciecamente dell’input operatore.

---

## 🔀 5. if / elif / else

Usati per **decisioni mutuamente esclusive**.

```python
if livello > 2100:
    print("TRIP")
elif livello > 1900:
    print("ALLARME")
else:
    print("OK")
```

### Regola d’oro
➡️ **Condizioni più gravi prima**  
Una condizione generica può “mangiare” una critica.

---

## 🎯 6. Errori sui float

I `float` sono **approssimazioni binarie**.

```python
0.1 + 0.2 == 0.3   # False
```

### Confronto corretto
```python
import math
math.isclose(a, b, abs_tol=0.01)
```

✔️ Usa tolleranze per:
- misure
- bilanci
- calcoli reali

---

## 🚨 7. Eccezioni

Le eccezioni:
- NON sono nemiche
- sono **segnali di errore controllati**

```python
try:
    x = int("12.5")
except ValueError:
    print("Valore non valido")
```

### Anti-pattern grave
```python
except:
    pass
```
❌ Nasconde errori → dati spazzatura → decisioni sbagliate.

---

## 🧠 Engineering Mindset (riassunto)

- **Fail fast**: meglio fermarsi che sbagliare in silenzio
- I dati non validi **non vanno “aggiustati”**
- Il codice deve proteggere la decisione
- La chiarezza batte la furbizia

---

📌 **Fine Scheda 1 – Python Core**  
Da qui in poi: strutture dati e librerie.
