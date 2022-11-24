# Object Oriented Programming (OOP)

*Pokračujme s přirovnáním, že objekty fungují jako samostatné počítače, které mají svůj vlastní program (třída objektu). Instance je konkrétní běžící počítač, na němž běží program určené třídy. Instance (počítače) komunikují mezi sebou, vědí o sobě skrze laserová ukazovátka (reference) , tj. lze zavolat operaci (metodu) jiného počítače (jiné instance), když mám laserové ukazovátko na cílový počítač (instanci) a cílový počítač mi operaci (metodu) nabízí (public).*

## Třída vs. instance

Třída objektu je předpis programu (struktury) pro konkrétní objekt, např. `Figurka`, `Kostka`, `Hrac`.

```csharp
public class HerniPlan
{
    // definice (metody, fieldy, properties, constructor, ...)
}
```

Předpis ("program") třídy tvoří:

* jméno třídy (součástí plného jména je namespace a další údaje, o tom někdy později),
* operace (metody),
* data (fieldy, atributy, hybridem mezi operací a daty jsou properties, viz níže)

Instance objektu je konkrétní spuštěný "počítač" na kterém běží "program" pro konkrétní třídu objektu (např. modrá figurka č. 1, modrá figurka č. 2, hráč Petr, hráč Karel, zelená kostka v mojí kapse, ...)

```csharp
var plan = new HerniPlan();
```

Instanci (nový počítač) vytvářím zásadně pomocí operátoru `new` (o konstruktorech níže), výsledek (laserové ukazovátko na instanci) si uložím do proměnné (nebo jinam, viz níže).

## Metody (operace)

Metoda je funkce/operace na třídě, volám ji prostřednictvím třídy (laserového ukazovátka).

```csharp
public class HerniPlan
{
    public Policko PosunFigurku(Figurka figurkaKterouTahnu, int pocetPolicekOKolik)
    {
        // výkonný kód metody
        return vysledek; // alespoň jednou, ale i vícekrát
    }
}
```

Metoda, která nic nevrací (nemá návratovou hodnotu):

```csharp
public void VykresliNaObrazovku()
{
    // nic nevracím, modifikuji vnitřní stav nebo jinak manipuluji se světem, aniž bych vracel konkrétní hodnotu
    // volitelně return (samotný), pokud chci ukončit předčasně
}
```

### Volání metod

Volání metody "z venku" (laserové ukazovátko):

```csharp
// vytvořím novou instanci a zapamtuji si laserové ukazovátko na ní do proměnné "plan"
var plan = new HerniPlan();
// posílám signal Vykresli() instanci na kterou ukazuje ukazovátko "plan"
plan.Vykresli();
```

Volání metody "uvnitř" třídy samotné (`this` je ukazovátko sám na sebe, zrcátko :-)):

```csharp
public class HerniPlan
{
    public void DoSomething()
    {
        this.Vykresli();
        Vykresli(); // this je nepovinné, pokud je zápis jednoznačný i bez něj
    }
}
```

Sám mohu pracovat i s jinou instancí téže třídy

```csharp
public class Figurka
{
    public Policko Posun(HerniPlan plan, int pocetPolicek)
    {
        // získám laserové ukazovátko na druhou figurku
        var figurkaNaCilovemPolicku = plan.DejFigurku(this.pozice, pocetPolicek);
        // signál VyhodSe() posílám druhé figurce, ne sám sobě
        figurkaNaCilovemPolicku.VyhodSe();
    }
    public void VyhodSe()
    {
        this.pozice = 0;
    }
}
```

#### (Přetěžování metod (overloading) - více definic s různými parametry)

Stejně jako konstruktor (viz níže), i metoda může být definována vícekrát s různými parametry

```csharp
public class Hrac
{
    public void Vykresli()
    {
        // ...
    }
    public void Vykresli(Barva barvaHrace)
    {
        // ...
    }
}
```

Podle toho, s jakými parametry metodu volám, taková verze se spustí.

## Vnitřní stav (field, data, atributy)

Data, která si instance třídy pamatuje i po skončení metody (mezi jednotlivými operacemi).

Tvoří se tzv. "fieldy":

```csharp
public class Hrac
{
    private string jmenoHrace;
    int pocetFigurek; // private je výchozí viditelnost, nemusím uvádět
}
```

Field třídy = jako bych deklaroval proměnnou, ale ne uvnitř metody, ale na úrovni celé třídy.

Každá instance třídy má svojí paměť, svoje hodnoty datových fieldů.

Přístup k fieldům je tečkou:

```csharp
public class Hrac
{
    string jmenoHrace; // neuvedl jsem modifikátor přístupu, tedy platí výchozí private
    public void NastavJmeno(string pozadovaneJmeno)
    {
        this.jmenoHrace = pozadovaneJmeno;
        jmenoHrace = pozadovaneJmeno; // this je nepovinné, pokud nehrozí kolize názvu
    }
    public string DejJmeno()
    {
        return this.jmenoHrace;
    }
    public void OtocJmeno()
    {
        var segmenty = this.jmenoHrace.Split(' ');
        this.jmenoHrace = jmenoHrace.Substring(0, jmenoHrace.IndexOf(' ')) + segmenty[1]
    }
}
```

### Modifikátory přístupu

Metody, fieldy a další membery se označují modifikátory přístupu:

* `private` (výchozí) - přístup pouze ze stejné třídy (nelze použít zvenku, viz zapouzdření níže)
* `public` - přístup odkudkoliv
* (a další variace, např.`protected` - přístup ze stejné třídy a jejích potomků při dědění)

## Konstruktor (constructor)

Metoda, která se volá při vytváření instance ("při spouštění počítače s programem dané třídy").

Nemá návratovou hodnotu (neuvádí se ani `void`), jmenuje se stejně jako třída.

```csharp
public class Hrac
{
    // výchozí konstruktor, který se automaticky vytvoří, pokud já nedefinuji jiný
    public Hrac() // nemá parametry
    {
        // nic nedělá
    }
}
```

Konstruktor může mít parametry (výhodně tak mohu vynutit zadání vstupních hodnot - nutného počátečního stavu)

```csharp
puvlic class Hrac
{
    private string jmenoHrace;
    public Hrac(string jmeno)
    {
        this.jmenoHrace = jmeno;
    }
}
```

Konstruktor s parametry pak volám jako metodu:

```csharp
var hrac = new Hrac("Pepa");
```

Třída může mít více než jeden konstruktor

```csharp
public class Hrac
{
    private string jmeno;
    public Hrac(string pozadovaneJmeno)
    {
        this.jmeno = pozadovaneJmeno;
    }
    public Hrac()
    {
        this.jmeno = "Anonym";
    }
    /*
    // error - nelze vytvořit metodu se stejnými parametry
    public Hrac(string jmenoManzelky)
    {
        // něco
    }
    */
}
```

Při volání se použije vždy jen jeden z nich (s odpovídajícími parametry).

```csharp
var hrac1 = new Hrac("Pepa"); // zavolá se ctor s variantou přijímající pozadovaneJmeno
var hrac2 = new Hrac(); // vznikne Anonym
```



## Zapouzdření



## Properties (operace, které se tváří jako data)



## Kompozice (odkazy mezi objekty)



## Dědičnost



## Polymorfizmus



## Statické třídy/membery



