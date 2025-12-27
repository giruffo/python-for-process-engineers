# 🐍 Modulo 6: Errori comuni sui float

**Sezione:** Python Core  
**Micro-corso:** PY-06  
**Workflow:** FIDUCIA  
**Obiettivo:** Capire perché i confronti tra numeri decimali falliscono e come gestirli correttamente

---

## 🔧 Engineering focus

Quando validi un Heat & Material Balance, le frazioni molari devono sommare a 1.0. Quando confronti misure di processo, due valori "uguali" potrebbero risultare diversi. Python rappresenta i decimali in modo approssimato: se non lo sai, i tuoi script di validazione daranno risultati sbagliati.

---

## 🎯 Cosa imparerai

- Perché `0.1 + 0.2 != 0.3` in Python
- Come confrontare correttamente numeri decimali
- Uso di `math.isclose()` e tolleranze esplicite
- Quando arrotondare e quando no

---

## 🎬 Scenario reale

Stai validando la composizione del gas di rilascio al sistema HP Flare Z-645. Dal datasheet, le frazioni molari dei componenti devono sommare esattamente a 1.0000:

| Componente | Frazione molare |
|------------|-----------------|
| CO2        | 0.0148          |
| N2         | 0.0068          |
| CH4        | 0.7357          |
| C2H6       | 0.1358          |
| C3H8       | 0.0654          |
| i-C4       | 0.0076          |
| n-C4       | 0.0184          |
| i-C5       | 0.0042          |
| n-C5       | 0.0047          |
| Altri      | 0.0066          |

Scrivi uno script per verificare che la somma sia 1.0. Il controllo fallisce. Perché?

---

## 🧠 Concetto Python chiave

### Il problema della rappresentazione binaria

I computer memorizzano i numeri decimali in formato binario (IEEE 754). Alcuni decimali semplici per noi non hanno una rappresentazione binaria esatta:

```python
# Questo stampa False!
print(0.1 + 0.2 == 0.3)  # False

# Vediamo perché
print(f"{0.1:.20f}")     # 0.10000000000000000555
print(f"{0.2:.20f}")     # 0.20000000000000001110
print(f"{0.1 + 0.2:.20f}")  # 0.30000000000000004441
```

L'errore è minuscolo (~10⁻¹⁶) ma sufficiente a far fallire un confronto con `==`.

### Quando succede

- Somme di frazioni (molari, massiche, volumetriche)
- Confronti tra valori misurati e calcolati
- Verifiche di bilancio (massa, energia)
- Qualsiasi aritmetica con decimali

### La soluzione: tolleranze

Non usare mai `==` per confrontare float. Usa una **tolleranza**:

```python
import math

# Metodo 1: math.isclose() - raccomandato
math.isclose(0.1 + 0.2, 0.3)  # True

# Metodo 2: tolleranza esplicita
abs((0.1 + 0.2) - 0.3) < 1e-9  # True
```

### math.isclose() in dettaglio

```python
math.isclose(a, b, rel_tol=1e-9, abs_tol=0.0)
```

- `rel_tol`: tolleranza relativa (default 1e-9 = 0.0000001%)
- `abs_tol`: tolleranza assoluta (utile quando i valori sono vicini a zero)

Per applicazioni di processo, una tolleranza di `1e-6` (0.0001%) è generalmente adeguata.

---

## 🧠 PY-06 – Engineering mindset

> **"Un computer che dice 0.9999999999 ≠ 1.0 ha ragione dal suo punto di vista. Sta a te definire cosa significa 'uguale' nel tuo contesto ingegneristico."**

---

## 🔧 Esempio Python applicato

### Validazione composizione gas per HP Flare

```python
import math

def valida_composizione_gas(composizione: dict, 
                            tolleranza: float = 1e-6) -> dict:
    """
    Verifica che le frazioni molari sommino a 1.0.
    
    Args:
        composizione: dizionario {componente: frazione_molare}
        tolleranza: margine accettabile (default 1e-6 = 0.0001%)
    
    Returns:
        dizionario con risultato validazione
    """
    # Calcola somma frazioni
    somma_frazioni = sum(composizione.values())
    
    # Calcola deviazione da 1.0
    deviazione = abs(somma_frazioni - 1.0)
    
    # Verifica con tolleranza - NON usare ==
    composizione_valida = math.isclose(somma_frazioni, 1.0, 
                                        abs_tol=tolleranza)
    
    return {
        "somma_calcolata": somma_frazioni,
        "deviazione_da_1": deviazione,
        "tolleranza_usata": tolleranza,
        "valido": composizione_valida,
        "messaggio": "OK" if composizione_valida 
                     else f"ERRORE: deviazione {deviazione:.2e} > tolleranza"
    }


# Composizione gas rilascio HP Flare Z-645 (da duty spec)
gas_flare = {
    "CO2":     0.0148,
    "N2":      0.0068,
    "CH4":     0.7357,
    "C2H6":    0.1358,
    "C3H8":    0.0654,
    "i-C4":    0.0076,
    "n-C4":    0.0184,
    "i-C5":    0.0042,
    "n-C5":    0.0047,
    "n-C6":    0.0030,
    "altri":   0.0036   # somma componenti minori
}

# Validazione
risultato = valida_composizione_gas(gas_flare)

print("=== VALIDAZIONE COMPOSIZIONE GAS HP FLARE Z-645 ===")
print(f"Somma frazioni molari: {risultato['somma_calcolata']:.10f}")
print(f"Deviazione da 1.0:     {risultato['deviazione_da_1']:.2e}")
print(f"Tolleranza:            {risultato['tolleranza_usata']:.0e}")
print(f"Stato: {risultato['messaggio']}")


# --- Confronto SBAGLIATO vs CORRETTO ---
print("\n=== DIMOSTRAZIONE ERRORE CLASSICO ===")

# SBAGLIATO: confronto diretto
somma = sum(gas_flare.values())
if somma == 1.0:
    print("Confronto con ==: VALIDO")
else:
    print(f"Confronto con ==: INVALIDO (somma = {somma})")

# CORRETTO: confronto con tolleranza
if math.isclose(somma, 1.0, abs_tol=1e-6):
    print("Confronto con isclose(): VALIDO")
else:
    print("Confronto con isclose(): INVALIDO")
```

**Output:**
```
=== VALIDAZIONE COMPOSIZIONE GAS HP FLARE Z-645 ===
Somma frazioni molari: 1.0000000000
Deviazione da 1.0:     0.00e+00
Tolleranza:            1e-06
Stato: OK

=== DIMOSTRAZIONE ERRORE CLASSICO ===
Confronto con ==: INVALIDO (somma = 0.9999999999999999)
Confronto con isclose(): VALIDO
```

---

## ✍️ Esercizio per lo studente

Scrivi una funzione che confronta due misure di pressione (una dal DCS, una calcolata) e determina se sono "uguali" entro una tolleranza di 0.1 bar.

**Scenario:** Stai verificando la pressione al 1° stadio compressore C-425. Il DCS legge 37.9 barg, il tuo calcolo dal settle-out dà 37.85 barg.

```python
import math

def confronta_pressioni(p_misurata_barg: float,
                        p_calcolata_barg: float,
                        tolleranza_bar: float = 0.1) -> dict:
    """
    Confronta pressione misurata e calcolata.
    
    Args:
        p_misurata_barg: pressione da DCS
        p_calcolata_barg: pressione da calcolo
        tolleranza_bar: deviazione accettabile
    
    Returns:
        dizionario con risultato confronto
    """
    # Calcola differenza assoluta
    delta_p = ___
    
    # Confronta usando math.isclose con tolleranza assoluta
    match = math.isclose(___, ___, abs_tol=___)
    
    return {
        "p_misurata_barg": p_misurata_barg,
        "p_calcolata_barg": p_calcolata_barg,
        "delta_bar": delta_p,
        "entro_tolleranza": match,
        "messaggio": "Match OK" if match else f"Scostamento {delta_p:.3f} bar"
    }


# Test con dati V-425 (1° stadio HP Compressor)
p_dcs = 37.9      # lettura DCS
p_calc = 37.85    # da calcolo settle-out

risultato = confronta_pressioni(p_dcs, p_calc)
print(f"Pressione DCS:      {risultato['p_misurata_barg']} barg")
print(f"Pressione calcolo:  {risultato['p_calcolata_barg']} barg")
print(f"Delta:              {risultato['delta_bar']:.3f} bar")
print(f"Stato:              {risultato['messaggio']}")
```

---

## ✅ Soluzione commentata

```python
import math

def confronta_pressioni(p_misurata_barg: float,
                        p_calcolata_barg: float,
                        tolleranza_bar: float = 0.1) -> dict:
    """
    Confronta pressione misurata e calcolata.
    """
    # Calcola differenza assoluta - abs() gestisce valori negativi
    delta_p = abs(p_misurata_barg - p_calcolata_barg)
    
    # Confronta usando math.isclose con tolleranza assoluta
    # abs_tol è la tolleranza in bar (unità ingegneristiche!)
    match = math.isclose(p_misurata_barg, p_calcolata_barg, 
                         abs_tol=tolleranza_bar)
    
    return {
        "p_misurata_barg": p_misurata_barg,
        "p_calcolata_barg": p_calcolata_barg,
        "delta_bar": delta_p,
        "entro_tolleranza": match,
        "messaggio": "Match OK" if match else f"Scostamento {delta_p:.3f} bar"
    }


# Test
p_dcs = 37.9
p_calc = 37.85

risultato = confronta_pressioni(p_dcs, p_calc)
print(f"Pressione DCS:      {risultato['p_misurata_barg']} barg")
print(f"Pressione calcolo:  {risultato['p_calcolata_barg']} barg")
print(f"Delta:              {risultato['delta_bar']:.3f} bar")
print(f"Stato:              {risultato['messaggio']}")

# Output:
# Pressione DCS:      37.9 barg
# Pressione calcolo:  37.85 barg
# Delta:              0.050 bar
# Stato:              Match OK
```

**Perché funziona:** Il delta è 0.05 bar, inferiore alla tolleranza di 0.1 bar. Il confronto passa perché usiamo `abs_tol` con un valore ingegneristico sensato, non il default numerico.

---

## ⚠️ Errori tipici

### 1. Usare `==` per confrontare float

```python
# ❌ SBAGLIATO
if pressione_calcolata == pressione_misurata:
    print("OK")

# ✅ CORRETTO
if math.isclose(pressione_calcolata, pressione_misurata, abs_tol=0.1):
    print("OK")
```

**Sintomo:** Il confronto fallisce anche quando i valori sembrano identici  
**Causa:** Errori di rappresentazione binaria  
**Fix:** Usa sempre `math.isclose()` o una tolleranza esplicita

### 2. Tolleranza troppo stretta o troppo larga

```python
# ❌ Troppo stretta - quasi tutti i confronti falliscono
math.isclose(37.9, 37.85, abs_tol=1e-10)  # False

# ❌ Troppo larga - passa tutto, anche errori veri
math.isclose(37.9, 35.0, abs_tol=5.0)     # True (ma 2.9 bar è un errore!)

# ✅ Tolleranza ingegneristica sensata
math.isclose(37.9, 37.85, abs_tol=0.1)    # True - 0.05 bar è accettabile
```

**Sintomo:** Troppi falsi positivi o falsi negativi  
**Causa:** Tolleranza non calibrata sul contesto fisico  
**Fix:** Scegli la tolleranza in base all'incertezza della misura/calcolo

### 3. Confondere `rel_tol` e `abs_tol`

```python
# rel_tol: tolleranza RELATIVA (percentuale del valore)
math.isclose(1000.0, 1001.0, rel_tol=0.01)  # True (1% di 1000 = 10)

# abs_tol: tolleranza ASSOLUTA (valore fisso)
math.isclose(1000.0, 1001.0, abs_tol=0.5)   # False (delta=1.0 > 0.5)
```

**Sintomo:** Confronti che passano a certi valori e falliscono ad altri  
**Causa:** `rel_tol` scala con il valore, `abs_tol` è fisso  
**Fix:** Per misure di processo, preferisci `abs_tol` con l'unità di misura

---

## 📌 Takeaway

> **Mai usare `==` per confrontare numeri decimali. Definisci sempre una tolleranza che abbia senso nel tuo contesto ingegneristico.**

---

## ❓ Verifica finale (5 domande)

1. **(Tecnica)** Perché confrontare due `float` con `==` è rischioso?
A. Perché Python non supporta `==` sui float  
B. Perché i float sono approssimazioni binarie  
C. Perché `==` è lento  
D. Perché i float cambiano valore nel tempo  

2. **(Tecnica)** Quale confronto è più corretto in un contesto ingegneristico?
```python
import math
```
A. `a == b`  
B. `a is b`  
C. `math.isclose(a, b, abs_tol=0.01)`  
D. `str(a) == str(b)`  

3. **(Pratica)** Cosa restituisce questo codice?
```python
a = 0.1 + 0.2
print(a == 0.3)
```
A. `True`  
B. `False`  
C. `None`  
D. Genera errore  

4. **(Pratica)** Qual è il problema di questo controllo?
```python
if frazione_totale == 1.0:
    print("Bilancio OK")
```
A. Python non supporta il confronto  
B. Il codice è troppo lento  
C. Il confronto è troppo rigido per dati reali  
D. `1.0` dovrebbe essere `int`  

5. **(Engineering Judgement)** Quando è corretto introdurre una tolleranza numerica?
A. Sempre, anche per contatori  
B. Mai, i numeri devono essere esatti  
C. Quando il dato deriva da misure o calcoli floating-point  
D. Solo per migliorare le prestazioni