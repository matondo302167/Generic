# Risposte Front-End Web Development

## 1. JavaScript vs. TypeScript: Benefici in progetti grandi e complessi

L'adozione di **TypeScript** in progetti enterprise porta vantaggi significativi:

### **Type Safety & Error Prevention**
- **Errori a compile-time** invece che a runtime: il type checker identifica errori prima dell'esecuzione
- **Refactoring sicuro**: cambiare interfacce o strutture dati evidenzia immediatamente tutti i punti da aggiornare
- **Meno bug in produzione**: molti errori comuni (typo, null/undefined, parametri errati) vengono catturati durante lo sviluppo

### **Developer Experience & Scalabilità**
- **IntelliSense avanzato**: autocompletamento preciso, documentazione inline, navigazione al codice
- **Contratti espliciti**: interfacce e tipi documentano le API interne senza bisogno di commenti
- **Onboarding più rapido**: nuovi sviluppatori comprendono il codice più velocemente grazie ai tipi espliciti

### **Manutenibilità**
```typescript
// TypeScript: contratto chiaro e verificabile
interface UserService {
  getUser(id: string): Promise<User>;
  updateUser(id: string, data: Partial<User>): Promise<void>;
}

// JavaScript: necessita documentazione JSDoc e test più estesi
// per garantire lo stesso livello di sicurezza
```

### **Tooling & Ecosistema**
- Migliore integrazione con IDE moderni
- Supporto per feature moderne (decorators, enums, generics)
- Interoperabilità con librerie JavaScript esistenti tramite DefinitelyTyped

---

## 2. Angular: Component e Data Binding

### **Ruolo del Component**
In Angular, il **Component** è l'elemento fondamentale dell'architettura e rappresenta:

- **Unità logica e visuale**: combina template HTML, logica TypeScript e stili CSS
- **Building block riutilizzabile**: encapsula funzionalità specifiche dell'UI
- **Gestione dello stato locale**: mantiene dati e comportamenti della porzione di interfaccia

```typescript
@Component({
  selector: 'app-user-profile',
  templateUrl: './user-profile.component.html',
  styleUrls: ['./user-profile.component.css']
})
export class UserProfileComponent implements OnInit {
  user: User;
  isEditing: boolean = false;
  
  ngOnInit() {
    // Inizializzazione
  }
}
```

### **Data Binding in Angular**

#### **1. Binding Unidirezionale**

**a) Interpolation (Component → Template)**
```typescript
// Component
title = 'Dashboard';

// Template
<h1>{{ title }}</h1>
```

**b) Property Binding (Component → Template)**
```typescript
// Component
imageUrl = 'assets/logo.png';
isDisabled = false;

// Template
<img [src]="imageUrl">
<button [disabled]="isDisabled">Click</button>
```

**c) Event Binding (Template → Component)**
```typescript
// Component
handleClick() {
  console.log('Button clicked');
}

// Template
<button (click)="handleClick()">Submit</button>
```

#### **2. Binding Bidirezionale (Two-Way Binding)**

Utilizza la direttiva **`[(ngModel)]`** (sintassi "banana in a box"):

```typescript
// Component
username: string = '';

// Template
<input [(ngModel)]="username">
<p>Ciao, {{ username }}!</p>
```

**Funzionamento interno:**
```html
<!-- Two-way binding è syntactic sugar per: -->
<input 
  [ngModel]="username" 
  (ngModelChange)="username = $event">
```

Il flusso bidirezionale:
1. **Component → Template**: il valore iniziale viene mostrato nell'input
2. **Template → Component**: ogni modifica dell'input aggiorna automaticamente la proprietà

---

## 3. Angular vs React.js: Differenze Architettoniche

### **Filosofia di Base**

| Aspetto | Angular | React.js |
|---------|---------|----------|
| **Tipo** | Framework completo (opinionated) | Libreria UI (unopinionated) |
| **Architettura** | MVC/MVVM completo | Component-based puro |
| **Paradigma** | Object-Oriented + Declarative | Functional + Declarative |

### **Component Architecture**

#### **Angular: Framework MVC/MVVM**
```typescript
// Struttura stratificata con separazione netta
@Component({...})
export class ProductComponent {
  // Model/State
  products: Product[];
  
  // Dependency Injection
  constructor(private productService: ProductService) {}
  
  // Controller logic
  ngOnInit() {
    this.productService.getProducts().subscribe(
      data => this.products = data
    );
  }
}
```

**Caratteristiche:**
- **Dependency Injection nativo**: gestione centralizzata delle dipendenze
- **Two-way binding nativo**: sincronizzazione automatica Model-View
- **Moduli strutturati**: NgModules organizzano l'applicazione
- **Tooling integrato**: CLI, routing, forms, HTTP client inclusi

#### **React: Component-based Puro**
```typescript
// Component funzionale con hooks
function ProductList() {
  // State locale
  const [products, setProducts] = useState<Product[]>([]);
  
  // Side effects
  useEffect(() => {
    fetchProducts().then(data => setProducts(data));
  }, []);
  
  return <div>{/* JSX */}</div>;
}
```

**Caratteristiche:**
- **Unidirezionale**: flusso dati esplicito (top-down)
- **Composizione**: combina componenti piccoli e focalizzati
- **Ecosistema aperto**: scegli librerie per routing, state management, forms
- **JSX**: JavaScript e markup unificati

### **Data Flow & State Management**

**Angular:**
- RxJS e Observables per reattività
- Services condivisi con DI
- Two-way binding per forms

**React:**
- Props unidirezionali (parent → child)
- State sollevato (lifting state up)
- Context API o Redux per stato globale

### **Quando Scegliere Cosa**

**Angular** è preferibile per:
- Applicazioni enterprise complesse
- Team che necessitano struttura rigida
- Progetti con requirements completi (auth, routing, forms)

**React** è preferibile per:
- Flessibilità architetturale
- Team con preferenze functional programming
- Progetti che necessitano graduale adozione o integrazione con sistemi esistenti

---

**In sintesi**: Angular offre una soluzione "batteries-included" con convenzioni forti, mentre React privilegia flessibilità e composizione, richiedendo scelte architetturali esplicite da parte del team.
