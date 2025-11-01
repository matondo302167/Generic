# Gestione Dati e Database - Risposte Dettagliate

## 1. Ciclo di Vita di una Richiesta HTTP (API RESTful → Front-End)

### **Backend: Spring Boot (Java)**

#### **Controller Layer**
```java
@RestController
@RequestMapping("/api/users")
@CrossOrigin(origins = "http://localhost:4200")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @GetMapping("/{id}")
    public ResponseEntity<UserDTO> getUserById(@PathVariable Long id) {
        UserDTO user = userService.findById(id);
        return ResponseEntity.ok(user);
    }
    
    @PostMapping
    public ResponseEntity<UserDTO> createUser(@RequestBody @Valid UserDTO userDTO) {
        UserDTO createdUser = userService.create(userDTO);
        return ResponseEntity.status(HttpStatus.CREATED).body(createdUser);
    }
}
```

#### **Service Layer**
```java
@Service
@Transactional
public class UserService {
    
    @Autowired
    private UserRepository userRepository;
    
    public UserDTO findById(Long id) {
        User user = userRepository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException("User not found"));
        return convertToDTO(user);
    }
    
    public UserDTO create(UserDTO userDTO) {
        User user = convertToEntity(userDTO);
        User savedUser = userRepository.save(user);
        return convertToDTO(savedUser);
    }
    
    private UserDTO convertToDTO(User user) {
        return new UserDTO(user.getId(), user.getName(), user.getEmail());
    }
}
```

#### **Repository Layer**
```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
}
```

#### **DTO (Data Transfer Object)**
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class UserDTO {
    private Long id;
    
    @NotBlank(message = "Name is required")
    private String name;
    
    @Email(message = "Email should be valid")
    private String email;
}
```

---

### **Front-End: Angular**

#### **Service**
```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {
  private apiUrl = 'http://localhost:8080/api/users';

  constructor(private http: HttpClient) {}

  getUserById(id: number): Observable<User> {
    return this.http.get<User>(`${this.apiUrl}/${id}`).pipe(
      catchError(this.handleError)
    );
  }

  createUser(user: User): Observable<User> {
    return this.http.post<User>(this.apiUrl, user, {
      headers: new HttpHeaders({ 'Content-Type': 'application/json' })
    }).pipe(
      catchError(this.handleError)
    );
  }

  private handleError(error: HttpErrorResponse) {
    console.error('An error occurred:', error.message);
    return throwError(() => new Error('Something went wrong'));
  }
}
```

#### **Component**
```typescript
@Component({
  selector: 'app-user-detail',
  templateUrl: './user-detail.component.html'
})
export class UserDetailComponent implements OnInit {
  user: User | null = null;
  loading = false;
  error: string | null = null;

  constructor(
    private userService: UserService,
    private route: ActivatedRoute
  ) {}

  ngOnInit(): void {
    const id = Number(this.route.snapshot.paramMap.get('id'));
    this.loadUser(id);
  }

  loadUser(id: number): void {
    this.loading = true;
    this.userService.getUserById(id).subscribe({
      next: (data) => {
        this.user = data;
        this.loading = false;
      },
      error: (err) => {
        this.error = 'Failed to load user';
        this.loading = false;
      }
    });
  }
}
```

---

### **Front-End: React**

#### **Service (usando Axios)**
```javascript
import axios from 'axios';

const API_URL = 'http://localhost:8080/api/users';

export const userService = {
  getUserById: async (id) => {
    try {
      const response = await axios.get(`${API_URL}/${id}`);
      return response.data;
    } catch (error) {
      throw new Error('Failed to fetch user');
    }
  },

  createUser: async (user) => {
    try {
      const response = await axios.post(API_URL, user, {
        headers: { 'Content-Type': 'application/json' }
      });
      return response.data;
    } catch (error) {
      throw new Error('Failed to create user');
    }
  }
};
```

#### **Component (usando Hooks)**
```javascript
import React, { useState, useEffect } from 'react';
import { userService } from './services/userService';

function UserDetail({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchUser = async () => {
      try {
        setLoading(true);
        const data = await userService.getUserById(userId);
        setUser(data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchUser();
  }, [userId]);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div>
      <h2>{user.name}</h2>
      <p>Email: {user.email}</p>
    </div>
  );
}
```

---

### **Flusso Completo della Richiesta**

1. **Front-End** → L'utente clicca su un pulsante/link
2. **HTTP Request** → Angular/React invia GET/POST a `http://localhost:8080/api/users/{id}`
3. **Controller** → Riceve la richiesta, valida i parametri
4. **Service** → Applica la business logic
5. **Repository** → Interroga il database (Oracle/PostgreSQL)
6. **Database** → Restituisce i dati
7. **Conversione** → Entity → DTO (oggetto Java → JSON tramite Jackson)
8. **HTTP Response** → JSON viene inviato al front-end
9. **Parsing** → Angular/React deserializza il JSON in oggetto TypeScript/JavaScript
10. **Rendering** → I dati vengono visualizzati nella UI

---

## 2. Liquibase per la Gestione delle Migrazioni

### **Cos'è Liquibase?**
Liquibase è uno strumento di **database migration** che permette di gestire l'evoluzione dello schema del database attraverso file di configurazione versionati (XML, YAML, JSON o SQL).

### **Esempio di Changelog Liquibase**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
    xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
    http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-4.0.xsd">

    <changeSet id="1" author="mario.rossi">
        <createTable tableName="users">
            <column name="id" type="BIGINT" autoIncrement="true">
                <constraints primaryKey="true" nullable="false"/>
            </column>
            <column name="name" type="VARCHAR(100)">
                <constraints nullable="false"/>
            </column>
            <column name="email" type="VARCHAR(100)">
                <constraints unique="true" nullable="false"/>
            </column>
            <column name="created_at" type="TIMESTAMP" defaultValueComputed="CURRENT_TIMESTAMP"/>
        </createTable>
    </changeSet>

    <changeSet id="2" author="mario.rossi">
        <addColumn tableName="users">
            <column name="phone" type="VARCHAR(20)"/>
        </addColumn>
    </changeSet>

    <changeSet id="3" author="mario.rossi">
        <createIndex indexName="idx_users_email" tableName="users">
            <column name="email"/>
        </createIndex>
    </changeSet>

</databaseChangeLog>
```

### **Configurazione in Spring Boot**

```yaml
# application.yml
spring:
  liquibase:
    enabled: true
    change-log: classpath:db/changelog/db.changelog-master.xml
    contexts: dev, prod
```

---

### **Vantaggi di Liquibase rispetto agli Script SQL Manuali**

| **Aspetto** | **Liquibase** | **Script SQL Manuali** |
|-------------|---------------|------------------------|
| **Versionamento** | Ogni changeset ha un ID univoco tracciato | Difficile tenere traccia delle modifiche applicate |
| **Rollback** | Rollback automatico con `liquibase rollback` | Bisogna scrivere manualmente script di rollback |
| **Indipendenza dal DB** | Supporta Oracle, PostgreSQL, MySQL, etc. | Script specifici per ogni database |
| **Controllo Stato** | Tabella `DATABASECHANGELOG` traccia le migrazioni | Nessun tracking automatico |
| **Ambienti Multipli** | Contexts e labels per ambienti diversi (dev/prod) | Gestione manuale complessa |
| **CI/CD Integration** | Si integra facilmente nelle pipeline | Richiede script custom |
| **Prevenzione Errori** | Checksum rileva modifiche non autorizzate | Nessuna protezione |
| **Team Collaboration** | Tutti lavorano sugli stessi changelog versionati | Conflitti frequenti tra sviluppatori |

---

## 3. Creazione e Modifica Tabelle in Oracle e PostgreSQL

### **Creazione di una Tabella**

#### **Oracle**
```sql
-- Creazione tabella con sequence per auto-increment
CREATE TABLE employees (
    employee_id NUMBER GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
    first_name VARCHAR2(50) NOT NULL,
    last_name VARCHAR2(50) NOT NULL,
    email VARCHAR2(100) UNIQUE,
    hire_date DATE DEFAULT SYSDATE,
    salary NUMBER(10,2) CHECK (salary > 0),
    department_id NUMBER,
    CONSTRAINT fk_department FOREIGN KEY (department_id) 
        REFERENCES departments(department_id)
);

-- Creazione indice
CREATE INDEX idx_emp_lastname ON employees(last_name);

-- Aggiungere commenti
COMMENT ON TABLE employees IS 'Tabella dipendenti';
COMMENT ON COLUMN employees.salary IS 'Salario mensile in EUR';
```

#### **PostgreSQL**
```sql
-- Creazione tabella con SERIAL per auto-increment
CREATE TABLE employees (
    employee_id SERIAL PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    hire_date DATE DEFAULT CURRENT_DATE,
    salary NUMERIC(10,2) CHECK (salary > 0),
    department_id INTEGER,
    CONSTRAINT fk_department FOREIGN KEY (department_id) 
        REFERENCES departments(department_id) ON DELETE SET NULL
);

-- Creazione indice
CREATE INDEX idx_emp_lastname ON employees(last_name);

-- Aggiungere commenti
COMMENT ON TABLE employees IS 'Tabella dipendenti';
COMMENT ON COLUMN employees.salary IS 'Salario mensile in EUR';
```

---

### **Modifica di una Tabella (ALTER TABLE)**

#### **Oracle**
```sql
-- Aggiungere una colonna
ALTER TABLE employees ADD phone_number VARCHAR2(20);

-- Modificare una colonna
ALTER TABLE employees MODIFY salary NUMBER(12,2);

-- Rinominare una colonna
ALTER TABLE employees RENAME COLUMN phone_number TO mobile;

-- Eliminare una colonna
ALTER TABLE employees DROP COLUMN mobile;

-- Aggiungere un constraint
ALTER TABLE employees ADD CONSTRAINT chk_email CHECK (email LIKE '%@%');

-- Disabilitare/abilitare constraint
ALTER TABLE employees DISABLE CONSTRAINT chk_email;
ALTER TABLE employees ENABLE CONSTRAINT chk_email;

-- Eliminare constraint
ALTER TABLE employees DROP CONSTRAINT chk_email;
```

#### **PostgreSQL**
```sql
-- Aggiungere una colonna
ALTER TABLE employees ADD COLUMN phone_number VARCHAR(20);

-- Modificare il tipo di una colonna
ALTER TABLE employees ALTER COLUMN salary TYPE NUMERIC(12,2);

-- Impostare un valore di default
ALTER TABLE employees ALTER COLUMN hire_date SET DEFAULT CURRENT_DATE;

-- Rinominare una colonna
ALTER TABLE employees RENAME COLUMN phone_number TO mobile;

-- Eliminare una colonna
ALTER TABLE employees DROP COLUMN mobile;

-- Aggiungere un constraint
ALTER TABLE employees ADD CONSTRAINT chk_email CHECK (email LIKE '%@%');

-- Eliminare constraint
ALTER TABLE employees DROP CONSTRAINT chk_email;

-- Aggiungere NOT NULL
ALTER TABLE employees ALTER COLUMN email SET NOT NULL;
```

---

### **Cos'è una Transazione?**

Una **transazione** è un'unità logica di lavoro che raggruppa una o più operazioni SQL. Le transazioni garantiscono le proprietà **ACID**:

- **Atomicity (Atomicità)**: Tutte le operazioni vengono eseguite o nessuna
- **Consistency (Consistenza)**: Il database passa da uno stato valido a un altro stato valido
- **Isolation (Isolamento)**: Le transazioni concorrenti non interferiscono tra loro
- **Durability (Durabilità)**: Una volta committata, la transazione è permanente

#### **Esempio di Transazione**

**Oracle:**
```sql
-- Inizio transazione (implicito in Oracle)
UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;

-- Se tutto è OK
COMMIT;

-- Se c'è un errore
ROLLBACK;
```

**PostgreSQL:**
```sql
-- Inizio esplicito della transazione
BEGIN;

UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;

-- Se tutto è OK
COMMIT;

-- Se c'è un errore
ROLLBACK;
```

#### **Transazioni in Spring Boot**

```java
@Service
public class TransferService {
    
    @Autowired
    private AccountRepository accountRepository;
    
    @Transactional(rollbackFor = Exception.class)
    public void transferMoney(Long fromId, Long toId, BigDecimal amount) {
        Account fromAccount = accountRepository.findById(fromId)
            .orElseThrow(() -> new AccountNotFoundException());
        
        Account toAccount = accountRepository.findById(toId)
            .orElseThrow(() -> new AccountNotFoundException());
        
        if (fromAccount.getBalance().compareTo(amount) < 0) {
            throw new InsufficientFundsException();
        }
        
        // Se una di queste operazioni fallisce, viene eseguito il ROLLBACK automatico
        fromAccount.setBalance(fromAccount.getBalance().subtract(amount));
        toAccount.setBalance(toAccount.getBalance().add(amount));
        
        accountRepository.save(fromAccount);
        accountRepository.save(toAccount);
        
        // Se tutto va bene, viene eseguito il COMMIT automatico
    }
}
```

#### **Livelli di Isolamento delle Transazioni**

| **Livello** | **Descrizione** | **Anomalie Previene** |
|-------------|-----------------|----------------------|
| **READ UNCOMMITTED** | Legge dati non ancora committati | Nessuna |
| **READ COMMITTED** | Legge solo dati committati (default PostgreSQL) | Dirty Read |
| **REPEATABLE READ** | Letture ripetibili nella stessa transazione | Dirty Read, Non-Repeatable Read |
| **SERIALIZABLE** | Massimo isolamento (default Oracle) | Tutte le anomalie |

```sql
-- PostgreSQL: impostare livello di isolamento
BEGIN TRANSACTION ISOLATION LEVEL SERIALIZABLE;
-- operazioni...
COMMIT;
```

---
