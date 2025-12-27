# 🐍 Modulo 7: Introduzione alle eccezioni

**Sezione:** Python Core  
**Micro-corso:** PY-07  
**Workflow:** FIDUCIA  
**Obiettivo:** Capire cosa sono le eccezioni Python e perché il codice può "crashare" in modo controllato

---

## 🔧 Engineering focus

Uno script che legge dati da file, sensori o database può fallire per mille motivi: file mancante, valore non numerico, connessione persa. **Le eccezioni** sono il meccanismo Python per segnalare questi problemi. Capirle è il primo passo per scrivere codice che non crasha silenziosamente lasciandoti con risultati sbagliati.

---

## 🎯 Cosa imparerai

- Cosa succede quando Python incontra un errore a runtime
- Leggere e interpretare un messaggio di errore (traceback)
- Riconoscere le eccezioni più comuni nel codice ingegneristico
- Perché "crashare esplicitamente" è meglio che continuare con dati sbagliati

---

## 🎬 Scenario reale

Stai sviluppando uno script per validare i dati del **HP Fuel Gas System PT-764** prima dell'avvio. Lo script legge le composizioni del gas da un file CSV e calcola il peso molecolare medio.

Durante un test, il file contiene una riga corrotta: invece di `0.7356` per il metano, qualcuno ha scritto `0,7356` (virgola europea). Lo script crasha con un errore incomprensibile.

Il tuo collega ti chiede: "Ma non poteva semplicemente saltare quella riga?"

La risposta è: **no, e per una buona ragione**.

---

## 🧠 Concetto Python chiave

### Cosa sono le eccezioni

Un'**eccezione** è un evento che interrompe il normale flusso di esecuzione. Quando Python incontra qualcosa che non può gestire, **solleva** (raise) un'eccezione.

```python
# Questo solleva un'eccezione
valore = float("0,7356")  # ValueError: could not convert string to float
```

Se l'eccezione non viene gestita, il programma termina mostrando un **traceback**: la "pila" di chiamate che ha portato all'errore.

### Anatomia di un traceback

```
Traceback (most recent call last):
  File "valida_composizione.py", line 12, in <module>
    frazione = float(riga[1])
ValueError: could not convert string to float: '0,7356'
```

Leggi dal basso verso l'alto:
1. **ValueError** → il tipo di errore
2. **could not convert...** → descrizione del problema
3. **line 12** → dove è successo
4. **File "valida_composizione.py"** → in quale file

### Eccezioni comuni nel codice ingegneristico

| Eccezione | Quando si verifica | Esempio |
|-----------|-------------------|---------|
| `ValueError` | Valore del tipo giusto ma contenuto sbagliato | `float("abc")` |
| `TypeError` | Operazione su tipo sbagliato | `"37.9" + 1` |
| `ZeroDivisionError` | Divisione per zero | `portata / 0` |
| `KeyError` | Chiave non trovata in dizionario | `dati["pressione"]` se non esiste |
| `FileNotFoundError` | File non trovato | `open("inesistente.csv")` |
| `IndexError` | Indice fuori range | `lista[100]` su lista di 10 elementi |

### Perché crashare è una feature

```python
# MALE: nasconde l'errore, continua con dato sbagliato
try:
    pressione_bar = float(valore_stringa)
except:
    pressione_bar = 0  # Valore "di default"... ma è giusto?

# Il codice continua, ma con pressione = 0 bar
# Downstream, qualcuno prende una decisione sbagliata
```

Crashare esplicitamente ti **forza** a notare il problema. Continuare silenziosamente con dati sbagliati è molto più pericoloso.

---

## 🧠 PY-7 – Engineering mindset

> **"Un crash esplicito è un problema risolto in 5 minuti. Un errore silenzioso è un problema scoperto in 5 mesi."**

Nel prossimo modulo imparerai a *gestire* le eccezioni con `try/except`. Per ora, impara a *leggerle*.

---

## 🔧 Esempio Python applicato

Scenario: validazione composizione gas fuel per turbine GT-410/420.

```python
# Composizioni molari fuel gas da HP Separator (caso 4 wet)
# Fonte: documento27 - Duty Specifications Compressor Fuel Gas

composizioni_fuel_gas = [
    ("CO2", "0.0147"),
    ("Nitrogen", "0.0068"),
    ("Methane", "0.7308"),      # valore corretto
    ("Ethane", "0,1350"),       # ERRORE: virgola invece di punto
    ("Propane", "0.0651"),
]

def calcola_peso_molecolare_medio():
    """
    Calcola MW medio del fuel gas.
    Solleva eccezione se trova dati non validi.
    """
    pesi_molecolari = {
        "CO2": 44.01,
        "Nitrogen": 28.01,
        "Methane": 16.04,
        "Ethane": 30.07,
        "Propane": 44.10,
    }
    
    mw_medio = 0.0
    
    for componente, frazione_str in composizioni_fuel_gas:
        # Questa riga solleverà ValueError se frazione_str non è valida
        frazione = float(frazione_str)
        
        # Questa riga solleverà KeyError se componente non è nel dizionario
        mw_componente = pesi_molecolari[componente]
        
        mw_medio += frazione * mw_componente
    
    return mw_medio

# Esecuzione
print("Calcolo peso molecolare fuel gas...")
mw = calcola_peso_molecolare_medio()
print(f"MW medio: {mw:.2f} kg/kmol")
```

**Output (crash controllato):**
```
Calcolo peso molecolare fuel gas...
Traceback (most recent call last):
  File "fuel_gas_mw.py", line 32, in <module>
    mw = calcola_peso_molecolare_medio()
  File "fuel_gas_mw.py", line 24, in calcola_peso_molecolare_medio
    frazione = float(frazione_str)
ValueError: could not convert string to float: '0,1350'
```

Ora sai **esattamente**:
- Quale file (`fuel_gas_mw.py`)
- Quale funzione (`calcola_peso_molecolare_medio`)
- Quale riga (24)
- Quale valore ha causato il problema (`'0,1350'`)

---

## ✍️ Esercizio per lo studente

Il seguente codice legge le pressioni operative dai dati di design del sistema TEG (documento24). Ci sono **3 potenziali eccezioni** nascoste nei dati. Identifica dove il codice crasherà e perché.

```python
# Dati pressioni TEG system (alcuni sono corrotti)
pressioni_teg = {
    "V-475_scrubber": "39.8",
    "T-475_contactor": "39.4",
    "V-477_flash_drum": "5.0",
    "H-477_reboiler": "atmosferico",    # Problema 1
    "V-478_surge_drum": "",              # Problema 2
}

def verifica_pressioni_design():
    pressione_max_design_barg = 47.0
    
    for tag, pressione_str in pressioni_teg.items():
        pressione_barg = float(pressione_str)
        
        margine = pressione_max_design_barg - pressione_barg
        print(f"{tag}: {pressione_barg} barg, margine: {margine:.1f} bar")

# Esegui
verifica_pressioni_design()
```

**Domande:**
1. Su quale tag crasherà per primo?
2. Che tipo di eccezione verrà sollevata?
3. Se correggi quel dato, quale sarà il prossimo crash?

---

## ✅ Soluzione commentata

```python
# Il codice crasherà in questo ordine:

# 1. PRIMO CRASH su "H-477_reboiler"
#    Valore: "atmosferico"
#    Eccezione: ValueError
#    Motivo: float("atmosferico") non è possibile

# 2. SECONDO CRASH su "V-478_surge_drum" (se correggi il primo)
#    Valore: "" (stringa vuota)
#    Eccezione: ValueError  
#    Motivo: float("") non è possibile

# 3. NON c'è un terzo crash nei dati mostrati
#    Ma se ci fosse una chiave mancante nel dizionario,
#    avremmo KeyError

# Versione corretta dei dati:
pressioni_teg_corrette = {
    "V-475_scrubber": "39.8",
    "T-475_contactor": "39.4",
    "V-477_flash_drum": "5.0",
    "H-477_reboiler": "0.0",      # Atmosferico = ~0 barg
    "V-478_surge_drum": "0.0",    # Valore mancante da verificare
}
```

**Lezione chiave:** Il crash ti ha detto *esattamente* quali dati erano problematici. Senza il crash, avresti calcolato margini con dati sbagliati.

---

## ⚠️ Errori tipici

### Errore 1: Ignorare il traceback
**Sintomo:** "Ha dato errore, non funziona"  
**Causa:** Non hai letto il messaggio di errore  
**Fix:** Leggi il traceback dal basso verso l'alto. Il tipo di eccezione e la riga ti dicono tutto.

### Errore 2: Catturare tutto con `except:` generico
**Sintomo:** Il codice non crasha mai, ma i risultati sono sbagliati  
**Causa:** Hai nascosto l'errore invece di gestirlo  
**Fix:** Per ora, lascia crashare. Nel modulo `try/except` imparerai a gestire *specifiche* eccezioni.

### Errore 3: Confondere errore di sintassi con eccezione
**Sintomo:** `SyntaxError` prima ancora di eseguire  
**Causa:** Codice scritto male (parentesi mancante, typo)  
**Fix:** `SyntaxError` non è un'eccezione runtime. È un errore nel codice stesso, va corretto prima di eseguire.

---

## 📌 Takeaway

> **Le eccezioni sono il modo di Python per dirti "qualcosa è andato storto". Leggile, non ignorarle.**

---

## ❓ Verifica finale (5 domande)

1. **(Tecnica)** A cosa serve un blocco `try / except` in Python?
A. A rendere il codice più veloce  
B. A evitare di scrivere controlli sui dati  
C. A intercettare errori prevedibili e gestirli in modo controllato  
D. A impedire qualsiasi crash  

2. **(Tecnica)** Quale eccezione viene sollevata se tenti di convertire `"abc"` in `int`?
A. `TypeError`  
B. `ValueError`  
C. `KeyError`  
D. Nessuna  

3. **(Pratica)** Cosa succede qui?
```python
try:
    x = int("12.5")
except ValueError:
    print("Valore non valido")
```
A. Stampa `12`  
B. Stampa `12.5`  
C. Stampa `Valore non valido`  
D. Genera errore non gestito  

4. **(Pratica)** Qual è il problema di questo codice?
```python
try:
    f = open("config.txt")
except:
    pass
```
A. `open()` non va usato con file di testo  
B. `pass` rallenta il codice  
C. L’errore viene nascosto e il programma continua in modo non sicuro  
D. Python non supporta `except` senza tipo  

5. **(Engineering Judgement)** Quando è corretto **lasciare crashare** uno script?
A. Mai, uno script non deve mai fermarsi  
B. Sempre, anche per errori banali  
C. Quando i dati non sono affidabili e proseguire porterebbe a decisioni sbagliate  
D. Solo durante i test, mai in produzione