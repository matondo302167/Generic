# Object-Oriented Programming (OOP) e Java

## 1. I Tre Pilastri di OOP

### **Incapsulamento**
L'incapsulamento consiste nel nascondere i dettagli implementativi di una classe, esponendo solo ciò che è necessario attraverso metodi pubblici. I dati vengono protetti dichiarandoli privati e fornendo metodi getter/setter per l'accesso controllato.

**Esempio pratico:**
```java
public class ContoBancario {
    private double saldo; // dato privato
    private String titolare;
    
    public ContoBancario(String titolare, double saldoIniziale) {
        this.titolare = titolare;
        this.saldo = saldoIniziale;
    }
    
    // Accesso controllato al saldo
    public double getSaldo() {
        return saldo;
    }
    
    // Metodo che implementa logica di business
    public boolean preleva(double importo) {
        if (importo > 0 && importo <= saldo) {
            saldo -= importo;
            return true;
        }
        return false;
    }
    
    public void deposita(double importo) {
        if (importo > 0) {
            saldo += importo;
        }
    }
}
```

### **Ereditarietà**
L'ereditarietà permette a una classe (sottoclasse) di acquisire proprietà e comportamenti di un'altra classe (superclasse), favorendo il riuso del codice e stabilendo relazioni gerarchiche.

**Esempio pratico:**
```java
// Classe base
public class Veicolo {
    protected String marca;
    protected int anno;
    
    public Veicolo(String marca, int anno) {
        this.marca = marca;
        this.anno = anno;
    }
    
    public void avvia() {
        System.out.println("Il veicolo si sta avviando...");
    }
}

// Classe derivata
public class Automobile extends Veicolo {
    private int numeroPorte;
    
    public Automobile(String marca, int anno, int numeroPorte) {
        super(marca, anno); // chiama il costruttore della superclasse
        this.numeroPorte = numeroPorte;
    }
    
    @Override
    public void avvia() {
        System.out.println("L'automobile " + marca + " si sta avviando con un rombo!");
    }
    
    public void apriPorte() {
        System.out.println("Apertura delle " + numeroPorte + " porte");
    }
}
```

### **Polimorfismo**
Il polimorfismo permette a oggetti di classi diverse di essere trattati attraverso la stessa interfaccia, consentendo a un metodo di comportarsi diversamente in base all'oggetto che lo invoca.

**Esempio pratico:**
```java
public abstract class Forma {
    public abstract double calcolaArea();
}

public class Cerchio extends Forma {
    private double raggio;
    
    public Cerchio(double raggio) {
        this.raggio = raggio;
    }
    
    @Override
    public double calcolaArea() {
        return Math.PI * raggio * raggio;
    }
}

public class Rettangolo extends Forma {
    private double base;
    private double altezza;
    
    public Rettangolo(double base, double altezza) {
        this.base = base;
        this.altezza = altezza;
    }
    
    @Override
    public double calcolaArea() {
        return base * altezza;
    }
}

// Utilizzo del polimorfismo
public class Main {
    public static void main(String[] args) {
        Forma[] forme = {
            new Cerchio(5),
            new Rettangolo(4, 6)
        };
        
        for (Forma forma : forme) {
            System.out.println("Area: " + forma.calcolaArea());
        }
    }
}
```

---

## 2. Polimorfismo in Java: Overloading vs Overriding

### **Overloading (Sovraccarico)**
L'overloading permette di definire più metodi con lo stesso nome ma con parametri diversi (numero, tipo o ordine). Viene risolto a **compile-time** (polimorfismo statico).

```java
public class Calcolatrice {
    // Metodi sovraccaricati
    public int somma(int a, int b) {
        return a + b;
    }
    
    public double somma(double a, double b) {
        return a + b;
    }
    
    public int somma(int a, int b, int c) {
        return a + b + c;
    }
    
    public String somma(String a, String b) {
        return a + b;
    }
}
```

### **Overriding (Sovrascrittura)**
L'overriding permette a una sottoclasse di fornire una propria implementazione di un metodo già definito nella superclasse. Viene risolto a **runtime** (polimorfismo dinamico).

```java
public class Animale {
    public void emettiVerso() {
        System.out.println("L'animale emette un verso");
    }
}

public class Cane extends Animale {
    @Override
    public void emettiVerso() {
        System.out.println("Bau bau!");
    }
}

public class Gatto extends Animale {
    @Override
    public void emettiVerso() {
        System.out.println("Miao!");
    }
}
```

### **Differenze chiave:**

| Caratteristica | Overloading | Overriding |
|---------------|-------------|------------|
| **Risoluzione** | Compile-time | Runtime |
| **Classe** | Stessa classe | Sottoclasse |
| **Parametri** | Devono essere diversi | Devono essere identici |
| **Tipo di polimorfismo** | Statico | Dinamico |
| **Return type** | Può essere diverso | Deve essere uguale o covariante |

---

## 3. Collection Framework e Stream

### **Vantaggi delle Streams API**

1. **Codice più leggibile e dichiarativo**: si esprime "cosa fare" invece di "come farlo"
2. **Operazioni funzionali**: supporto per programmazione funzionale
3. **Parallelizzazione semplice**: conversione facile a stream paralleli
4. **Operazioni lazy**: valutazione posticipata per migliori performance
5. **Meno codice boilerplate**: riduzione di variabili temporanee e cicli annidati
6. **Composizione di operazioni**: concatenazione fluida di trasformazioni

### **Esempio pratico con filter e map**

```java
import java.util.*;
import java.util.stream.*;

class Prodotto {
    private String nome;
    private double prezzo;
    private String categoria;
    
    public Prodotto(String nome, double prezzo, String categoria) {
        this.nome = nome;
        this.prezzo = prezzo;
        this.categoria = categoria;
    }
    
    public String getNome() { return nome; }
    public double getPrezzo() { return prezzo; }
    public String getCategoria() { return categoria; }
    
    @Override
    public String toString() {
        return nome + " - €" + prezzo;
    }
}

public class StreamExample {
    public static void main(String[] args) {
        List<Prodotto> prodotti = Arrays.asList(
            new Prodotto("Laptop", 899.99, "Elettronica"),
            new Prodotto("Mouse", 25.50, "Elettronica"),
            new Prodotto("Libro Java", 45.00, "Libri"),
            new Prodotto("Monitor", 199.99, "Elettronica"),
            new Prodotto("Tastiera", 75.00, "Elettronica")
        );
        
        // APPROCCIO TRADIZIONALE con ciclo for
        System.out.println("=== Approccio Tradizionale ===");
        List<String> nomiElettronicaCostosa = new ArrayList<>();
        for (Prodotto p : prodotti) {
            if (p.getCategoria().equals("Elettronica") && p.getPrezzo() > 50) {
                nomiElettronicaCostosa.add(p.getNome().toUpperCase());
            }
        }
        System.out.println(nomiElettronicaCostosa);
        
        // APPROCCIO CON STREAMS
        System.out.println("\n=== Approccio con Streams ===");
        List<String> nomiStream = prodotti.stream()
            .filter(p -> p.getCategoria().equals("Elettronica"))  // filtra per categoria
            .filter(p -> p.getPrezzo() > 50)                       // filtra per prezzo
            .map(p -> p.getNome().toUpperCase())                   // trasforma in maiuscolo
            .collect(Collectors.toList());
        System.out.println(nomiStream);
        
        // Altri esempi utili
        System.out.println("\n=== Altri esempi ===");
        
        // Calcola il prezzo medio dei prodotti elettronici
        double prezzoMedio = prodotti.stream()
            .filter(p -> p.getCategoria().equals("Elettronica"))
            .mapToDouble(Prodotto::getPrezzo)
            .average()
            .orElse(0.0);
        System.out.println("Prezzo medio elettronica: €" + prezzoMedio);
        
        // Trova il prodotto più costoso
        Optional<Prodotto> piuCostoso = prodotti.stream()
            .max(Comparator.comparingDouble(Prodotto::getPrezzo));
        piuCostoso.ifPresent(p -> System.out.println("Più costoso: " + p));
        
        // Raggruppa per categoria
        Map<String, List<Prodotto>> perCategoria = prodotti.stream()
            .collect(Collectors.groupingBy(Prodotto::getCategoria));
        System.out.println("\nProdotti per categoria:");
        perCategoria.forEach((cat, lista) -> 
            System.out.println(cat + ": " + lista.size() + " prodotti")
        );
    }
}
```

**Output:**
```
=== Approccio Tradizionale ===
[LAPTOP, MONITOR, TASTIERA]

=== Approccio con Streams ===
[LAPTOP, MONITOR, TASTIERA]

=== Altri esempi ===
Prezzo medio elettronica: €300.096
Più costoso: Laptop - €899.99

Prodotti per categoria:
Libri: 1 prodotti
Elettronica: 4 prodotti
```

---

## 4. Interfacce e Classi Astratte

### **Classi Astratte**
Una classe astratta può contenere sia metodi astratti (senza implementazione) che metodi concreti. Non può essere istanziata direttamente e serve come base per altre classi.

```java
public abstract class Dipendente {
    protected String nome;
    protected double stipendioBase;
    
    public Dipendente(String nome, double stipendioBase) {
        this.nome = nome;
        this.stipendioBase = stipendioBase;
    }
    
    // Metodo astratto - deve essere implementato dalle sottoclassi
    public abstract double calcolaStipendio();
    
    // Metodo concreto - implementazione comune
    public void stampaDettagli() {
        System.out.println("Nome: " + nome);
        System.out.println("Stipendio: €" + calcolaStipendio());
    }
    
    // Metodo concreto
    public String getNome() {
        return nome;
    }
}
```

### **Interfacce**
Un'interfaccia definisce un contratto che le classi devono rispettare. Da Java 8 può contenere metodi default e statici con implementazione.

```java
public interface Pagabile {
    // Costante (implicitamente public static final)
    double TASSO_IVA = 0.22;
    
    // Metodo astratto (implicitamente public abstract)
    double calcolaPagamento();
    
    // Metodo default (da Java 8)
    default double calcolaPagamentoConIva() {
        return calcolaPagamento() * (1 + TASSO_IVA);
    }
    
    // Metodo statico (da Java 8)
    static double applicaSconto(double importo, double percentualeSconto) {
        return importo * (1 - percentualeSconto / 100);
    }
}
```

### **Esempio completo: combinazione di classe astratta e interfacce**

```java
// Interfaccia per comportamento bonus
interface Bonusabile {
    double calcolaBonus();
}

// Classe astratta base
abstract class Dipendente {
    protected String nome;
    protected double stipendioBase;
    
    public Dipendente(String nome, double stipendioBase) {
        this.nome = nome;
        this.stipendioBase = stipendioBase;
    }
    
    public abstract double calcolaStipendio();
    
    public void stampaDettagli() {
        System.out.println("Dipendente: " + nome);
        System.out.println("Stipendio totale: €" + calcolaStipendio());
    }
}

// Classe concreta che estende e implementa
class Manager extends Dipendente implements Pagabile, Bonusabile {
    private int numeroSubordinati;
    
    public Manager(String nome, double stipendioBase, int numeroSubordinati) {
        super(nome, stipendioBase);
        this.numeroSubordinati = numeroSubordinati;
    }
    
    @Override
    public double calcolaStipendio() {
        return stipendioBase + calcolaBonus();
    }
    
    @Override
    public double calcolaPagamento() {
        return calcolaStipendio();
    }
    
    @Override
    public double calcolaBonus() {
        return numeroSubordinati * 100; // €100 per subordinato
    }
}

class Sviluppatore extends Dipendente implements Pagabile {
    private String linguaggioPrincipale;
    
    public Sviluppatore(String nome, double stipendioBase, String linguaggio) {
        super(nome, stipendioBase);
        this.linguaggioPrincipale = linguaggio;
    }
    
    @Override
    public double calcolaStipendio() {
        return stipendioBase;
    }
    
    @Override
    public double calcolaPagamento() {
        return calcolaStipendio();
    }
}
```

### **Quando usare cosa?**

**Usa una Classe Astratta quando:**
- Vuoi condividere codice tra classi strettamente correlate
- Hai metodi con implementazione comune
- Vuoi definire campi non-static o non-final
- Serve una relazione "è un" forte

**Usa un'Interfaccia quando:**
- Vuoi definire un contratto per classi non correlate
- Serve ereditarietà multipla (una classe può implementare più interfacce)
- Vuoi definire capacità che classi diverse possono avere
- Serve una relazione "può fare" o "ha capacità di"

**Differenze principali:**

| Caratteristica | Classe Astratta | Interfaccia |
|---------------|-----------------|-------------|
| **Ereditarietà** | Singola (extends) | Multipla (implements) |
| **Campi** | Qualsiasi tipo | Solo public static final |
| **Costruttori** | Sì | No |
| **Metodi** | Astratti e concreti | Astratti, default, static |
| **Modificatori** | Qualsiasi | public (implicit) |
| **Quando usare** | Relazione forte "è un" | Contratto/capacità |

Spero che questa spiegazione completa ti sia utile! Hai domande su qualche aspetto specifico?
