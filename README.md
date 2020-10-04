# Projekt Vytvorenie slovníka dvojíc pre účely Named Entity Recognizing 


## Zadanie

Vytvorenie slovníka dvojíc pre účely Named Entity Recognizing: pojem - typ (priradenie kategórií ako person, locality, organization jednotlivým stránkam z wikipédie)

Možnosť rozšírenia o kategórie ako napríklad spoločnosť, alebo zmiešaný typ.

## Príklad vstupu a výstupu

```
Vstup => WikiPage Slovakia
Výstup => (Slovakia, Location)
```

## Dáta 

Na prácu som si zvolil dáta z Anglickej wikipédie, konkrétne súbor [enwiki-latest-pages-articles](http://dumps.wikimedia.org/skwiki/latest/enwiki-latest-pages-articles.xml.bz2).

## Popis riešenia

Princípom tohto projektu je prehľadávanie zväčša infoboxov stránok, za účelom získania informácií, podľa ktorých by bolo možné určiť typ entity týchto stránok. 
Z týchto infoboxov(príklad nižšie) sú dôlezité atribúty Osobnosť, dátum narodenia/umrtia. 
Okrem týchto infoboxov sa javí ako dobrým atribútom aj Kategória, podľa ktorej je taktiež možné zaradenie do našich kategórií.

Výsledok tohto projektu je možné použiť na porovnávanie stránok za účelom zistenia podobností. 
Týmto by bolo možné kontrolovať kategórie iných/nových článkov. 
Na tento účel je možné použiť štatistické vyhodnocovanie, alebo použiť princípy machine learningu.

#### Update konzultacia 2

Na účel čítania a následného parsovania jednotlivých stránok som použil prístup, ktorý nevyžaduje dekompresiu súborov, ktorých obsah má veľkosť 50 GB.
Konkrétne sa jedná o príncip postúpneho dekopresovania súborov pomocou podprocesu bzcat, ktorý dodáva jednotlivé riadky zo súboru postupne.
Na extrahovanie informácií z článkov je použitý SAX parser(The Simple API for XML).
SAX umožnuje oproti iným parserov(DOM) spracovanie XML súboru po každom riadku zvlášť, čo vyhovuje použitej technológií čítania súboru.

Každý článok je indetifikovaný na základe tagov: <page> </page>
Z každého takéhoto články je možné extrahovať title: <title> </title> a text: <text>.
Text obsahuje všetok obsah článku vrátane Infoboxov.
Ďalšie informácie z infoboxov, ako aj z textu je možné pomocou regulárnych výrazov.

Po extrahovaní všetkých dôležitých informácií je možné určiť kategóriu tohto článku a uložiť túto skutočnosť ako tuple názvu stránky a kategórie.


## Implementácia projektu

Na implementácia bude prebiehať v jazyku Python.


### Ukážka infobox

```
{{Infobox Osobnosť
| Meno = Alan Turing 
| Rodné meno = 
| Portrét = Alan Turing Aged 16.jpg 
| Veľkosť portrétu = 
| Popis = britský matematik, logik, kryptograf a vojnový hrdina 
| Dátum narodenia = [[23. jún]] [[1912]] 
| Miesto narodenia = [[Londýn]], [[Spojené kráľovstvo]] 
| Dátum úmrtia = {{duv|1954|6|7|1912|6|23}} 
| Miesto úmrtia = [[Wilmslow]], [[Spojené kráľovstvo]]
}}
```