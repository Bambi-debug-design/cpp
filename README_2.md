# Příručka k závěrečné zkoušce z C++

**Objektově orientované programování — polymorfismus, přetěžování operátorů, algoritmizace**

Tato příručka je stavěná na typ zkouškové úlohy, kde se vytváří abstraktní bázová třída, dvě odvozené třídy, přetěžují se operátory a implementují dva algoritmy nad vektorem. Veškerý kód v příručce byl přeložen `g++ -std=c++17 -Wall -Wextra` bez jediného varování a spuštěn.

---

## Obsah

**Část I — Praktický postup**
1. [Strategie: kde jsou body](#1-strategie-kde-jsou-body)
2. [Postup krok za krokem](#2-postup-krok-za-krokem)
3. [Rozdělení do souborů](#3-rozdělení-do-souborů)

**Část II — Kompletní referenční kostra**
4. [Bázová abstraktní třída](#4-bázová-abstraktní-třída)
5. [Odvozená třída (jednoduchá)](#5-odvozená-třída-jednoduchá)
6. [Odvozená třída s operátory](#6-odvozená-třída-s-operátory)
7. [Funkce main](#7-funkce-main)

**Část III — Klíčové koncepty**
8. [Konstruktory a inicializační seznam](#8-konstruktory-a-inicializační-seznam)
9. [Statický čítač instancí](#9-statický-čítač-instancí)
10. [Virtuální metody a polymorfismus](#10-virtuální-metody-a-polymorfismus)
11. [Abstraktní třída a `= 0`](#11-abstraktní-třída-a--0)
12. [Přetěžování operátorů](#12-přetěžování-operátorů)
13. [Reference vs. kopie](#13-reference-vs-kopie)
14. [Správa paměti](#14-správa-paměti)

**Část IV — Algoritmy**
15. [Vzor: nejdelší souvislá série](#15-vzor-nejdelší-souvislá-série)
16. [Vzor: podmíněné mazání z vektoru](#16-vzor-podmíněné-mazání-z-vektoru)
17. [Vzor: agregace s filtrem](#17-vzor-agregace-s-filtrem)

**Část V — Ladění a kontrola**
18. [Syntaktické chyby, které dělá každý](#18-syntaktické-chyby-které-dělá-každý)
19. [Chybové hlášky a jejich příčiny](#19-chybové-hlášky-a-jejich-příčiny)
20. [Otázky k ústní části](#20-otázky-k-ústní-části)
21. [Kontrolní seznam před odevzdáním](#21-kontrolní-seznam-před-odevzdáním)

---

# ČÁST I — PRAKTICKÝ POSTUP

## 1. Strategie: kde jsou body

U tohoto typu zkoušky je bodování obvykle rozložené takto:

| Oblast | Body | Charakter |
|---|---|---|
| Základní OOP + rozdělení souborů | 15 | šablonová práce |
| Dědičnost a polymorfismus | 15 | šablonová práce |
| Přetěžování operátorů | 15 | šablonová práce |
| Algoritmus 1 | 20 | vyžaduje myšlení |
| Algoritmus 2 | 20 | vyžaduje myšlení |
| Polymorfní průchod v `main` | 10 | šablonová práce |
| Správa paměti | 5 | šablonová práce |

**Zásadní pozorování:** 60 bodů (základ + dědičnost + operátory + průchod + paměť) je *šablonová* práce — struktura se u tohoto typu zadání opakuje téměř nezměněná. Kdo si nastuduje kostru z části II, dostane tyto body i bez vyřešení jediného algoritmu.

**Priorita při psaní:** nejdřív zprovoznit kostru (třídy, dědičnost, operátory, `main` s průchodem a úklidem), teprve pak řešit algoritmy. Nikdy naopak — rozpracovaný algoritmus v nekompilovatelném projektu nemá cenu.

**Sankce:** za nepřeložitelný kód se obvykle strhává 20 bodů, za pád programu 10 bodů. Program, který se přeloží a nespadne, ale nemá algoritmy, je vždy lepší než opačný případ.

---

## 2. Postup krok za krokem

### Minuta 0–3: příprava

1. Přečti zadání, spočítej třídy. Tři třídy = 7 souborů (3× `.h`, 3× `.cpp`, `main.cpp`).
2. Založ všech 7 prázdných souborů.
3. Do každého `.h` napiš include guard, do každého `.cpp` `#include` své hlavičky.
4. Do `main.cpp` napiš prázdný `main` s jedním výpisem a **přelož to**. Ověříš, že prostředí funguje.

### Minuta 3–40: bázová třída

Napiš celý `.h`, pak celý `.cpp`. **Přelož** (`g++ -c Baze.cpp` — přepínač `-c` přeloží bez potřeby `main`).

### Minuta 40–70: odvozené třídy

Nejdřív ta jednodušší (bez operátorů), **přelož**. Pak ta s operátory, **přelož**.

### Minuta 70–110: main

Nejdřív polymorfní průchod a úklid paměti (jistých 15 bodů), **přelož a spusť**. Pak algoritmy, jeden po druhém, po každém **přelož a spusť**.

### Minuta 110–120: kontrola

Projdi kontrolní seznam z části 21. Ověř, že čítač končí na nule.

### Základní pravidlo

**Překládej po každé dokončené části.** Když program přestane jít přeložit, víš přesně, který kousek to způsobil. Kdo napíše 300 řádků a pak se poprvé pokusí přeložit, hledá chybu v celém projektu.

```bash
g++ -std=c++17 -Wall -Wextra -o program *.cpp   # celý projekt
g++ -std=c++17 -Wall -Wextra -c Trida.cpp       # jen jeden soubor (bez main)
```

---

## 3. Rozdělení do souborů

Každá třída má dvojici souborů. Princip: `.h` je **seznam toho, co třída umí**, `.cpp` je **jak to dělá**.

| | `.h` (hlavička) | `.cpp` (implementace) |
|---|---|---|
| obsahuje | deklarace | definice (těla) |
| metoda končí | středníkem `;` | tělem `{ }` |
| před názvem metody | nic | `NazevTridy::` |
| `static` u metody | **ano** | **ne** |
| `virtual` | **ano** | **ne** |
| `override` | **ano** | **ne** |
| inicializační seznam `:` | **ne** | **ano** |
| `using namespace std;` | nedávat | lze dát |

Tentýž konstruktor ve dvou souborech:

```cpp
// v .h — pouze deklarace
Trida(const std::string& n, double p);

// v .cpp — definice s inicializačním seznamem
Trida::Trida(const string& n, double p) : Baze(n), parametr(p) {
}
```

**Struktura vkládání souborů:**

```
Baze.h          ← vkládá <string>, <vector>
Baze.cpp        ← vkládá "Baze.h"
TypA.h          ← vkládá "Baze.h"
TypA.cpp        ← vkládá "TypA.h"
TypB.h          ← vkládá "Baze.h", <iostream>
TypB.cpp        ← vkládá "TypB.h"
main.cpp        ← vkládá všechny tři hlavičky
```

Soubor `.cpp` se nikdy nevkládá do jiného souboru — vkládají se pouze hlavičky.

---

# ČÁST II — KOMPLETNÍ REFERENČNÍ KOSTRA

Následuje úplný, přeložený a spuštěný projekt. Názvy jsou obecné (`Zaznam`, `TypA`, `TypB`) — u zkoušky je přejmenuj podle zadání, struktura zůstane stejná.

## 4. Bázová abstraktní třída

### Zaznam.h

```cpp
#ifndef ZAZNAM_H
#define ZAZNAM_H

#include <string>
#include <vector>

class Zaznam {
protected:                                  // protected: vidí potomci
    std::string nazev;
    std::vector<double> historie;
    static int pocetObjektu;                // statický čítač

public:
    Zaznam(const std::string& n);
    virtual ~Zaznam();                      // VIRTUÁLNÍ destruktor

    void pridejPolozku(double hodnota);
    void pridejPolozky(const std::vector<double>& hodnoty);

    std::vector<double>& getHistorie();     // reference → lze měnit originál
    size_t pocetPolozek() const;            // const verze pro operátor <<
    std::string getNazev() const;           // getter pro operátor <<

    static int getPocetObjektu();           // statická metoda

    virtual void analyzuj() const = 0;      // čistě virtuální → třída je abstraktní
    virtual void vypisInfo() const;         // virtuální, potomci rozšíří
};

#endif
```

### Zaznam.cpp

```cpp
#include "Zaznam.h"
#include <iostream>
using namespace std;

int Zaznam::pocetObjektu = 0;               // DEFINICE čítače — bez ní linker error

Zaznam::Zaznam(const string& n) : nazev(n) {
    pocetObjektu++;                         // vznik objektu
}

Zaznam::~Zaznam() {
    pocetObjektu--;                         // zánik objektu
}

void Zaznam::pridejPolozku(double hodnota) {
    historie.push_back(hodnota);
}

void Zaznam::pridejPolozky(const vector<double>& hodnoty) {
    for (size_t i = 0; i < hodnoty.size(); i++)
        historie.push_back(hodnoty[i]);
}

vector<double>& Zaznam::getHistorie() {
    return historie;
}

size_t Zaznam::pocetPolozek() const {
    return historie.size();
}

string Zaznam::getNazev() const {
    return nazev;
}

int Zaznam::getPocetObjektu() {             // zde už BEZ static
    return pocetObjektu;
}

void Zaznam::vypisInfo() const {
    cout << "Zaznam: " << nazev
         << " | Pocet polozek: " << historie.size();
    // záměrně BEZ endl — potomek naváže svou část
}
```

**Metoda `analyzuj()` zde není definována** — má `= 0`, takže ji definují až potomci.

---

## 5. Odvozená třída (jednoduchá)

### TypA.h

```cpp
#ifndef TYPA_H
#define TYPA_H

#include "Zaznam.h"

class TypA : public Zaznam {                // : public Zaznam = dědičnost
private:
    double parametr;                        // vlastní atribut

public:
    TypA(const std::string& n, double p);
    void vypisInfo() const override;
    void analyzuj() const override;          // MUSÍ být definována
};

#endif
```

### TypA.cpp

```cpp
#include "TypA.h"
#include <iostream>
using namespace std;

TypA::TypA(const string& n, double p)
    : Zaznam(n), parametr(p) {              // Zaznam(n) = konstruktor předka
}

void TypA::vypisInfo() const {
    Zaznam::vypisInfo();                    // část předka
    cout << " | Typ: A | Parametr: " << parametr << endl;   // vlastní část + endl
}

void TypA::analyzuj() const {
    double soucet = 0;                      // NUTNĚ inicializovat na 0
    int pocet = 0;
    for (size_t i = 0; i < historie.size(); i++) {
        if (historie[i] > 0) {              // filtr: kladné hodnoty
            soucet += historie[i];
            pocet++;
        }
    }
    double prumer = (pocet > 0) ? (soucet / pocet) : 0;   // ochrana proti dělení nulou
    cout << "Analyza (" << nazev << "): Prumer kladnych je " << prumer << endl;
}
```

---

## 6. Odvozená třída s operátory

### TypB.h

```cpp
#ifndef TYPB_H
#define TYPB_H

#include "Zaznam.h"
#include <iostream>                         // kvůli std::ostream

class TypB : public Zaznam {
private:
    double koeficient;

public:
    TypB(const std::string& n, double k);
    void vypisInfo() const override;
    void analyzuj() const override;

    double getKoeficient() const;           // getter

    bool operator==(const TypB& druhy) const;   // metoda (vlevo je náš objekt)
    TypB& operator+=(double hodnota);            // metoda (vlevo je náš objekt)
};

// operátor << je VOLNÁ FUNKCE — mimo třídu, za závorkou };
std::ostream& operator<<(std::ostream& os, const TypB& obj);

#endif
```

### TypB.cpp

```cpp
#include "TypB.h"
using namespace std;

TypB::TypB(const string& n, double k) : Zaznam(n), koeficient(k) {
}

void TypB::vypisInfo() const {
    Zaznam::vypisInfo();
    cout << " | Typ: B | Koeficient: " << koeficient << endl;
}

void TypB::analyzuj() const {
    int pocetZapornych = 0;
    for (size_t i = 0; i < historie.size(); i++)
        if (historie[i] < 0) pocetZapornych++;
    cout << "Analyza (" << nazev << "): Zapornych polozek: "
         << pocetZapornych << endl;
}

double TypB::getKoeficient() const {
    return koeficient;
}

// == : porovná atribut, vrátí bool
bool TypB::operator==(const TypB& druhy) const {
    return koeficient == druhy.koeficient;
}

// += : změní objekt, vrátí sám sebe
TypB& TypB::operator+=(double hodnota) {
    pridejPolozku(hodnota);                 // zděděná metoda z předka
    return *this;
}

// << : volná funkce, data čte přes gettery
ostream& operator<<(ostream& os, const TypB& obj) {
    os << "TypB[" << obj.getNazev() << "], polozek: " << obj.pocetPolozek();
    return os;                              // vrácení proudu → řetězení
}
```

---

## 7. Funkce main

```cpp
#include <iostream>
#include <vector>
#include "Zaznam.h"
#include "TypA.h"
#include "TypB.h"
using namespace std;

// ---------- ALGORITMUS 1: nejdelší souvislá série kladných ----------
int nejdelsiSerie(Zaznam* z) {
    int nejdelsi = 0, aktualni = 0;
    vector<double>& h = z->getHistorie();
    for (size_t i = 0; i < h.size(); i++) {
        if (h[i] > 0) {
            aktualni++;
            if (aktualni > nejdelsi) nejdelsi = aktualni;
        } else {
            aktualni = 0;                   // porušení podmínky → reset
        }
    }
    return nejdelsi;
}

// ---------- ALGORITMUS 2: odstranění hodnot v intervalu ----------
void odstranMale(Zaznam* z) {
    vector<double>& h = z->getHistorie();   // REFERENCE → měníme originál
    vector<double> vysledek;                // nový vektor jen z těch, co zůstanou
    for (size_t i = 0; i < h.size(); i++) {
        bool smazat = (h[i] < 0 && h[i] > -50);   // pozor na ostré hranice
        if (!smazat) vysledek.push_back(h[i]);
    }
    h = vysledek;                            // přepis originálu
}

int main() {
    // 1) počáteční stav čítače
    cout << "Pocatecni pocet: " << Zaznam::getPocetObjektu() << endl;

    // 2) vektor ukazatelů na bázi + alokace přes new
    vector<Zaznam*> objekty;
    objekty.push_back(new TypB("B-1", 50));
    objekty.push_back(new TypB("B-2", 0));
    objekty.push_back(new TypA("A-1", 4.5));

    objekty[0]->pridejPolozky({100, 200, 300, -20, 500, 600, -1000, -45});
    objekty[1]->pridejPolozky({100, -50, 200, -30, 300});
    objekty[2]->pridejPolozky({1000, 2000, 7000, -100});

    cout << "Po alokaci: " << Zaznam::getPocetObjektu() << endl << endl;

    // 3) polymorfní průchod
    for (size_t i = 0; i < objekty.size(); i++) {
        objekty[i]->vypisInfo();
        objekty[i]->analyzuj();
    }
    cout << endl;

    // 4) algoritmy
    cout << "Nejdelsi serie: " << nejdelsiSerie(objekty[0]) << endl;
    cout << "Pred: " << objekty[0]->getHistorie().size();
    odstranMale(objekty[0]);
    cout << " -> Po: " << objekty[0]->getHistorie().size() << endl << endl;

    // 5) test operátorů — v BLOKU, aby objekty zanikly před kontrolou čítače
    {
        TypB a("LOK-A", 50), b("LOK-B", 50), c("LOK-C", 99);
        a += 100;
        b += 100; b += 200;
        cout << a << " == " << b << " ? " << ((a == b) ? "ANO" : "NE") << endl;
        cout << a << " == " << c << " ? " << ((a == c) ? "ANO" : "NE") << endl;
    }   // ← zde lokální objekty zanikají

    // 6) úklid paměti + kontrola
    for (size_t i = 0; i < objekty.size(); i++) delete objekty[i];
    objekty.clear();
    cout << endl << "Konecny pocet: " << Zaznam::getPocetObjektu() << endl;

    return 0;
}
```

### Ověřený výstup

```
Pocatecni pocet: 0
Po alokaci: 3

Zaznam: B-1 | Pocet polozek: 8 | Typ: B | Koeficient: 50
Analyza (B-1): Zapornych polozek: 3
Zaznam: B-2 | Pocet polozek: 5 | Typ: B | Koeficient: 0
Analyza (B-2): Zapornych polozek: 2
Zaznam: A-1 | Pocet polozek: 4 | Typ: A | Parametr: 4.5
Analyza (A-1): Prumer kladnych je 3333.33

Nejdelsi serie: 3
Pred: 8 -> Po: 6

TypB[LOK-A], polozek: 1 == TypB[LOK-B], polozek: 2 ? ANO
TypB[LOK-A], polozek: 1 == TypB[LOK-C], polozek: 0 ? NE

Konecny pocet: 0
```

---

# ČÁST III — KLÍČOVÉ KONCEPTY

## 8. Konstruktory a inicializační seznam

**Konstruktor** se spustí automaticky při vzniku objektu. Má stejný název jako třída a **nemá návratový typ**.

```cpp
Zaznam(const std::string& n);   // deklarace v .h
```

Rozklad: `Zaznam` je název (= název třídy), `(const std::string& n)` je co dostane zvenčí. `const` = nezmění to, `&` = nekopíruje, jen si půjčí přístup.

**Inicializační seznam** je část za dvojtečkou, před tělem:

```cpp
Zaznam::Zaznam(const string& n) : nazev(n) {
    pocetObjektu++;
}
```

Zápis `nazev(n)` znamená: **do atributu `nazev` ulož hodnotu z parametru `n`**. Vlevo cíl (trvalý atribut), vpravo zdroj (dočasný parametr).

### U dědičnosti: atributy předka nastavuje předek

Nejčastější chyba u odvozených tříd:

```cpp
// CHYBA — nazev patří předkovi, nelze ho nastavit přímo
TypA::TypA(const string& n, double p) : nazev(n), parametr(p) { }

// SPRÁVNĚ — hodnotu pošli předkovi, ať si ji uloží sám
TypA::TypA(const string& n, double p) : Zaznam(n), parametr(p) { }
```

Pravidlo: **v inicializačním seznamu potomka se předek uvádí svým názvem** (`Zaznam(n)`), ne názvem jeho atributu. Vlastní atributy potomka se uvádějí normálně (`parametr(p)`).

### Inicializační seznam patří jen do .cpp

V hlavičce je konstruktor **jen deklarovaný** — končí středníkem, žádná dvojtečka, žádné tělo:

```cpp
// .h — správně
TypA(const std::string& n, double p);

// .h — CHYBA, sem inicializační seznam nepatří
TypA(const std::string& n, double p) : Zaznam(n), parametr(p);
```

---

## 9. Statický čítač instancí

**Statický atribut** existuje jednou pro celou třídu, ne pro každý objekt zvlášť. Proto se hodí na počítání instancí.

Vyžaduje **dva řádky ve dvou souborech**:

```cpp
// v .h — DEKLARACE (pouze oznámení, že čítač existuje)
static int pocetObjektu;

// v .cpp — DEFINICE (skutečné místo v paměti + počáteční hodnota)
int Zaznam::pocetObjektu = 0;
```

Bez druhého řádku se projekt přeloží, ale **linker skončí chybou** `undefined reference to Zaznam::pocetObjektu`. Toto je nejčastější chyba u statických členů.

Rozklad definice: `int` (typ — každá proměnná ho potřebuje), `Zaznam::` (patří této třídě, ne volná proměnná), `pocetObjektu` (název), `= 0` (počáteční hodnota).

**Statická metoda** nepracuje s konkrétním objektem, čte jen statické členy:

```cpp
// v .h
static int getPocetObjektu();

// v .cpp — slovo static se NEOPAKUJE
int Zaznam::getPocetObjektu() {
    return pocetObjektu;
}
```

Volání přes název třídy: `Zaznam::getPocetObjektu()`.

**Mechanismus čítače:** `++` v konstruktoru, `--` v destruktoru. Pokud čítač na konci programu ukazuje nulu, byly všechny objekty korektně uvolněny.

---

## 10. Virtuální metody a polymorfismus

**Polymorfismus** znamená, že volání přes ukazatel na bázi spustí verzi podle **skutečného typu objektu**, ne podle typu ukazatele.

```cpp
Zaznam* p = new TypB("B-1", 50);   // typ ukazatele: Zaznam*, skutečný objekt: TypB
p->vypisInfo();                     // spustí se TypB::vypisInfo() — díky virtual
```

Bez `virtual` by se rozhodovalo podle typu ukazatele a spustila by se verze báze.

### Rozšíření metody předka

Potomek nemusí opisovat celou logiku — zavolá verzi předka a přidá svou část:

```cpp
void TypA::vypisInfo() const {
    Zaznam::vypisInfo();                    // předek vypíše základ
    cout << " | Typ: A | Parametr: " << parametr << endl;
}
```

Zápis `Zaznam::vypisInfo()` znamená „zavolej verzi z předka". Bez `Zaznam::` by metoda volala sama sebe do nekonečna.

### Virtuální destruktor

Je nezbytný vždy, když se objekty potomků mažou přes ukazatel na bázi:

```cpp
Zaznam* p = new TypB("B", 50);
delete p;    // který destruktor se zavolá?
```

- **bez `virtual`**: zavolá se pouze `~Zaznam()`. Část potomka zůstane neuklizená → únik paměti, nedefinované chování.
- **s `virtual`**: zavolá se `~TypB()`, pak automaticky `~Zaznam()`. Celý objekt se uklidí korektně.

**Pravidlo:** má-li třída jakoukoli virtuální metodu nebo z ní někdo dědí, destruktor musí být `virtual`.

### override

Nepovinné, ale doporučené. Zajišťuje, že metoda skutečně přepisuje virtuální metodu předka — překlep (jiné jméno, chybějící `const`) odhalí překladač:

```cpp
void vypisInfo() const override;
```

---

## 11. Abstraktní třída a `= 0`

**Čistě virtuální metoda** (`= 0`) nemá tělo v bázi; každý potomek ji **musí** definovat. Třída s alespoň jednou takovou metodou je **abstraktní** a nelze z ní vytvořit instanci.

```cpp
virtual void analyzuj() const = 0;   // v .h báze
// v .cpp báze se NEDEFINUJE
```

Kombinace `virtual` a `= 0` je **nedělitelná** — samotné `= 0` bez `virtual` překladač odmítne:

```cpp
void analyzuj() const = 0;           // CHYBA — chybí virtual
virtual void analyzuj() const = 0;   // správně
```

### Rozdíl proti běžné virtuální metodě

| | `virtual void m() const = 0;` | `virtual void m() const;` |
|---|---|---|
| tělo v bázi | ne | ano |
| potomek | **musí** definovat | může rozšířit |
| dělá třídu abstraktní | ano | ne |

Typické rozdělení: metoda, jejíž chování se u každého potomka zásadně liší (`analyzuj`), je čistě virtuální. Metoda s částí společnou pro všechny (`vypisInfo`) je běžně virtuální a potomci ji rozšiřují.

```cpp
Zaznam z("X");                  // CHYBA — abstraktní třída
Zaznam* p = new TypB("X", 50);  // v pořádku — ukazatel na bázi
```

---

## 12. Přetěžování operátorů

### Co to je

C++ umí porovnávat a vypisovat základní typy samo (`5 == 3`, `cout << 5`). U vlastních tříd to neví — netuší, co znamená „dva objekty jsou si rovny". **Přetěžování operátorů je návod, kterým to překladač naučíš.** Operátor je jen funkce se speciálním názvem (`operator==`).

### Klíčové pravidlo: rozhoduje levý operand

Operátor jako **metoda** se automaticky volá na objektu **vlevo**. Proto musí být vlevo objekt naší třídy.

```cpp
obj == druhy    // vlevo: obj (naše třída)   → METODA (uvnitř třídy)
obj += 100      // vlevo: obj (naše třída)   → METODA (uvnitř třídy)
cout << obj     // vlevo: cout (cizí typ!)   → VOLNÁ FUNKCE (mimo třídu)
```

U `<<` je vlevo `cout` (typ `std::ostream`). Metoda by musela patřit třídě `ostream`, do níž nelze zasahovat. Proto se `<<` píše jako volná funkce **za** závorkou `};` třídy.

**Pomůcka:** co stojí nalevo? Náš objekt → metoda dovnitř. `cout`/`cin` → volná funkce ven.

### Přehled

| Operátor | Forma | Návratový typ | Vlevo | Vidí private přímo |
|---|---|---|---|---|
| `==`, `!=`, `<` | metoda | `bool` | náš objekt | ano |
| `+`, `-`, `*` | metoda | nový objekt | náš objekt | ano |
| `+=`, `-=` | metoda | `Trida&` (`*this`) | náš objekt | ano |
| `[]` | metoda | `T&` | náš objekt | ano |
| `<<`, `>>` | **volná funkce** | `ostream&`/`istream&` | **proud** | **ne → gettery** |

### Operátor `==`

```cpp
bool TypB::operator==(const TypB& druhy) const {
    return koeficient == druhy.koeficient;
}
```

Dostane druhý objekt (ten vpravo), porovná atributy, vrátí `bool`. `koeficient` je můj, `druhy.koeficient` je jeho. Použití: `a == b`.

### Operátor `+=`

```cpp
TypB& TypB::operator+=(double hodnota) {
    pridejPolozku(hodnota);   // zděděná metoda
    return *this;
}
```

Dostane hodnotu, změní objekt, vrátí **sám sebe**. `this` je ukazatel na aktuální objekt, `*this` je objekt samotný. Návratový typ `TypB&` a `return *this;` jsou u složených operátorů konvence (umožňují řetězení). Použití: `a += 100;`.

### Operátor `<<`

```cpp
ostream& operator<<(ostream& os, const TypB& obj) {
    os << "TypB[" << obj.getNazev() << "], polozek: " << obj.pocetPolozek();
    return os;
}
```

Rozklad: `ostream&` (vrací proud), `os` (= `cout`, to vlevo), `obj` (vypisovaný objekt, to vpravo), `return os;` (umožní řetězení `cout << a << b`).

Protože je funkce **mimo třídu**, nevidí soukromé atributy — data čte přes veřejné **gettery** (`getNazev()`, `pocetPolozek()`). Alternativou je deklarovat ji v hlavičce jako `friend`.

**Důsledek:** kvůli operátoru `<<` je potřeba mít v bázi i potomkovi `const` gettery. Metoda `getHistorie()` vracející ne-const referenci se z `const` objektu volat nedá — proto existuje samostatná `pocetPolozek() const`.

### Operátor je vždy pro jeden konkrétní typ

Zápis `operator<<(ostream&, const TypB&)` umí vypsat **jen `TypB`**. Pro jiný typ je nutný další operátor. Proto se deklaruje v hlavičce toho typu, ke kterému patří (byť mimo tělo třídy).

---

## 13. Reference vs. kopie

Reference (`&`) je alias — přístup k originálu. Bez `&` se vytvoří kopie.

```cpp
std::vector<double>& getHistorie();       // reference → změny se propíšou
std::vector<double>  getHistorie() const; // kopie → změny se zahodí
```

**Praktický důsledek:** algoritmus mazající prvky z historie potřebuje **referenci**. S kopií by mazal duplikát a originál by zůstal nedotčen — algoritmus by se zdál nefunkční.

```cpp
void odstranMale(Zaznam* z) {
    vector<double>& h = z->getHistorie();   // & je zásadní
    // ...
    h = vysledek;                            // přepis skutečné historie
}
```

Předávání parametrů:

| Zápis | Kopíruje | Lze měnit originál | Použití |
|---|---|---|---|
| `f(T x)` | ano | ne | malé typy (int, double) |
| `f(T& x)` | ne | ano | když chceme měnit |
| `f(const T& x)` | ne | ne | čtení velkých objektů (string, vector) |

---

## 14. Správa paměti

Každému `new` musí odpovídat právě jedno `delete`; u polí `delete[]`.

```cpp
vector<Zaznam*> objekty;
objekty.push_back(new TypB("B-1", 50));    // alokace

for (size_t i = 0; i < objekty.size(); i++)
    delete objekty[i];                      // uvolnění
objekty.clear();
```

`delete` volá destruktor (a díky `virtual` ten správný), destruktor sníží čítač. Proto po úklidu čítač ukazuje nulu.

### Past: lokální objekty a čítač

Objekty vytvořené na zásobníku (bez `new`) žijí až do konce svého bloku. Vytvoří-li se v `main` pro test operátorů, existují ještě při závěrečné kontrole čítače:

```cpp
// PROBLÉM
TypB a("A", 50), b("B", 50);
// ... testy ...
for (...) delete objekty[i];
cout << Zaznam::getPocetObjektu();   // vypíše 2, ne 0! a,b ještě žijí

// ŘEŠENÍ — uzavřít do bloku
{
    TypB a("A", 50), b("B", 50);
    // ... testy ...
}   // ← zde a,b zanikají, čítač se sníží
for (...) delete objekty[i];
cout << Zaznam::getPocetObjektu();   // 0
```

---

# ČÁST IV — ALGORITMY

## 15. Vzor: nejdelší souvislá série

**Úloha:** kolik prvků splňujících podmínku následuje bezprostředně za sebou (nejdelší taková řada).

**Myšlenka:** dvě počítadla. `aktualni` roste při splnění podmínky a **nuluje se při porušení**; `nejdelsi` si pamatuje dosažené maximum.

```cpp
int nejdelsiSerie(Zaznam* z) {
    int nejdelsi = 0, aktualni = 0;
    vector<double>& h = z->getHistorie();
    for (size_t i = 0; i < h.size(); i++) {
        if (h[i] > 0) {                     // ← zde podmínka ze zadání
            aktualni++;
            if (aktualni > nejdelsi) nejdelsi = aktualni;
        } else {
            aktualni = 0;                   // reset série
        }
    }
    return nejdelsi;
}
```

Průběh na datech `{100, 200, 300, -20, 500, 600, -1000, -45}`:

| prvek | podmínka | aktualni | nejdelsi |
|---|---|---|---|
| 100 | ano | 1 | 1 |
| 200 | ano | 2 | 2 |
| 300 | ano | 3 | **3** |
| -20 | ne | 0 | 3 |
| 500 | ano | 1 | 3 |
| 600 | ano | 2 | 3 |
| -1000 | ne | 0 | 3 |
| -45 | ne | 0 | 3 |

Výsledek: 3.

**Typická chyba:** aktualizovat `nejdelsi` až po cyklu — pak se ztratí série, která nekončí na konci vektoru. Maximum se musí kontrolovat **uvnitř** cyklu.

---

## 16. Vzor: podmíněné mazání z vektoru

**Úloha:** odstranit z vektoru prvky splňující podmínku.

**Myšlenka:** nemazat za běhu (posouvání indexů je zdroj chyb), ale **postavit nový vektor jen z prvků, které mají zůstat**, a přepsat jím originál.

```cpp
void odstranMale(Zaznam* z) {
    vector<double>& h = z->getHistorie();       // reference na originál
    vector<double> vysledek;                    // pomocný vektor
    for (size_t i = 0; i < h.size(); i++) {
        bool smazat = (h[i] < 0 && h[i] > -50); // ← podmínka ze zadání
        if (!smazat) vysledek.push_back(h[i]);  // ponechat
    }
    h = vysledek;                                // přepis originálu
}
```

**Pozor na ostré hranice.** U podmínky „záporné a zároveň větší než -50" (interval −49,99 až −0,01):

| hodnota | `< 0` | `> -50` | smazat |
|---|---|---|---|
| -20 | ano | ano | **ano** |
| -45 | ano | ano | **ano** |
| -50 | ano | **ne** | ne |
| -100 | ano | ne | ne |
| 50 | ne | — | ne |

Hodnota −50 se **nemaže** — není větší než −50. Záměna `>` a `>=` je častá ztráta bodů.

---

## 17. Vzor: agregace s filtrem

**Úloha:** spočítat součet, počet nebo průměr z prvků splňujících podmínku.

```cpp
void TypA::analyzuj() const {
    double soucet = 0;                      // inicializace na 0 je nutná
    int pocet = 0;
    for (size_t i = 0; i < historie.size(); i++) {
        if (historie[i] > 0) {              // filtr
            soucet += historie[i];
            pocet++;
        }
    }
    double prumer = (pocet > 0) ? (soucet / pocet) : 0;   // ochrana proti /0
    cout << "Prumer: " << prumer << endl;
}
```

Zápis `(pocet > 0) ? (soucet / pocet) : 0` je ternární operátor — „je-li podmínka splněna, použij první výraz, jinak druhý". Ekvivalent:

```cpp
double prumer;
if (pocet > 0) prumer = soucet / pocet;
else prumer = 0;
```

**Dvě častá opomenutí:** neinicializovaná proměnná (`double soucet;` má nedefinovanou hodnotu — vždy `= 0`) a nechráněné dělení nulou (prázdný filtr → pád programu → −10 bodů).

Pro pouhé počítání stačí jedno počítadlo:

```cpp
int pocetZapornych = 0;
for (size_t i = 0; i < historie.size(); i++)
    if (historie[i] < 0) pocetZapornych++;
```

---

# ČÁST V — LADĚNÍ A KONTROLA

## 18. Syntaktické chyby, které dělá každý

| Chyba | Špatně | Správně |
|---|---|---|
| Jedna dvojtečka místo dvou | `void Trida: metoda()` | `void Trida::metoda()` |
| Chybějící návratový typ | `Trida::metoda() { }` | `void Trida::metoda() { }` |
| Středník za tělem metody | `void Trida::m() { };` | `void Trida::m() { }` |
| Chybějící středník za třídou | `class A { }` | `class A { };` |
| Volání metody špičatými závorkami | `v.push_back<x>;` | `v.push_back(x);` |
| Obrácený zápis metody | `size.vektor()` | `vektor.size()` |
| Čárka ve `for` místo středníku | `for (int i=0, i<n; i++)` | `for (int i=0; i<n; i++)` |
| Dědičnost přes `class` | `class B : class A` | `class B : public A` |
| Inicializační seznam v `.h` | `A(int x) : a(x);` | `A(int x);` |
| `= 0` bez `virtual` | `void m() const = 0;` | `virtual void m() const = 0;` |
| Atribut předka v seznamu potomka | `: nazev(n)` | `: Predek(n)` |
| Chybějící `const` v `.cpp` | `void A::m() { }` | `void A::m() const { }` |
| Neinicializovaná proměnná | `double s;` | `double s = 0;` |
| Neshoda velikosti písmen | `.h`: `getX`, `.cpp`: `getx` | stejně v obou |
| `static` opakovaný v `.cpp` | `static int A::f() { }` | `int A::f() { }` |
| `operator<<` uvnitř třídy | v těle `class { }` | za `};` |
| Vkládání `.cpp` | `#include "A.cpp"` | `#include "A.h"` |

**Zásada:** názvy metod, jejich parametry i `const` musí být v `.h` a `.cpp` **naprosto identické**, včetně velikosti písmen. Jakýkoli rozdíl znamená, že překladač vidí dvě různé metody — deklarovanou bez definice a definovanou bez deklarace.

---

## 19. Chybové hlášky a jejich příčiny

| Hláška (zkráceně) | Nejčastější příčina |
|---|---|
| `undefined reference to 'A::x'` | chybí definice statického atributu v `.cpp` |
| `undefined reference to 'A::m()'` | chybí `A::` před metodou, nebo neshoda názvu/`const` mezi `.h` a `.cpp` |
| `cannot declare variable to be of abstract type` | pokus vytvořit instanci abstraktní třídy |
| `no matching function for call to ...` | neshoda parametrů (počet, typy, `const`) |
| `'x' was not declared in this scope` | chybí `#include`, nebo `std::` u `string`/`vector` |
| `expected ';' before ...` | chybí středník (často za `class { }`) |
| `passing 'const A' as 'this' discards qualifiers` | `const` metoda volá ne-`const` metodu |
| `only virtual member functions can be marked pure` | `= 0` bez `virtual` |
| `'nazev' is not a nonstatic data member of ...` | v inicializačním seznamu potomka je atribut předka |
| `no match for 'operator<<'` | `<<` chybí, nebo je napsán jako metoda místo volné funkce |
| `comparison of integer expressions of different signedness` | porovnání `int` s `.size()` — použij `size_t` nebo přetypuj |

**Postup při chybě:** čti **první** hlášku, ne poslední — další jsou obvykle jen následky. Hláška obsahuje číslo řádku; chyba je na něm nebo na řádku před ním.

---

## 20. Otázky k ústní části

**Co je polymorfismus?**
Volání metody přes ukazatel nebo referenci na bázi spustí verzi odpovídající skutečnému typu objektu, ne typu ukazatele. Umožňuje ho `virtual`.

**Proč musí být destruktor virtuální?**
Aby se při mazání objektu potomka přes ukazatel na bázi zavolal správný destruktor a uklidil se celý objekt. Bez `virtual` se zavolá jen destruktor báze a část potomka zůstane neuvolněná.

**Co znamená `= 0` u metody?**
Čistě virtuální metoda — bez těla v bázi, každý potomek ji musí definovat. Třída se tím stává abstraktní a nelze z ní vytvořit instanci.

**Jaký je rozdíl mezi `private` a `protected`?**
`private` členy vidí pouze třída sama, `protected` i odvozené třídy. Atributy báze jsou `protected` proto, aby k nim potomci mohli přistupovat ve svých metodách.

**Co je statický člen?**
Člen existující jednou pro celou třídu, sdílený všemi objekty. Statický atribut se deklaruje v `.h` a definuje v `.cpp`; statická metoda nemá `this` a volá se přes název třídy.

**Proč je operátor `<<` volná funkce, a ne metoda?**
Protože v zápisu `cout << objekt` je levým operandem `cout` (typ `std::ostream`). Metoda se volá na objektu vlevo, což by znamenalo metodu třídy `ostream`, do níž nelze zasahovat. Volná funkce dostane oba operandy jako parametry.

**Proč `+=` vrací referenci na sebe?**
Aby bylo možné řetězení a aby se zachovala konzistence se sémantikou vestavěných typů. Vrací se `*this`, tedy samotný objekt.

**Kdy použít referenci a kdy hodnotu u návratového typu?**
Referenci, když má volající měnit originál (například mazat prvky z historie). Hodnotu, když stačí jen přečíst data a změny se nemají propisovat.

**K čemu slouží inicializační seznam?**
K inicializaci atributů před vstupem do těla konstruktoru a k volání konstruktoru předka. Je efektivnější než přiřazení v těle a nutný pro `const` atributy a reference.

**Jak se prokáže, že program neuniká pamětí?**
Statickým čítačem instancí: `++` v konstruktoru, `--` v destruktoru. Ukazuje-li čítač na konci programu nulu, byly všechny objekty korektně zrušeny.

---

## 21. Kontrolní seznam před odevzdáním

### Překlad a běh
- [ ] Projekt se přeloží bez chyb: `g++ -std=c++17 -Wall -Wextra -o program *.cpp`
- [ ] Nejsou žádná varování
- [ ] Program proběhne bez pádu

### Struktura
- [ ] Každá třída má vlastní `.h` a `.cpp`
- [ ] Každá hlavička má include guard
- [ ] Nikde není `#include` souboru `.cpp`

### Bázová třída
- [ ] Atributy jsou `protected`
- [ ] Statický čítač je **definován v `.cpp`** (`int Trida::citac = 0;`)
- [ ] Destruktor je `virtual`
- [ ] Alespoň jedna metoda je `= 0` (třída je abstraktní)
- [ ] Getter historie vrací **referenci** (`vector<double>&`)
- [ ] Existuje `const` getter pro potřeby operátoru `<<`

### Odvozené třídy
- [ ] Dědí přes `: public Baze`
- [ ] Konstruktor volá konstruktor předka (`: Baze(n), vlastni(p)`)
- [ ] Obě virtuální metody jsou definované a označené `override`
- [ ] `vypisInfo` volá `Baze::vypisInfo()` a doplňuje vlastní část

### Operátory
- [ ] `==` je metoda, vrací `bool`
- [ ] `+=` je metoda, vrací `Trida&` a končí `return *this;`
- [ ] `<<` je **volná funkce za `};`**, vrací `ostream&` a končí `return os;`
- [ ] `<<` čte data přes gettery

### Algoritmy
- [ ] Všechny akumulátory jsou inicializované na 0
- [ ] Dělení je chráněné proti nule
- [ ] Hranice intervalů odpovídají zadání (`>` vs. `>=`)
- [ ] Algoritmus mazání pracuje s referencí, ne s kopií

### Paměť
- [ ] Každému `new` odpovídá `delete`
- [ ] Lokální testovací objekty jsou uzavřené v bloku `{ }`
- [ ] Čítač na konci programu ukazuje **0**

---

*Veškerý kód v této příručce byl přeložen překladačem g++ (standard C++17, přepínače `-Wall -Wextra`) bez varování a spuštěn s ověřeným výstupem.*
