---
title: "TODO or not TODO - to je otázka"
date: "2017-05-15T11:22:00Z"
tags: ["php", "komentáre"]
categories: ["Development"]
---

Máloktoré z kľúčových slov v komentároch má tak voľnú interpretáciu ako TODO.
Je použitie TODO komentárov dobré, alebo zlé? Prináša nám v kóde pridanú hodnotu,
alebo nie? Na tieto a iné otázky sa budem snažiť nájsť odpoveď v tomto článku.
<!--more-->

Mnohí z nás sa už v praxi určite stretli s jedným z nasledujúcich kľúčových slov
v komentároch kódu: TODO, FIXME, XXX, OTHER, HACK a podobne. Všetky
tieto slová patria do "TODO rodiny". V mojej osobnej praxi som sa s inými, ako
TODO a FIXME nestretol, a keďže som sa nechal pre tento článok inšpirovať 
výskumom [\[1\]]({{< ref "todo-or-not-todo-to-je-otazka.md#r1" >}}), kde sú
iné slová v značnej minorite, nebudem sa inými ani zaoberať. 

Ak sa lepšie zamyslíme, potom zistíme, že TODO v skutočnosti naznačuje bod,
v ktorom bolo urobené rozhodnutie - rozhodnutie odložiť konanie, v skutočnosti
rozhodnutie neurobiť niečo. Toto rozhodnutie je nepochybne menej ako ideálne [\[2\]]({{< ref "todo-or-not-todo-to-je-otazka.md#r2" >}}).

Myslím, že pri vhodnom použití nám však TODO môže priniesť osoh, ale tak ako pri
použití hocijakých iných komentároch sa môže použitie stať neaktuálne, zabudnuté,
alebo ignorované. Je preto dôležité sa zamyslieť na tom, kedy TODO použiť, a kedy
použiť niečo vhodnejšie.

Kľúčové slovo FIXME sa v komentároch často považuje za zameniteľné s TODO.
Názov ale s väčším dôrazom naznačuje, že niečo nie je v poriadku. Ak je teda
TODO považované za žiadosť o zmenu, potom je FIXME považované za hlásenie chyby.

## Príklady použitia

Toto sú niektoré príklady použitia podľa [\[1\]]({{< ref "todo-or-not-todo-to-je-otazka.md#r1" >}}):

**Podúlohy**

Vyznačenie podúloh pri vykonávaní väčšej úlohy, v prípade vyššej priority.
Pri vývoji takto osobne často označujem podúlohy, ku ktorým sa čoskoro v rámci
môjho zadania, vo svojej pracovnej vetve, vrátim. Tieto komentáre však vytváram
len pre osobné využitie a do produkčnej vetvy sa nikdy nedostanú.
  
**Krátkodobé úlohy**

Vyznačenie podúloh, pre vykonanie vo veľmi krátkej dobe.
Osobne používam TODO v tomto prípade asi najčastejšie. Často pri ukladaní zmien
vo verziovaciom systéme na konci dňa, keď viem, že sa k úlohe opäť dostanem
na ďalší deň. Do hlavnej vetvy sa z mojej pracovnej vetvy takéto komentáre
však nedostanú.

**Indikátor problémov**

Komunikácia k iným členom tímu a vyznačenie,
že programátor si je vedomý chyby. V mojom prípade takto označujem chyby, ktoré
nie sú jednoznačné na odstránenie (skryté závislosti, absencia testov),
ale nespôsobujú v kóde škodu. Prípadne nie sú až tak dôležité na odstránenie.
Inak chyby priamo odstraňujem. 

**Hraničné prípady**

Vyznačenie hraničných prípadov, ktoré sú zdĺhavé na implementáciu, prípadne
nie sú momentálne dôležité.

**Odkladanie úloh s nízkou prioritou**

Kód, ktorý by bolo vhodné zmeniť, ale momentálne to nie je dôležité. Myslím,
že toto je druh komentárov, ktorý je časom zabudnutý a tým pádom zbytočný,
pretože takéto zmeny nebudú pravdepodobne nikdy implementované. 

Ďalšie zaujímave príklady použitia:

**Mikro-manažment**

Iný zaujímavý príklad použitia je udaný v článku
[\[4\]]({{< ref "todo-or-not-todo-to-je-otazka.md#r4" >}}) od JetBrains, ktorý
popisuje spôsob manažmentu projektu pomocou TODO komentárov. Pre každého člena
týmu je v tomto prípade vytvorený TODO komentár so špecifickým značením `(\btodo-NAME\b.*)`:

```
// @todo-LUKAS
// @todo-JOZO
// @todo-MATEJ
```

S využitím vbudovanej funkcionality v PhpStorm-e, za pomoci filtrov, je takto možné
pre každého člena vytvoriť zoznam úloh. Myslím, že takýto prístup sa môže zísť
pri riadení malých projektov v počiatočnom štádiu, ak sa nekladie až taký veľký
dôraz na sledovanie pokrokov v tiketovacom systéme a pod. Inak je tento prístup
napríklad pri manažovaní budúci úloh značne nepraktický.

**Označenie aktuálne nepotrebných úloh/funkcionality**

V zdrojovom kóde Zend Framework-u [\[6\]]({{< ref "todo-or-not-todo-to-je-otazka.md#r6" >}})
možno vidieť komentáre podobné tomuto:

```
/**
 * HTTP Authentication Adapter
 *
 * Implements a pretty good chunk of RFC 2617.
 *
 * @todo       Support auth-int
 * @todo       Track nonces, nonce-count, opaque for replay protection and stale support
 * @todo       Support Authentication-Info header
 */
``` 

Jedná sa o implementáciu RFC 2617, kde v tomto prípade nebolo pre použitie
dôležité implementovať niektoré z funkcionalít definovaných v RFC. Autori
však týmto komentárom poznamenali, že sú si vedomí neimplementovanej
funkcionality, čo môže napríklad upovedomiť vývojárov využívajúcich knižnicu
o chýbajúcej funkcionalite.

V čase písania kódu len prosím nepíšte komentáre typu:

```
// TODO improve this part
```

Aký zmyseľ má písať takýto kód, keď už v čase písania si je autor vedomý,
že kód je zlý a dokonca pridá takúto poznámku?


## Formátovanie TODO komentárov

Vo všetkých prípadoch použitia TODO je dôležité použiť vhodné metadáta ako
deskriptívny popis, prípadne iniciály autora, dátum, alebo číslo chyby v systéme
pre evidenciu chýb. V článku [\[3\]]({{< ref "todo-or-not-todo-to-je-otazka.md#r3" >}})
autor navrhuje nasledujúci formát TODO komentára, ktorý je vhodný pre spracovanie
nástrojmi pre manipuláciu s textom v termináli:

```
// TODO:2008-12-06:johnc:Add support for negative offsets.
// While it is unlikely that we get a negative offset, it can
// occur if the garbage collector runs out of space.
```

Pre automatickú extrakciu informácií z takýchto komentárov je však dôležité, aby
bol uchovaný štandardizovaný formát komentára.

V praxi som sa však najviac stretávam s formátom:

```
// TODO: Deskriptívny popis
```

Prípadne s phpDocumentor formátom [\[5\]]({{< ref "todo-or-not-todo-to-je-otazka.md#r5" >}}),
ktorý je v PHP preferovaný:

```
/**
 * @todo deskriptívny popis
 */
```

PhpDocumentor však nepodporuje iné slová z "TODO rodiny" ako `@todo`.


## Použiť TODO, alebo vytvoriť tiket?

Mnoho projektov používa pre evidenciu chýb na to určený softvér (napr. JIRA, Bugzilla,
Github a pod.). Nastáva teda otázka, či nieje vhodnejšie a prehľadnejšie uchovávať
chyby takto.

Prečo má vlastne zmysel sa zamýšľať nad TODO komentármi?
V [\[7\]]({{< ref "todo-or-not-todo-to-je-otazka.md#r7" >}}) autori uvádzajú, že
približne 50% pracovného času trávia vývojári v IDE
(Integrated Development Environment - vývojové prostredie). Síce sa jedná o článok
staršieho vydania a s ohľadom na Eclipse IDE, možno však usúdiť, že minimalizácia 
použitie iných nástrojov a využitie samotného IDE môže zefektívniť prácu. Napríklad
Eclipse a PhpStorm majú priamo vbudovanú funkcionalitu pre zobrazenie TODO v zozname.

V analýze [\[1\]]({{< ref "todo-or-not-todo-to-je-otazka.md#r1" >}})
dospeli k nasledujúcim záverom:

- použitie TODO komentárov sa ustáluje so zrelosťou projektu
- v stabilných a produkčných projektoch sa kvôli väčšej prehľadnosti chyby evidujú 
- pri malých chybách je často vytvorený TODO komentár, pri väčších tiket

Zo záverov možno teda usúdiť, že ideálnym kandidátom pre používanie TODO sú projekty
v počiatočnom štádiu vývoja, ktoré ešte neboli úplne použité v produkčnom prostredí.


## Nevýhody spojené s používaním                                   

TODO komentáre často dostanú nálepku autora. Tým pádom ostatní programátori
predpokladajú, že chyby budú odstránené autorom a takéto komentáre väčšinou ignorujú.

Komentáre tohto druhu sú často prehliadané a ľahko sa v zdrojovom kóde stratia.
Je preto pri ich používaní potrebné zaviesť proces v ktorom budú komentáre opäť
preskúmané, aktualizované, prípadne odstránené. Inak komentáre strácajú na význame.


## Zhodnotenie

Je teda použitie TODO komentárov dobré a prináša nám pridanú hodnotu? Určite,
ale iba v niektorých prípadoch. Kedy ho teda použiť? V počiatkoch projektu,
prípadne ako osobnú poznámku v pracovnej vetve pri práci na zadaní. 
TODO komentáre by som inak použil už len pre označenie potencionálne chýbajúcej
funkcionality, ktorá v danom čase nie je potrebná, podobne ako je to vidieť
v zdrojovom kóde Zend Framework-u. V iných prípadoch by som na evidenciu použil
tiketovací systém.


## Referencie

- <b id="r1">[1]</b> *TODO or To Bug: Exploring How Task Annotations Play a Role
  in the Work Practices of Software Developers*, **Margaret-Anne Storey, a iní**.
  Proceeding. ICSE '08 Proceedings of the 30th international conference
  on Software engineering. s. 251-260. ISBN: 978-1-60558-079-1.
- <b id="r2">[2]</b> Word Aligned. The case against TODO [online] [cit 2017-05-11].
  Dostupné na internete \<http://wordaligned.org/articles/todo\>
- <b id="r3">[3]</b> Approxion. TODO or not TODO [online]. [cit 2017-05-10].
  Dostupné na internete \<https://www.approxion.com/?p=39\>
- <b id="r4">[4]</b> WebStorm & PhpStorm Blog. Managing TODO comments in your code
  [online]. [cit 2017-05-10]. Dostupné na internete
  \< https://blog.jetbrains.com/webide/2012/10/managing-todo/ \>
- <b id="r5">[5]</b> phpDocumentor. @todo [online]. [cit 2017-05-10]. Dostupné
  na internete \< https://phpdoc.org/docs/latest/references/phpdoc/tags/todo.html \>
- <b id="r6">[6]</b> Zdrojový kód komponentu `Zend\Authentication`. Http.php [online]. [cit 2017-05-12]. Dostupné
  na internete \< https://github.com/zendframework/zend-authentication/blob/master/src/Adapter/Http.php \>
- <b id="r7">[7]</b> *How Are Java Software Developers Using the Eclipse IDE?*,
  **G.C. Murphy, M. Kersten, L. Findlater.**. IEEE Software, 23(4), s. 76-83, 2006.
  ISSN: 0740-7459.
