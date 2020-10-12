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

Druhov časťou projektu by bolo vytvorenie indexovania nad vytvorenými dvojicami a umožnenie vyhľadávania nad vytvorenými dátami. 
Tento index by sa ukladal na názov stránky (title) wikipédie, tak aby bolo rýchlo vyhľadatelná kategória na základe wiki stránky.
 

## Implementácia projektu

Na implementácia bude prebiehať v jazyku Python.

## Spustenie súboru

Projekt na nachádza v notebooku v adresári /src.
Na jeho spusteniu sa vyžadujú súbory(wikipedia dump), avšak v adresári data sa nachádza menší sample dát, ktorý je možné zvoliť na testovanie systému.

```diff
- POZOR: Nespúštať notebook ako celok. 
- POZOR: Notebook obsahuje časť na stiahnutie wikipedia dump (15 GB)

```

### Ukážka infobox

```
{{Infobox tennis biography
|name= Karol Kučera
|country= {{SVK}}
|residence= [[Monte Carlo]], [[Monaco]]
|birth_date= {{Birth date and age
|df=y|1974|3|4}}
|birth_place= [[Bratislava]], [[Czechoslovakia]]
|height= {{height|m=1.87}}\n
|turnedpro= 1992
|retired= 2005
|coach= [[Miloslav Mečíř]] (1997-2001) [[Marian Vajda]] (2001-2005)\n
|plays= Right-handed (two-handed backhand)
|careerprizemoney= [[US$|$]]5,061,125
|singlesrecord= 293–244
|singlestitles= 6
}}

[[Category:1974 births]]
[[Category:Hopman Cup competitors]]
[[Category:Living people]]
[[Category:People from Monte Carlo]]
[[Category:Slovak male tennis players]]
[[Category:Slovak expatriates in Monaco]]
[[Category:Sportspeople from Bratislava]]
[[Category:Olympic tennis players of Slovakia]]
[[Category:Tennis players at the 1996 Summer Olympics]]
[[Category:Tennis players at the 2000 Summer Olympics]]
[[Category:Tennis players at the 2004 Summer Olympics]]
[[Category:Czechoslovak male tennis players]]
```