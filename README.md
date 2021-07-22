# Clean Code PHP

## Tabella dei contenuti

  1. [Introduzione](#introduction)
  2. [Variabili](#variables)
     * [Utilizzare nomi di variabili significativi e pronunciabili](#use-meaningful-and-pronounceable-variable-names)
     * [Utilizzare lo stesso vocabolario per lo stesso tipo di variabile](#use-the-same-vocabulary-for-the-same-type-of-variable)
     * [Utilizzare nomi ricercabili (parte 1)](#use-searchable-names-part-1)
     * [Utilizzare nomi ricercabili (parte 2)](#use-searchable-names-part-2)
     * [Utilizzare variabili esplicative](#use-explanatory-variables)
     * [Evitare di annidarsi troppo profondamente (parte 1)](#avoid-nesting-too-deeply-and-return-early-part-1)
     * [Evitare di annidarsi troppo profondamente (parte 2)](#avoid-nesting-too-deeply-and-return-early-part-2)
     * [Evitare la mappatura mentale](#avoid-mental-mapping)
     * [Non aggiungere un contesto non necessario](#dont-add-unneeded-context)
  3. [Confronto](#comparison)
     * [Usare un confronto identico](#use-identical-comparison)
     * [Operatore Null coalescing](#null-coalescing-operator)
  4. [Funzioni](#functions)
     * [Utilizzare argomenti predefiniti invece di cortocircuiti o condizionali](#use-default-arguments-instead-of-short-circuiting-or-conditionals)
     * [Argomenti della funzione (idealmente 2 o meno)](#function-arguments-2-or-fewer-ideally)
     * [I nomi delle funzioni dovrebbero dire cosa fanno](#function-names-should-say-what-they-do)
     * [Le funzioni dovrebbero essere solo un livello di astrazione](#functions-should-only-be-one-level-of-abstraction)
     * [Non utilizzare flag come parametri di funzione](#dont-use-flags-as-function-parameters)
     * [Evitare gli effetti collaterali](#avoid-side-effects)
     * [Non scrivere nelle funzioni gloabli](#dont-write-to-global-functions)
     * [Non utilizzare un Singleton pattern](#dont-use-a-singleton-pattern)
     * [Incapsulare condizionali](#encapsulate-conditionals)
     * [Evitare condizionali negativi](#avoid-negative-conditionals)
     * [Evitare conditionali](#avoid-conditionals)
     * [Evitare il controllo dei tipi (parte 1)](#avoid-type-checking-part-1)
     * [Evitare il controllo dei tipi (parte 2)](#avoid-type-checking-part-2)
     * [Rimuovere il codice morto](#remove-dead-code)
  5. [Oggetti e strutture dati](#objects-and-data-structures)
     * [Utilizzare l'incapsulamente degli oggetti](#use-object-encapsulation)
     * [Fare in modo che gli oggetti abbiano membri privati/protetti](#make-objects-have-privateprotected-members)
  6. [Classi](#classes)
     * [Preferire la composizione all'ereditarietà](#prefer-composition-over-inheritance)
     * [Evitare interfacce fluide](#avoid-fluent-interfaces)
     * [Preferire classi final](#prefer-final-classes)
  7. [SOLID](#solid)
     * [Single Responsibility Principle (SRP)](#single-responsibility-principle-srp)
     * [Open/Closed Principle (OCP)](#openclosed-principle-ocp)
     * [Liskov Substitution Principle (LSP)](#liskov-substitution-principle-lsp)
     * [Interface Segregation Principle (ISP)](#interface-segregation-principle-isp)
     * [Dependency Inversion Principle (DIP)](#dependency-inversion-principle-dip)
  8. [Don’t repeat yourself (DRY)](#dont-repeat-yourself-dry)
  9. [Traduzioni](#translations)

## Introduzione

Principi di ingengeria del software, dal libro di Robert C. Martin
[*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882),
adattato per PHP. Questa non è una guida di stile. È una guida per produrre
software leggibile, riutilizzabile e rifattorizzabile in PHP.

Non tutti i principi qui contenuti devono essere seguiti rigorosamente, e ancora meno saranno universalmente d'accordo. Queste sono linee guida e niente di più, ma sono quelle codificate in molti anni di esperienza collettiva dagli autori di *Clean Code*.

Ispirato da [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript).

Anche se molti sviluppatori utilizzano ancora PHP 5, la maggior parte degli esempi in questo articolo funziona solo con PHP 7.1+.

## Variabili

### Utilizzare nomi di variabili significativi e pronunciabili

**Male:**

```php
$ymdstr = $moment->format('y-m-d');
```

**Bene:**

```php
$currentDate = $moment->format('y-m-d');
```

**[⬆ torna all'inizio](#tabella-dei-contenuti)**

### Utilizzare lo stesso vocabolario per lo stesso tipo di variabile

**Male:**

```php
getUserInfo();
getUserData();
getUserRecord();
getUserProfile();
```

**Bene:**

```php
getUser();
```

**[⬆ torna all'inizio](#table-of-contents)**

### Utilizzare nomi ricercabili (parte 1)

Leggeremo più codice di quanto ne scriveremo. È importante che il codice che scriviamo sia leggibile e ricercabile. Non utilizzando nomi delle variabili significativi per la comprensione del nostro programma danneggiamo i nostri lettori.
Rendete i vostri nomi ricercabili.

**Male:**

```php
// A che cosa serve il 448?
$result = $serializer->serialize($data, 448);
```

**Bene:**

```php
$json = $serializer->serialize($data, JSON_UNESCAPED_SLASHES | JSON_PRETTY_PRINT | JSON_UNESCAPED_UNICODE);
```

### Utilizzare nomi ricercabili (parte 2)

**Male:**

```php
class User
{
    // A cosa serve il 7?
    public $access = 7;
}

// A cosa serve il 4?
if ($user->access & 4) {
    // ...
}

// Cosa succede qui?
$user->access ^= 2;
```

**Bene:**

```php
class User
{
    public const ACCESS_READ = 1;

    public const ACCESS_CREATE = 2;

    public const ACCESS_UPDATE = 4;

    public const ACCESS_DELETE = 8;

    // L'utente come predefinito può leggere, creare e aggiornare qualcosa
    public $access = self::ACCESS_READ | self::ACCESS_CREATE | self::ACCESS_UPDATE;
}

if ($user->access & User::ACCESS_UPDATE) {
    // modificare ...
}

// Nega i diritti di accesso per creare qualcosa
$user->access ^= User::ACCESS_CREATE;
```

**[⬆ torna all'inizio](#table-of-contents)**

### Utilizzare variabili esplicative

**Male:**

```php
$address = 'One Infinite Loop, Cupertino 95014';
$cityZipCodeRegex = '/^[^,]+,\s*(.+?)\s*(\d{5})$/';
preg_match($cityZipCodeRegex, $address, $matches);

saveCityZipCode($matches[1], $matches[2]);
```

**Non male:**

È meglio, ma siamo ancora pesantemente dipendenti dalla regex.

```php
$address = 'One Infinite Loop, Cupertino 95014';
$cityZipCodeRegex = '/^[^,]+,\s*(.+?)\s*(\d{5})$/';
preg_match($cityZipCodeRegex, $address, $matches);

[, $city, $zipCode] = $matches;
saveCityZipCode($city, $zipCode);
```

**Bene:**

Diminuire la dipendenza dalla regex nominando le subpatterns.

```php
$address = 'One Infinite Loop, Cupertino 95014';
$cityZipCodeRegex = '/^[^,]+,\s*(?<city>.+?)\s*(?<zipCode>\d{5})$/';
preg_match($cityZipCodeRegex, $address, $matches);

saveCityZipCode($matches['city'], $matches['zipCode']);
```

**[⬆ torna all'inizio](#table-of-contents)**

### Evitare di annidarsi troppo profondamente (parte 1)

Troppe dichiarazioni if-else possono rendere il vostro codice difficile da seguire. Esplicito è meglio
che implicito.

**Male:**

```php
function isShopOpen($day): bool
{
    if ($day) {
        if (is_string($day)) {
            $day = strtolower($day);
            if ($day === 'friday') {
                return true;
            } elseif ($day === 'saturday') {
                return true;
            } elseif ($day === 'sunday') {
                return true;
            }
            return false;
        }
        return false;
    }
    return false;
}
```

**Bene:**

```php
function isShopOpen(string $day): bool
{
    if (empty($day)) {
        return false;
    }

    $openingDays = ['friday', 'saturday', 'sunday'];

    return in_array(strtolower($day), $openingDays, true);
}
```

**[⬆ torna all'inizio](#table-of-contents)**

### Evitare di annidarsi troppo profondamente (parte 2)

**Male:**

```php
function fibonacci(int $n)
{
    if ($n < 50) {
        if ($n !== 0) {
            if ($n !== 1) {
                return fibonacci($n - 1) + fibonacci($n - 2);
            }
            return 1;
        }
        return 0;
    }
    return 'Not supported';
}
```

**Bene:**

```php
function fibonacci(int $n): int
{
    if ($n === 0 || $n === 1) {
        return $n;
    }

    if ($n >= 50) {
        throw new Exception('Not supported');
    }

    return fibonacci($n - 1) + fibonacci($n - 2);
}
```

**[⬆ torna all'inizio](#table-of-contents)**

### Evitare la mappatura mentale

Non forzate il lettore del vostro codice a tradurre il significato della variabile.
Esplicito è meglio di implicito.

**Male:**

```php
$l = ['Austin', 'New York', 'San Francisco'];

for ($i = 0; $i < count($l); $i++) {
    $li = $l[$i];
    doStuff();
    doSomeOtherStuff();
    // ...
    // ...
    // ...
    // Wait, what is `$li` for again?
    dispatch($li);
}
```

**Bene:**

```php
$locations = ['Austin', 'New York', 'San Francisco'];

foreach ($locations as $location) {
    doStuff();
    doSomeOtherStuff();
    // ...
    // ...
    // ...
    dispatch($location);
}
```

**[⬆ torna all'inizio](#table-of-contents)**

### Non aggiungere un contesto non necessario

Se il nome della vostra classe/oggetto vi dice qualcosa, non ripetetelo nel vostro
nome della variabile.

**Male:**

```php
class Car
{
    public $carMake;

    public $carModel;

    public $carColor;

    //...
}
```

**Bene:**

```php
class Car
{
    public $make;

    public $model;

    public $color;

    //...
}
```

**[⬆ torna all'inizio](#table-of-contents)**

## Confronto

### Usare un [confronto identico](http://php.net/manual/en/language.operators.comparison.php)

**Non male:**

Il confronto semplice convertirà la stringa in un intero.

```php
$a = '42';
$b = 42;

if ($a != $b) {
    // L'espressione passerà sempre
}
```

Il confronto `$a != $b` restituisce `FALSE` ma in realtà è `TRUE`!
La stringa `42` è diversa dall'intero `42`.

**Bene:**

Il confronto identico confronta il tipo e il valore.

```php
$a = '42';
$b = 42;

if ($a !== $b) {
    // L'espressione è verificata
}
```

Il confronto `$a !== $b` restituisce `TRUE`.

**[⬆ torna all'inizio](#table-of-contents)**

### Operatore Null coalescing

Il Null coalescing è un nuovo operatore [introdotto in PHP 7](https://www.php.net/manual/en/migration70.new-features.php). L'operatore di null coalescing `??` è stato aggiunto come syntactic sugar per il caso comune di dover utilizzare un ternario assieme a `isset()`. Restituisce il suo primo operando se esiste e non è `null`; altrimenti restituisce il suo secondo operando.

**Male:**

```php
if (isset($_GET['name'])) {
    $name = $_GET['name'];
} elseif (isset($_POST['name'])) {
    $name = $_POST['name'];
} else {
    $name = 'nobody';
}
```

**Bene:**
```php
$name = $_GET['name'] ?? $_POST['name'] ?? 'nobody';
```

**[⬆ torna all'inizio](#table-of-contents)**

## Funzioni

### Utilizzare argomenti predefiniti invece di cortocircuiti o condizionali

**Non male:**

Questo non va bene perché `$breweryName` può essere `NULL`.

```php
function createMicrobrewery($breweryName = 'Hipster Brew Co.'): void
{
    // ...
}
```

**Non male:**

Questa opinione è più comprensibile della versione precedente, ma controlla meglio il valore della variabile.

```php
function createMicrobrewery($name = null): void
{
    $breweryName = $name ?: 'Hipster Brew Co.';
    // ...
}
```

**Bene:**

Puoi usare il [type hinting](http://php.net/manual/en/functions.arguments.php#functions.arguments.type-declaration) ed essere sicuro che `$breweryName` non sarà `NULL`.

```php
function createMicrobrewery(string $breweryName = 'Hipster Brew Co.'): void
{
    // ...
}
```

**[⬆ torna all'inizio](#table-of-contents)**

### Argomenti della funzione (idealmente 2 o meno)

Limitare la quantità di parametri della funzione è incredibilmente importante perché rende più facile testare la vostra funzione. Avere più di tre parametri porta ad un'esplosione combinatoria dove dovete testare tonnellate di casi diversi con ogni argomento separato.

Zero argomenti è il caso ideale. Uno o due argomenti vanno bene, e tre dovrebbero essere evitati. 
Qualsiasi cosa più di questo dovrebbe essere consolidata. Di solito, se avete più di due argomenti allora la vostra funzione sta cercando di fare troppo. Nei casi in cui non lo è, la maggior parte delle volte un oggetto di livello superiore sarà sufficiente come argomento.

**Male:**

```php
class Questionnaire
{
    public function __construct(
        string $firstname,
        string $lastname,
        string $patronymic,
        string $region,
        string $district,
        string $city,
        string $phone,
        string $email
    ) {
        // ...
    }
}
```

**Bene:**

```php
class Name
{
    private $firstname;

    private $lastname;

    private $patronymic;

    public function __construct(string $firstname, string $lastname, string $patronymic)
    {
        $this->firstname = $firstname;
        $this->lastname = $lastname;
        $this->patronymic = $patronymic;
    }

    // getters ...
}

class City
{
    private $region;

    private $district;

    private $city;

    public function __construct(string $region, string $district, string $city)
    {
        $this->region = $region;
        $this->district = $district;
        $this->city = $city;
    }

    // getters ...
}

class Contact
{
    private $phone;

    private $email;

    public function __construct(string $phone, string $email)
    {
        $this->phone = $phone;
        $this->email = $email;
    }

    // getters ...
}

class Questionnaire
{
    public function __construct(Name $name, City $city, Contact $contact)
    {
        // ...
    }
}
```

**[⬆ torna all'inizio](#table-of-contents)**

### I nomi delle funzioni dovrebbero dire cosa fanno

**Male:**

```php
class Email
{
    //...

    public function handle(): void
    {
        mail($this->to, $this->subject, $this->body);
    }
}

$message = new Email(...);
// Che cos'è questo? Un handle per il messaggio? Stiamo scrivendo su un file?
$message->handle();
```

**Bene:**

```php
class Email
{
    //...

    public function send(): void
    {
        mail($this->to, $this->subject, $this->body);
    }
}

$message = new Email(...);
// Chiaro ed evidente
$message->send();
```

**[⬆ torna all'inizio](#table-of-contents)**

### Le funzioni dovrebbero essere solo un livello di astrazione

Quando avete più di un livello di astrazione la vostra funzione solitamente sta facendo troppo. Dividere le funzioni porta alla riusabilità e test più semplici da effettuare.

**Male:**

```php
function parseBetterPHPAlternative(string $code): void
{
    $regexes = [
        // ...
    ];

    $statements = explode(' ', $code);
    $tokens = [];
    foreach ($regexes as $regex) {
        foreach ($statements as $statement) {
            // ...
        }
    }

    $ast = [];
    foreach ($tokens as $token) {
        // lex...
    }

    foreach ($ast as $node) {
        // parse...
    }
}
```

**Anche male:**

Abbiamo realizzato alcune funzionalità, ma la funzione `parseBetterPHPAlternative()` è ancora molto complessa e non testabile.

```php
function tokenize(string $code): array
{
    $regexes = [
        // ...
    ];

    $statements = explode(' ', $code);
    $tokens = [];
    foreach ($regexes as $regex) {
        foreach ($statements as $statement) {
            $tokens[] = /* ... */;
        }
    }

    return $tokens;
}

function lexer(array $tokens): array
{
    $ast = [];
    foreach ($tokens as $token) {
        $ast[] = /* ... */;
    }

    return $ast;
}

function parseBetterPHPAlternative(string $code): void
{
    $tokens = tokenize($code);
    $ast = lexer($tokens);
    foreach ($ast as $node) {
        // parse...
    }
}
```

**Bene:**

La soluzione migliore è spostare le dipendenze della funzione `parseBetterPHPAlternative()`.

```php
class Tokenizer
{
    public function tokenize(string $code): array
    {
        $regexes = [
            // ...
        ];

        $statements = explode(' ', $code);
        $tokens = [];
        foreach ($regexes as $regex) {
            foreach ($statements as $statement) {
                $tokens[] = /* ... */;
            }
        }

        return $tokens;
    }
}

class Lexer
{
    public function lexify(array $tokens): array
    {
        $ast = [];
        foreach ($tokens as $token) {
            $ast[] = /* ... */;
        }

        return $ast;
    }
}

class BetterPHPAlternative
{
    private $tokenizer;
    private $lexer;

    public function __construct(Tokenizer $tokenizer, Lexer $lexer)
    {
        $this->tokenizer = $tokenizer;
        $this->lexer = $lexer;
    }

    public function parse(string $code): void
    {
        $tokens = $this->tokenizer->tokenize($code);
        $ast = $this->lexer->lexify($tokens);
        foreach ($ast as $node) {
            // parse...
        }
    }
}
```

**[⬆ torna all'inizio](#table-of-contents)**

### Non utilizzare flag come parametri di funzione

I flag dicono all'utente che questa funzione fa più di una cosa. Le funzioni dovrebbero
fare una sola cosa. Dividete le vostre funzioni se seguono percorsi di codice diversi
sulla base di un booleano.

**Male:**

```php
function createFile(string $name, bool $temp = false): void
{
    if ($temp) {
        touch('./temp/' . $name);
    } else {
        touch($name);
    }
}
```

**Bene:**

```php
function createFile(string $name): void
{
    touch($name);
}

function createTempFile(string $name): void
{
    touch('./temp/' . $name);
}
```

**[⬆ torna all'inizio](#table-of-contents)**

### Evitare gli effetti collaterali

Una funzione produce un effetto collaterale se non fa altro che prendere un valore e
restituire uno o più valori. Un effetto collaterale potrebbe essere scrivere su un file, modificare qualche variabile globale, o trasferire accidentalmente tutti i vostri soldi ad un estraneo.

Ora, è necessario avere effetti collaterali in un programma delle volte. Come nell'esempio precedente esempio, potreste aver bisogno di scrivere su un file. Quello che volete fare è centralizzare dove state facendo questo. Non avere diverse funzioni e classi che scrivono su un particolare file. Abbiate un solo servizio che lo faccia. Uno e uno solo.

Il punto principale è quello di evitare trabocchetti comuni come la condivisione di stato tra oggetti senza struttura, usare tipi di dati mutabili che possono essere scritti da qualsiasi cosa e non centralizzare dove si verificano gli effetti collaterali. Se riuscite a fare questo, sarete più felici della stragrande maggioranza degli altri programmatori.

**Male:**

```php
// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
$name = 'Ryan McDermott';

function splitIntoFirstAndLastName(): void
{
    global $name;

    $name = explode(' ', $name);
}

splitIntoFirstAndLastName();

var_dump($name);
// ['Ryan', 'McDermott'];
```

**Bene:**

```php
function splitIntoFirstAndLastName(string $name): array
{
    return explode(' ', $name);
}

$name = 'Ryan McDermott';
$newName = splitIntoFirstAndLastName($name);

var_dump($name);
// 'Ryan McDermott';

var_dump($newName);
// ['Ryan', 'McDermott'];
```

**[⬆ torna all'inizio](#table-of-contents)**

### Non scrivere nelle funzioni gloabli

Inquinare i globali è una cattiva pratica in molti linguaggi perché potreste scontrarvi con un'altra libreria e l'utente della vostra API non si accorgerebbe di nulla fino a quando non otterrebbe un'eccezione in produzione. 
Pensiamo ad un esempio: e se voleste avere un array di configurazione?
Potreste scrivere una funzione globale come `config()`, ma potrebbe scontrarsi con un'altra libreria che ha cercato di fare la stessa cosa.

**Male:**

```php
function config(): array
{
    return [
        'foo' => 'bar',
    ];
}
```

**Bene:**

```php
class Configuration
{
    private $configuration = [];

    public function __construct(array $configuration)
    {
        $this->configuration = $configuration;
    }

    public function get(string $key): ?string
    {
        // null coalescing operator
        return $this->configuration[$key] ?? null;
    }
}
```

Carica la configurazione e crea un'istanza della classe `Configuration`.

```php
$configuration = new Configuration([
    'foo' => 'bar',
]);
```

E ora dovete usare un'istanza di `Configuration` nella vostra applicazione.

**[⬆ torna all'inizio](#table-of-contents)**

### Non utilizzare un Singleton pattern

Il Singleton è un [anti-pattern](https://en.wikipedia.org/wiki/Singleton_pattern). Parafrasando Brian Button:
 1. Sono generalmente usati come **istanza globale**. Perché è così cattiva come cosa? Perché **nascondete le dipendenze** della vostra applicazione nel vostro codice, invece di esporle attraverso le interfacce. Rendere qualcosa globale per evitare di passarlo in giro è un [code smell](https://en.wikipedia.org/wiki/Code_smell).
 2. Violano il [single responsibility principle](#single-responsibility-principle-srp): in virtù del fatto che **controllano la propria creazione e il proprio ciclo di vita**.
 3. Essi causano intrinsecamente un codice strettamente [accoppiato] (https://en.wikipedia.org/wiki/Coupling_%28computer_programming%29). Questo rende i **test piuttosto difficili** in molti casi.
 4. Si portano dietro lo stato per tutta la durata dell'applicazione. Un altro colpo ai test poiché **si può finire con una situazione in cui i test devono essere ordinati** che è un gran problema per i test unitari. Perché? Perché ogni test unitario dovrebbe essere indipendente dall'altro.

Ci sono anche ottime riflessioni di [Misko Hevery](http://misko.hevery.com/about/) su [root of problem](http://misko.hevery.com/2008/08/25/root-cause-of-singletons/).

**Male:**

```php
class DBConnection
{
    private static $instance;

    private function __construct(string $dsn)
    {
        // ...
    }

    public static function getInstance(): self
    {
        if (self::$instance === null) {
            self::$instance = new self();
        }

        return self::$instance;
    }

    // ...
}

$singleton = DBConnection::getInstance();
```

**Bene:**

```php
class DBConnection
{
    public function __construct(string $dsn)
    {
        // ...
    }

    // ...
}
```

Crea un'istanza della classe `DBConnection` e configurarla con [DSN](http://php.net/manual/en/pdo.construct.php#refsect1-pdo.construct-parameters).

```php
$connection = new DBConnection($dsn);
```

E ora dovete usare un'istanza di `DBConnection` nella vostra applicazione.

**[⬆ torna all'inizio](#table-of-contents)**

### Incapsulare condizionali

**Male:**

```php
if ($article->state === 'published') {
    // ...
}
```

**Bene:**

```php
if ($article->isPublished()) {
    // ...
}
```

**[⬆ torna all'inizio](#table-of-contents)**

### Evitare condizionali negativi

**Male:**

```php
function isDOMNodeNotPresent(DOMNode $node): bool
{
    // ...
}

if (! isDOMNodeNotPresent($node)) {
    // ...
}
```

**Bene:**

```php
function isDOMNodePresent(DOMNode $node): bool
{
    // ...
}

if (isDOMNodePresent($node)) {
    // ...
}
```

**[⬆ torna all'inizio](#table-of-contents)**

### Evitare conditionali

Questo sembra un compito impossibile. Al primo sentire, la maggior parte delle persone dice,"come posso fare qualcosa senza una dichiarazione `if`? La risposta è che
si può usare il polimorfismo per ottenere lo stesso compito in molti casi. La seconda
domanda è di solito, "beh, è fantastico, ma perché dovrei volerlo fare? La
risposta è un precedente concetto di codice pulito che abbiamo imparato: una funzione dovrebbe fare una sola cosa. Quando avete classi e funzioni che hanno dichiarazioni `if`, state dicendo al vostro utente che la vostra funzione fa più di una cosa. Ricordate, fate solo una cosa.

**Male:**

```php
class Airplane
{
    // ...

    public function getCruisingAltitude(): int
    {
        switch ($this->type) {
            case '777':
                return $this->getMaxAltitude() - $this->getPassengerCount();
            case 'Air Force One':
                return $this->getMaxAltitude();
            case 'Cessna':
                return $this->getMaxAltitude() - $this->getFuelExpenditure();
        }
    }
}
```

**Bene:**

```php
interface Airplane
{
    // ...

    public function getCruisingAltitude(): int;
}

class Boeing777 implements Airplane
{
    // ...

    public function getCruisingAltitude(): int
    {
        return $this->getMaxAltitude() - $this->getPassengerCount();
    }
}

class AirForceOne implements Airplane
{
    // ...

    public function getCruisingAltitude(): int
    {
        return $this->getMaxAltitude();
    }
}

class Cessna implements Airplane
{
    // ...

    public function getCruisingAltitude(): int
    {
        return $this->getMaxAltitude() - $this->getFuelExpenditure();
    }
}
```

**[⬆ torna all'inizio](#table-of-contents)**

### Evitare il controllo dei tipi (parte 1)

PHP non è tipizzato, il che significa che le vostre funzioni possono prendere qualsiasi tipo di argomento. A volte si è morsi da questa libertà e si è tentati di fare
il controllo dei tipi nelle vostre funzioni. Ci sono molti modi per evitare di doverlo fare.
La prima cosa da considerare sono le API coerenti.

**Male:**

```php
function travelToTexas($vehicle): void
{
    if ($vehicle instanceof Bicycle) {
        $vehicle->pedalTo(new Location('texas'));
    } elseif ($vehicle instanceof Car) {
        $vehicle->driveTo(new Location('texas'));
    }
}
```

**Bene:**

```php
function travelToTexas(Vehicle $vehicle): void
{
    $vehicle->travelTo(new Location('texas'));
}
```

**[⬆ torna all'inizio](#table-of-contents)**

### Evitare il controllo dei tipi (parte 2)

Se state lavorando con valori primitivi di base come stringhe, interi e array,
e usate PHP 7+ e non potete usare il polimorfismo ma sentite ancora il bisogno di
controllare il tipo, dovreste considerare
[dichiarazione del tipo](http://php.net/manual/en/functions.arguments.php#functions.arguments.type-declaration) o la modalità strict. 
Essa vi fornisce una tipizzazione statica sopra la sintassi standard di PHP.
Il problema con il controllo manuale dei tipi è che richiede così tanta verbosità extra che la finta "sicurezza dei tipi" che si ottiene non compensa la perdita di leggibilità del codice. Mantenete il vostro PHP pulito, scrivete buoni test e fate buone revisioni del codice.
Altrimenti fate tutto questo ma con PHP strict type declaration o strict mode.

**Male:**

```php
function combine($val1, $val2): int
{
    if (! is_numeric($val1) || ! is_numeric($val2)) {
        throw new Exception('Must be of type Number');
    }

    return $val1 + $val2;
}
```

**Bene:**

```php
function combine(int $val1, int $val2): int
{
    return $val1 + $val2;
}
```

**[⬆ torna all'inizio](#table-of-contents)**

### Rimuovere il codice morto

Il codice morto è brutto tanto quanto il codice duplicato. Non c'è ragione di tenerlo nel
nella vostra codebase. Se non viene chiamato, liberatevene! Sarà ancora al sicuro
nella vostra cronologia delle versioni se ne avete ancora bisogno.

**Male:**

```php
function oldRequestModule(string $url): void
{
    // ...
}

function newRequestModule(string $url): void
{
    // ...
}

$request = newRequestModule($requestUrl);
inventoryTracker('apples', $request, 'www.inventory-awesome.io');
```

**Bene:**

```php
function requestModule(string $url): void
{
    // ...
}

$request = requestModule($requestUrl);
inventoryTracker('apples', $request, 'www.inventory-awesome.io');
```

**[⬆ torna all'inizio](#table-of-contents)**


## Oggetti e strutture dati

### Utilizzare l'incapsulamente degli oggetti

In PHP potete impostare le parole chiave `public`, `protected` e `private` per i metodi.
Usandole, potete controllare la modifica delle proprietà di un oggetto.

* Quando si vuole fare di più che ottenere una proprietà di un oggetto, non si deve
cercare e cambiare ogni accessor nel tuo codice.
* Rende semplice l'aggiunta della validazione quando si fa un `set`.
* Incapsula la rappresentazione interna.
* Semplice da aggiungere il logging e gestione errori quando si fa un get o un set.
* Ereditando questa classe, puoi sovrascrivere le funzionalità predefinite.
* Puoi caricare in modo lazy le proprietà del tuo oggetto, per esempio prendendolo da un server.

Inoltre, questo fa parte del principio [Open/Closed](#openclosed-principle-ocp).

**Male:**

```php
class BankAccount
{
    public $balance = 1000;
}

$bankAccount = new BankAccount();

// Buy shoes...
$bankAccount->balance -= 100;
```

**Bene:**

```php
class BankAccount
{
    private $balance;

    public function __construct(int $balance = 1000)
    {
      $this->balance = $balance;
    }

    public function withdraw(int $amount): void
    {
        if ($amount > $this->balance) {
            throw new \Exception('Amount greater than available balance.');
        }

        $this->balance -= $amount;
    }

    public function deposit(int $amount): void
    {
        $this->balance += $amount;
    }

    public function getBalance(): int
    {
        return $this->balance;
    }
}

$bankAccount = new BankAccount();

// Buy shoes...
$bankAccount->withdraw($shoesPrice);

// Get balance
$balance = $bankAccount->getBalance();
```

**[⬆ torna all'inizio](#table-of-contents)**

### Fare in modo che gli oggetti abbiano membri privati/protetti

* `public` methods and properties are most dangerous for changes, because some outside code may easily rely on them and you can't control what code relies on them. **Modifications in class are dangerous for all users of class.**
* `protected` modifier are as dangerous as public, because they are available in scope of any child class. This effectively means that difference between public and protected is only in access mechanism, but encapsulation guarantee remains the same. **Modifications in class are dangerous for all descendant classes.**
* `private` modifier guarantees that code is **dangerous to modify only in boundaries of single class** (you are safe for modifications and you won't have [Jenga effect](http://www.urbandictionary.com/define.php?term=Jengaphobia&defid=2494196)).

Therefore, use `private` by default and `public/protected` when you need to provide access for external classes.

For more information you can read the [blog post](http://fabien.potencier.org/pragmatism-over-theory-protected-vs-private.html) on this topic written by [Fabien Potencier](https://github.com/fabpot).

**Bad:**

```php
class Employee
{
    public $name;

    public function __construct(string $name)
    {
        $this->name = $name;
    }
}

$employee = new Employee('John Doe');
// Employee name: John Doe
echo 'Employee name: ' . $employee->name;
```

**Good:**

```php
class Employee
{
    private $name;

    public function __construct(string $name)
    {
        $this->name = $name;
    }

    public function getName(): string
    {
        return $this->name;
    }
}

$employee = new Employee('John Doe');
// Employee name: John Doe
echo 'Employee name: ' . $employee->getName();
```

**[⬆ torna all'inizio](#table-of-contents)**

## Classes

### Prefer composition over inheritance

As stated famously in [*Design Patterns*](https://en.wikipedia.org/wiki/Design_Patterns) by the Gang of Four,
you should prefer composition over inheritance where you can. There are lots of
good reasons to use inheritance and lots of good reasons to use composition.
The main point for this maxim is that if your mind instinctively goes for
inheritance, try to think if composition could model your problem better. In some
cases it can.

You might be wondering then, "when should I use inheritance?" It
depends on your problem at hand, but this is a decent list of when inheritance
makes more sense than composition:

1. Your inheritance represents an "is-a" relationship and not a "has-a"
relationship (Human->Animal vs. User->UserDetails).
2. You can reuse code from the base classes (Humans can move like all animals).
3. You want to make global changes to derived classes by changing a base class.
(Change the caloric expenditure of all animals when they move).

**Bad:**

```php
class Employee
{
    private $name;

    private $email;

    public function __construct(string $name, string $email)
    {
        $this->name = $name;
        $this->email = $email;
    }

    // ...
}

// Bad because Employees "have" tax data.
// EmployeeTaxData is not a type of Employee

class EmployeeTaxData extends Employee
{
    private $ssn;

    private $salary;

    public function __construct(string $name, string $email, string $ssn, string $salary)
    {
        parent::__construct($name, $email);

        $this->ssn = $ssn;
        $this->salary = $salary;
    }

    // ...
}
```

**Good:**

```php
class EmployeeTaxData
{
    private $ssn;

    private $salary;

    public function __construct(string $ssn, string $salary)
    {
        $this->ssn = $ssn;
        $this->salary = $salary;
    }

    // ...
}

class Employee
{
    private $name;

    private $email;

    private $taxData;

    public function __construct(string $name, string $email)
    {
        $this->name = $name;
        $this->email = $email;
    }

    public function setTaxData(EmployeeTaxData $taxData): void
    {
        $this->taxData = $taxData;
    }

    // ...
}
```

**[⬆ torna all'inizio](#table-of-contents)**

### Avoid fluent interfaces

A [Fluent interface](https://en.wikipedia.org/wiki/Fluent_interface) is an object
oriented API that aims to improve the readability of the source code by using
[Method chaining](https://en.wikipedia.org/wiki/Method_chaining).

While there can be some contexts, frequently builder objects, where this
pattern reduces the verbosity of the code (for example the [PHPUnit Mock Builder](https://phpunit.de/manual/current/en/test-doubles.html)
or the [Doctrine Query Builder](http://docs.doctrine-project.org/projects/doctrine-dbal/en/latest/reference/query-builder.html)),
more often it comes at some costs:

1. Breaks [Encapsulation](https://en.wikipedia.org/wiki/Encapsulation_%28object-oriented_programming%29).
2. Breaks [Decorators](https://en.wikipedia.org/wiki/Decorator_pattern).
3. Is harder to [mock](https://en.wikipedia.org/wiki/Mock_object) in a test suite.
4. Makes diffs of commits harder to read.

For more information you can read the full [blog post](https://ocramius.github.io/blog/fluent-interfaces-are-evil/)
on this topic written by [Marco Pivetta](https://github.com/Ocramius).

**Bad:**

```php
class Car
{
    private $make = 'Honda';

    private $model = 'Accord';

    private $color = 'white';

    public function setMake(string $make): self
    {
        $this->make = $make;

        // NOTE: Returning this for chaining
        return $this;
    }

    public function setModel(string $model): self
    {
        $this->model = $model;

        // NOTE: Returning this for chaining
        return $this;
    }

    public function setColor(string $color): self
    {
        $this->color = $color;

        // NOTE: Returning this for chaining
        return $this;
    }

    public function dump(): void
    {
        var_dump($this->make, $this->model, $this->color);
    }
}

$car = (new Car())
    ->setColor('pink')
    ->setMake('Ford')
    ->setModel('F-150')
    ->dump();
```

**Good:**

```php
class Car
{
    private $make = 'Honda';

    private $model = 'Accord';

    private $color = 'white';

    public function setMake(string $make): void
    {
        $this->make = $make;
    }

    public function setModel(string $model): void
    {
        $this->model = $model;
    }

    public function setColor(string $color): void
    {
        $this->color = $color;
    }

    public function dump(): void
    {
        var_dump($this->make, $this->model, $this->color);
    }
}

$car = new Car();
$car->setColor('pink');
$car->setMake('Ford');
$car->setModel('F-150');
$car->dump();
```

**[⬆ torna all'inizio](#table-of-contents)**

### Prefer final classes

The `final` keyword should be used whenever possible:

1. It prevents an uncontrolled inheritance chain.
2. It encourages [composition](#prefer-composition-over-inheritance).
3. It encourages the [Single Responsibility Pattern](#single-responsibility-principle-srp).
4. It encourages developers to use your public methods instead of extending the class to get access to protected ones.
5. It allows you to change your code without breaking applications that use your class.

The only condition is that your class should implement an interface and no other public methods are defined.

For more informations you can read [the blog post](https://ocramius.github.io/blog/when-to-declare-classes-final/) on this topic written by [Marco Pivetta (Ocramius)](https://ocramius.github.io/).

**Bad:**

```php
final class Car
{
    private $color;

    public function __construct($color)
    {
        $this->color = $color;
    }

    /**
     * @return string The color of the vehicle
     */
    public function getColor()
    {
        return $this->color;
    }
}
```

**Good:**

```php
interface Vehicle
{
    /**
     * @return string The color of the vehicle
     */
    public function getColor();
}

final class Car implements Vehicle
{
    private $color;

    public function __construct($color)
    {
        $this->color = $color;
    }

    public function getColor()
    {
        return $this->color;
    }
}
```

**[⬆ torna all'inizio](#table-of-contents)**

## SOLID

**SOLID** is the mnemonic acronym introduced by Michael Feathers for the first five principles named by Robert Martin, which meant five basic principles of object-oriented programming and design.

 * [S: Single Responsibility Principle (SRP)](#single-responsibility-principle-srp)
 * [O: Open/Closed Principle (OCP)](#openclosed-principle-ocp)
 * [L: Liskov Substitution Principle (LSP)](#liskov-substitution-principle-lsp)
 * [I: Interface Segregation Principle (ISP)](#interface-segregation-principle-isp)
 * [D: Dependency Inversion Principle (DIP)](#dependency-inversion-principle-dip)

### Single Responsibility Principle (SRP)

As stated in Clean Code, "There should never be more than one reason for a class
to change". It's tempting to jam-pack a class with a lot of functionality, like
when you can only take one suitcase on your flight. The issue with this is
that your class won't be conceptually cohesive and it will give it many reasons
to change. Minimizing the amount of times you need to change a class is important.
It's important because if too much functionality is in one class and you modify a piece of it,
it can be difficult to understand how that will affect other dependent modules in
your codebase.

**Bad:**

```php
class UserSettings
{
    private $user;

    public function __construct(User $user)
    {
        $this->user = $user;
    }

    public function changeSettings(array $settings): void
    {
        if ($this->verifyCredentials()) {
            // ...
        }
    }

    private function verifyCredentials(): bool
    {
        // ...
    }
}
```

**Good:**

```php
class UserAuth
{
    private $user;

    public function __construct(User $user)
    {
        $this->user = $user;
    }

    public function verifyCredentials(): bool
    {
        // ...
    }
}

class UserSettings
{
    private $user;

    private $auth;

    public function __construct(User $user)
    {
        $this->user = $user;
        $this->auth = new UserAuth($user);
    }

    public function changeSettings(array $settings): void
    {
        if ($this->auth->verifyCredentials()) {
            // ...
        }
    }
}
```

**[⬆ torna all'inizio](#table-of-contents)**

### Open/Closed Principle (OCP)

As stated by Bertrand Meyer, "software entities (classes, modules, functions,
etc.) should be open for extension, but closed for modification." What does that
mean though? This principle basically states that you should allow users to
add new functionalities without changing existing code.

**Bad:**

```php
abstract class Adapter
{
    protected $name;

    public function getName(): string
    {
        return $this->name;
    }
}

class AjaxAdapter extends Adapter
{
    public function __construct()
    {
        parent::__construct();

        $this->name = 'ajaxAdapter';
    }
}

class NodeAdapter extends Adapter
{
    public function __construct()
    {
        parent::__construct();

        $this->name = 'nodeAdapter';
    }
}

class HttpRequester
{
    private $adapter;

    public function __construct(Adapter $adapter)
    {
        $this->adapter = $adapter;
    }

    public function fetch(string $url): Promise
    {
        $adapterName = $this->adapter->getName();

        if ($adapterName === 'ajaxAdapter') {
            return $this->makeAjaxCall($url);
        } elseif ($adapterName === 'httpNodeAdapter') {
            return $this->makeHttpCall($url);
        }
    }

    private function makeAjaxCall(string $url): Promise
    {
        // request and return promise
    }

    private function makeHttpCall(string $url): Promise
    {
        // request and return promise
    }
}
```

**Good:**

```php
interface Adapter
{
    public function request(string $url): Promise;
}

class AjaxAdapter implements Adapter
{
    public function request(string $url): Promise
    {
        // request and return promise
    }
}

class NodeAdapter implements Adapter
{
    public function request(string $url): Promise
    {
        // request and return promise
    }
}

class HttpRequester
{
    private $adapter;

    public function __construct(Adapter $adapter)
    {
        $this->adapter = $adapter;
    }

    public function fetch(string $url): Promise
    {
        return $this->adapter->request($url);
    }
}
```

**[⬆ torna all'inizio](#table-of-contents)**

### Liskov Substitution Principle (LSP)

This is a scary term for a very simple concept. It's formally defined as "If S
is a subtype of T, then objects of type T may be replaced with objects of type S
(i.e., objects of type S may substitute objects of type T) without altering any
of the desirable properties of that program (correctness, task performed,
etc.)." That's an even scarier definition.

The best explanation for this is if you have a parent class and a child class,
then the base class and child class can be used interchangeably without getting
incorrect results. This might still be confusing, so let's take a look at the
classic Square-Rectangle example. Mathematically, a square is a rectangle, but
if you model it using the "is-a" relationship via inheritance, you quickly
get into trouble.

**Bad:**

```php
class Rectangle
{
    protected $width = 0;

    protected $height = 0;

    public function setWidth(int $width): void
    {
        $this->width = $width;
    }

    public function setHeight(int $height): void
    {
        $this->height = $height;
    }

    public function getArea(): int
    {
        return $this->width * $this->height;
    }
}

class Square extends Rectangle
{
    public function setWidth(int $width): void
    {
        $this->width = $this->height = $width;
    }

    public function setHeight(int $height): void
    {
        $this->width = $this->height = $height;
    }
}

function printArea(Rectangle $rectangle): void
{
    $rectangle->setWidth(4);
    $rectangle->setHeight(5);

    // BAD: Will return 25 for Square. Should be 20.
    echo sprintf('%s has area %d.', get_class($rectangle), $rectangle->getArea()) . PHP_EOL;
}

$rectangles = [new Rectangle(), new Square()];

foreach ($rectangles as $rectangle) {
    printArea($rectangle);
}
```

**Good:**

The best way is separate the quadrangles and allocation of a more general subtype for both shapes.

Despite the apparent similarity of the square and the rectangle, they are different.
A square has much in common with a rhombus, and a rectangle with a parallelogram, but they are not subtypes.
A square, a rectangle, a rhombus and a parallelogram are separate shapes with their own properties, albeit similar.

```php
interface Shape
{
    public function getArea(): int;
}

class Rectangle implements Shape
{
    private $width = 0;
    private $height = 0;

    public function __construct(int $width, int $height)
    {
        $this->width = $width;
        $this->height = $height;
    }

    public function getArea(): int
    {
        return $this->width * $this->height;
    }
}

class Square implements Shape
{
    private $length = 0;

    public function __construct(int $length)
    {
        $this->length = $length;
    }

    public function getArea(): int
    {
        return $this->length ** 2;
    }
}

function printArea(Shape $shape): void
{
    echo sprintf('%s has area %d.', get_class($shape), $shape->getArea()).PHP_EOL;
}

$shapes = [new Rectangle(4, 5), new Square(5)];

foreach ($shapes as $shape) {
    printArea($shape);
}
```

**[⬆ torna all'inizio](#table-of-contents)**

### Interface Segregation Principle (ISP)

ISP states that "Clients should not be forced to depend upon interfaces that
they do not use."

A good example to look at that demonstrates this principle is for
classes that require large settings objects. Not requiring clients to set up
huge amounts of options is beneficial, because most of the time they won't need
all of the settings. Making them optional helps prevent having a "fat interface".

**Bad:**

```php
interface Employee
{
    public function work(): void;

    public function eat(): void;
}

class HumanEmployee implements Employee
{
    public function work(): void
    {
        // ....working
    }

    public function eat(): void
    {
        // ...... eating in lunch break
    }
}

class RobotEmployee implements Employee
{
    public function work(): void
    {
        //.... working much more
    }

    public function eat(): void
    {
        //.... robot can't eat, but it must implement this method
    }
}
```

**Good:**

Not every worker is an employee, but every employee is a worker.

```php
interface Workable
{
    public function work(): void;
}

interface Feedable
{
    public function eat(): void;
}

interface Employee extends Feedable, Workable
{
}

class HumanEmployee implements Employee
{
    public function work(): void
    {
        // ....working
    }

    public function eat(): void
    {
        //.... eating in lunch break
    }
}

// robot can only work
class RobotEmployee implements Workable
{
    public function work(): void
    {
        // ....working
    }
}
```

**[⬆ torna all'inizio](#table-of-contents)**

### Dependency Inversion Principle (DIP)

This principle states two essential things:
1. High-level modules should not depend on low-level modules. Both should
depend on abstractions.
2. Abstractions should not depend upon details. Details should depend on
abstractions.

This can be hard to understand at first, but if you've worked with PHP frameworks (like Symfony), you've seen an implementation of this principle in the form of Dependency
Injection (DI). While they are not identical concepts, DIP keeps high-level
modules from knowing the details of its low-level modules and setting them up.
It can accomplish this through DI. A huge benefit of this is that it reduces
the coupling between modules. Coupling is a very bad development pattern because
it makes your code hard to refactor.

**Bad:**

```php
class Employee
{
    public function work(): void
    {
        // ....working
    }
}

class Robot extends Employee
{
    public function work(): void
    {
        //.... working much more
    }
}

class Manager
{
    private $employee;

    public function __construct(Employee $employee)
    {
        $this->employee = $employee;
    }

    public function manage(): void
    {
        $this->employee->work();
    }
}
```

**Good:**

```php
interface Employee
{
    public function work(): void;
}

class Human implements Employee
{
    public function work(): void
    {
        // ....working
    }
}

class Robot implements Employee
{
    public function work(): void
    {
        //.... working much more
    }
}

class Manager
{
    private $employee;

    public function __construct(Employee $employee)
    {
        $this->employee = $employee;
    }

    public function manage(): void
    {
        $this->employee->work();
    }
}
```

**[⬆ torna all'inizio](#table-of-contents)**

## Don’t repeat yourself (DRY)

Try to observe the [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) principle.

Do your absolute best to avoid duplicate code. Duplicate code is bad because
it means that there's more than one place to alter something if you need to
change some logic.

Imagine if you run a restaurant and you keep track of your inventory: all your
tomatoes, onions, garlic, spices, etc. If you have multiple lists that
you keep this on, then all have to be updated when you serve a dish with
tomatoes in them. If you only have one list, there's only one place to update!

Often you have duplicate code because you have two or more slightly
different things, that share a lot in common, but their differences force you
to have two or more separate functions that do much of the same things. Removing
duplicate code means creating an abstraction that can handle this set of different
things with just one function/module/class.

Getting the abstraction right is critical, that's why you should follow the
SOLID principles laid out in the [Classes](#classes) section. Bad abstractions can be
worse than duplicate code, so be careful! Having said this, if you can make
a good abstraction, do it! Don't repeat yourself, otherwise you'll find yourself
updating multiple places any time you want to change one thing.

**Bad:**

```php
function showDeveloperList(array $developers): void
{
    foreach ($developers as $developer) {
        $expectedSalary = $developer->calculateExpectedSalary();
        $experience = $developer->getExperience();
        $githubLink = $developer->getGithubLink();
        $data = [$expectedSalary, $experience, $githubLink];

        render($data);
    }
}

function showManagerList(array $managers): void
{
    foreach ($managers as $manager) {
        $expectedSalary = $manager->calculateExpectedSalary();
        $experience = $manager->getExperience();
        $githubLink = $manager->getGithubLink();
        $data = [$expectedSalary, $experience, $githubLink];

        render($data);
    }
}
```

**Good:**

```php
function showList(array $employees): void
{
    foreach ($employees as $employee) {
        $expectedSalary = $employee->calculateExpectedSalary();
        $experience = $employee->getExperience();
        $githubLink = $employee->getGithubLink();
        $data = [$expectedSalary, $experience, $githubLink];

        render($data);
    }
}
```

**Very good:**

It is better to use a compact version of the code.

```php
function showList(array $employees): void
{
    foreach ($employees as $employee) {
        render([$employee->calculateExpectedSalary(), $employee->getExperience(), $employee->getGithubLink()]);
    }
}
```

**[⬆ torna all'inizio](#table-of-contents)**

## Translations

This is also available in other languages:

* :cn: **Chinese:**
   * [php-cpm/clean-code-php](https://github.com/php-cpm/clean-code-php)
* :ru: **Russian:**
   * [peter-gribanov/clean-code-php](https://github.com/peter-gribanov/clean-code-php)
* :es: **Spanish:**
   * [fikoborquez/clean-code-php](https://github.com/fikoborquez/clean-code-php)
* :brazil: **Portuguese:**
   * [fabioars/clean-code-php](https://github.com/fabioars/clean-code-php)
   * [jeanjar/clean-code-php](https://github.com/jeanjar/clean-code-php/tree/pt-br)
* :thailand: **Thai:**
   * [panuwizzle/clean-code-php](https://github.com/panuwizzle/clean-code-php)
* :fr: **French:**
   * [errorname/clean-code-php](https://github.com/errorname/clean-code-php)
* :vietnam: **Vietnamese**
   * [viethuongdev/clean-code-php](https://github.com/viethuongdev/clean-code-php)
* :kr: **Korean:**
   * [yujineeee/clean-code-php](https://github.com/yujineeee/clean-code-php)
* :tr: **Turkish:**
   * [anilozmen/clean-code-php](https://github.com/anilozmen/clean-code-php)
* :iran: **Persian:**
     * [amirshnll/clean-code-php](https://github.com/amirshnll/clean-code-php)

**[⬆ torna all'inizio](#table-of-contents)**
