# Simulazione di Colloquio: Junior Software Engineer

## 1. Dart/Flutter (e Paragone con React Native)

### 1. Dart vs. JavaScript/TypeScript: Esecuzione e Tipizzazione

#### 📚 Definizioni Fondamentali
* **Dart:** Linguaggio di programmazione ottimizzato per il client, sviluppato da Google, usato per Flutter.
* **TypeScript:** Un superset di JavaScript che aggiunge la **tipizzazione statica**.
* **Compilazione JIT (Just-in-Time):** Avviene durante l'esecuzione (tipica in sviluppo) per cicli rapidi (*hot reload*).
* **Compilazione AOT (Ahead-of-Time):** Avviene prima dell'esecuzione per codice nativo e alte prestazioni in produzione.

#### 💬 Risposta
Le principali differenze riguardano l'**esecuzione** e la **tipizzazione**:

| Caratteristica | Dart | JavaScript/TypeScript |
| :--- | :--- | :--- |
| **Esecuzione (AOT/JIT)** | Supporta **entrambi**: **JIT** per *hot reload* in sviluppo; **AOT** per codice nativo in produzione. | JS puro è JIT/interpretato. TS è transpila in JS, che è poi JIT/interpretato. |
| **Tipizzazione** | **Forte e Statica**. I tipi sono noti a tempo di compilazione. | JS: Debole e Dinamica. TS: **Forte e Statica** (tramite il superset). |

---

### 2. Flutter vs. React Native: Modello di Rendering

#### 📚 Definizioni Fondamentali
* **Flutter:** Framework UI che usa Dart per app multipiattaforma.
* **Modello di Rendering di Flutter (Skia Engine):** Flutter *disegna* l'intera UI pixel per pixel utilizzando il proprio motore grafico **Skia**, senza usare i widget nativi.

#### 💬 Risposta
L'approccio di Flutter si distingue per il suo modello di rendering:

* **Approccio "Everything is a Widget" di Flutter:** Tutto è un widget, da un semplice testo a un layout complesso. Flutter disegna questi widget direttamente sullo schermo utilizzando Skia, eliminando la necessità di un **JavaScript Bridge**.
* **Contrasto con React Native:** React Native usa il **JavaScript Bridge** per comunicare con le API native e renderizzare i **componenti UI nativi** della piattaforma (Android/iOS). L'assenza del bridge in Flutter garantisce performance superiori e una maggiore consistenza grafica.

---

### 3. Programmazione Asincrona: Gestione degli Errori

#### 📚 Definizioni Fondamentali
* **`Future` (Dart):** Un oggetto che rappresenta un valore o un errore che sarà disponibile in un momento futuro.
* **`async` / `await`:** Parole chiave per semplificare la scrittura di codice asincrono.

#### 💬 Risposta
In Dart, gli errori in una catena di operazioni asincrone si gestiscono principalmente tramite i blocchi **`try-catch`** con `async/await`.

```dart
Future<void> eseguiCatenaAsincrona() async {
  try {
    var datiIniziali = await leggiDatiDaAPI(); 
    var datiElaborati = await elaboraDati(datiIniziali); 
    await salvaDati(datiElaborati); 
  } catch (e) {
    // Se un errore si verifica in qualsiasi 'await', l'esecuzione salta qui.
    print('Errore gestito nella catena: $e');
  }
}
