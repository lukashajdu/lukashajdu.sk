---
title: "Odstránenie zlúčených vetiev v Git"
date: "2017-04-06T21:10:00Z"
tags: ["git", "terminál"]
categories: ["Development"]
---

Pri vývoji nových funkcionalít, alebo pri odstraňovaní chýb v aplikácii je
dobrým zvykom používať samostatné vetvu. S týmto prístupom sa vám po čase
v zozname vetiev určite nakopia vetvy, ktoré už boli zlúčené, ale referencia
na ne ostala v zozname. V tomto článku si ukážeme, ako tieto vetvy z lokálneho
systému jednoducho odstrániť.<!--more-->

## TL;DR;

Zlúčené vetvy môžete odstrániť pomocou príkazu:
```
$ git branch --merged | egrep -v "(^\*|master|develop)" | xargs git branch -d
```

## Rozbor príkazu

Samostatné vetvy majú niekoľko výhod. Izolujú zmeny v kóde medzi programátormi,
zapuzdrujú zmeny potrebné pre danú funkcionalitu, alebo opravu chyby. Vetvy,
v ktorých je málo zmien, sú taktiež jednoduchšie na preskúmanie pri dopyte pre
zlúčenie do hlavnej vetvy atď.


Zoznam všetkých lokálnych vetiev môžete vo vašom termináli zobraziť pomocou
príkazu: 

```
$ git branch
  XYZ-123-feature-123
  XYZ-124-feature-124
  XYZ-125-feature-125
  XYZ-128-feature-128
  XYZ-143-feature-143
* develop
  master
```

Názov vetvy označený hviezdičkou (`*`) predstavuje vetvu, na ktorej sa 
momentálne nachádzate. Pre zobrazenie zlúčených vetiev je potrebné použiť
prepínač `--merged`:

```
$ git branch --merged
  XYZ-123-feature-123
  XYZ-124-feature-124
  XYZ-125-feature-125
* develop
  master
```

Majte však na pamäti, že prepínač [merged](https://git-scm.com/docs/git-branch#git-branch---mergedltcommitgt)
bez definície konkrétnej vetvy zobrazuje zlúčené vetvy s ohľadom na aktuálnu
vetvu (hlavu - _HEAD_). Preto je pred vyčistením zoznamu vetiev potrebné prepnúť
hlavu na vašu hlavnú vetvu repozitára (prípadne použiť v príkaze konkrétnu vetvu).

Máme teda zoznam zlúčených vetiev. V ďalšom kroku vylúčime zo zoznamu naše
hlavné vetvy _master_ a _develop_ a aktuálnu pracovnú vetvu, ktorú i tak nie je
možné odstrániť. Pre tento účel nám poslúži zreťazenie pomocou `|` a niektorý
z programov [grep](http://man7.org/linux/man-pages/man1/grep.1.html),
[egrep](http://man7.org/linux/man-pages/man1/grep.1.html), alebo podobný.
Pre náš príklad použijeme program _egrep_ s regulárnym výrazom
`(^\*|master|develop)` a prepínačom `-v`, ktorý zabezpečí invertovanú zhodu
pre výraz:

```
$ git branch --merged | egrep -v "(^\*|master|develop)"
  XYZ-123-feature-123
  XYZ-124-feature-124
  XYZ-125-feature-125
```

V prípade, že vaše hlavné vetvy nie sú _master_ a _develop_, upravte regulárny
výraz podľa vašej potreby.

Ako posledný krok nám ostáva samotné zmazanie vetiev. Pre tento účel nám poslúži
zreťazenie výstupu z predchádzajúceho príkazu a použitie programu
[xargs](http://man7.org/linux/man-pages/man1/xargs.1.html) s príkazom Git
pre zmazanie vetvy:

```
$ git branch --merged | egrep -v "(^\*|master|develop)" | xargs git branch -d
Deleted branch XYZ-123-feature-123 (was 813dae4).
Deleted branch XYZ-124-feature-124 (was 87247a0).
Deleted branch XYZ-125-feature-125 (was 83347ea).
```

Ak následne necháme vypísať zoznam vetiev, zlúčené vetvy budú odstránené:

```
$ git branch
  XYZ-128-feature-128
  XYZ-143-feature-143
* develop
  master
```

Príkaz pre odstránenie zlúčených vetiev môžete taktiež pridať do vášho
konfiguračného súboru ako alias. Alias môžete vytvoriť pomocou nasledujúceho
príkazu:

```
git config --global alias.cleanup '!git branch --merged | egrep -v "(^\*|master|develop)" | xargs git branch -d'
```

Pomocou príkazu `git config -e --global` môžete prípadne otvoriť globálny
konfiguračný súbor v móde pre editáciu a sekciu `[alias]` editovať manuálne: 

```
[alias]
    cleanup = !git branch --merged | egrep -v \"(^\\*|master|develop)\" | xargs git branch -d
```

Vytvorený alias bude následne dostupný ako `git cleanup`.

Viac informácií o tejto problematike sa môžete dozvedieť na Stack Overflow
v otázke [How can I delete all git branches which have been merged?](http://stackoverflow.com/questions/6127328/how-can-i-delete-all-git-branches-which-have-been-merged).
