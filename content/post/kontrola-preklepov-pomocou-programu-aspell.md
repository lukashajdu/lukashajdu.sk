---
title: "Kontrola preklepov pomocou programu Aspell"
date: "2017-03-09T17:44:00Z"
tags: ["terminál", "preklepy"]
categories: ["Development"]
---

Ak na písanie používate textový editor, ktorý nemá kontrolu preklepov, prípadne nemá podporu pre slovenský jazyk,
potom sa vám určite hodí program [Aspell](http://aspell.net/). Aspell je voľne šíriteľným programom, môže byť
použitý buď ako knižnica, alebo ako nezávislý program a má podporu na viacerých platformách. V tomto článku
sa pozrieme na základné použitie v termináli.<!--more-->

Na macOS môžete program nainštalovať napríklad 
pomocou správcu balíčkov [Homebrew](https://brew.sh/) nasledovne:

```
$ brew install aspell
```

Program prichádza v základnej inštalácii s limitovaným zoznamom slovníkov. Zoznam oficiálnych slovníkov môžete nájsť na
[tejto adrese](ftp://ftp.gnu.org/gnu/aspell/dict/0index.html). Požadovaný stiahnutý slovník môžete nainštalovať nasledovne:

```
$ cd /cesta/k/slovniku-aspell
$ ./configure
$ make && make install && make clean
```

Nainštalovaný slovník by sa mal následne nachádzať v zozname slovníkov. Pre kontrolu môžete použiť príkaz `dicts`:

```
$ aspell dicts
en
en-variant_0
...
sk
sk_SK
```

Súbory môžete skontrolovať pomocou príkazu:

```
$ aspell -l sk -c /cesta/k/suboru
```

Majme napríklad súbor `testovanie-aspell.txt`, ktorý chceme skontrolovať:

```
$ cat testovanie-aspell.txt
Tot je text, ktory má vľa preklepou...
```

Po spustení programu Aspell `$ aspell -l sk -c testovanie-aspell.txt` sa v prítomnosti chýb otvorí okno, kde
môžete chyby následne opraviť.

![Oprava chýb pomocou programu Aspell](/img/oprava-chyb-aspell.png)

Program na základe slovníka skontroluje preklepy a v prípade chýb navrhne opravné slová. Tie môžete vybrať
pomocou príslušných čísiel na vašej klávesnici. Slová môžete taktiež opraviť manuálne, ignorovať ich
a prípadne pridať do slovníka. Ak v súbore nie sú chyby, program je automaticky ukončený bez otvorenia okna.
