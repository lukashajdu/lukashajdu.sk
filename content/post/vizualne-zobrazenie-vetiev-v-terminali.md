---
title: "Vizuálne zobrazenie vetiev v termináli"
date: "2017-03-14T09:50:42Z"
tags: ["git", "terminál"]
categories: ["Development"]
---

Ak ste používateľom verziovacieho systému [Git](https://git-scm.com/) a pre
interakciu používate terminál, potom určite oceníte vizuálne zobrazenie vetiev bez
dodatočnej inštalácie programov na to určených (ako napr. [gitk](https://git-scm.com/docs/gitk)).
V tomto článku sa pozrieme na to, ako vykresliť históriu s údajmi podľa vašich
želaní pomocou vstavaných príkazov v systéme Git.<!--more-->

Ako používateľ terminálu, určite poznáte príkaz na zobrazenie histórie
repozitára `git log`, ktorý pre zmeny v histórii vygeneruje zoznam podobný tomuto:

```
$ git log
commit 27dbfda18e44dcb9315b786e36d13dd807fc8e53
Merge: 8141d17 efa74c5
Author: Nicolas Tester <nicolas.tester@example.com>
Date:   Tue Mar 14 08:58:07 2017 +0100

    Merge branch '3.2'

    * 3.2:
      [FrameworkBundle] Fix cleaning of test dirs

commit efa74c5eab2e285d81928790bc2e48078861ca39
Merge: 5676526 6b4cfd6
Author: Nicolas Tester <nicolas.tester@example.com>
Date:   Tue Mar 14 08:56:22 2017 +0100

    Merge branch '2.8' into 3.2

    * 2.8:
      [FrameworkBundle] Fix cleaning of test dirs

...
```

Zoznam commit-ov obsahuje detaily, ktoré nás obyčajne nezaujímajú. Čo asi
väčšinu vývojárov zaujíma je predmet commit-u, autor commit-u, názov vetvy
a prípadne dátum.

Výstup z predchádzajúceho príkazu `git log` môžeme zjednodušiť pomocou prepínača
`--pretty=oneline`, prípadne jeho modifikovanou skrátenou verziou `--oneline`.
Prepínač `--pretty` podporuje okrem hodnoty `oneline` aj iné formáty, a taktiež 
umožňuje použitie vlastných formátov. V prípade použitia hodnoty `oneline` bude
výstup vyzerať nasledovne:
 
```
$ git log --oneline
27dbfda Merge branch '3.2'
efa74c5 Merge branch '2.8' into 3.2
6b4cfd6 Merge branch '2.7' into 2.8
ac89b1c minor #21972 [FrameworkBundle] Fix cleaning of test dirs
...
```

Zoznam commit-ov je zjednodušený a usporiadaný chronologicky. Čo však z výstupu
stále nie je zrejmé, je umiestnenie commit-u vo vetve. Pre vykreslenie textovej
grafickej reprezentácie histórie commit-ov môžeme spolu s `--oneline` použiť
prepínač `--graph`. Výstup bude následne vyzerať podobne:
 
```
$ git log --oneline --graph
*   27dbfda Merge branch '3.2'
|\
| *   efa74c5 Merge branch '2.8' into 3.2
| |\
| | *   6b4cfd6 Merge branch '2.7' into 2.8
| | |\
| | | *   ac89b1c minor #21972 [FrameworkBundle] Fix cleaning of test dirs
| | | |\
| | | | * 4842c86 [FrameworkBundle] Fix cleaning of test dirs
* | | | |   8141d17 bug #21983 [HttpKernel] Resolve real class when failing on proxies
|\ \ \ \ \
| * | | | | e34e29a [HttpKernel] Resolve real class when failing on proxies
* | | | | |   f2ae2ed bug #21986 Stop relying on the $mode argument
|\ \ \ \ \ \
...
```

Všimnite si, že predchádzajúci výstup neobsahuje informácie ako názov vetvy,
dátum, či meno autora. Ak tieto informácie chceme mať vo svojom výstupe, potom
budeme musieť upraviť hodnotu prepínača `--pretty`. Táto hodnota môže
pozostávať z prednastavených zástupných znakov a vlastných znakov. Viac informácií
o formátoch a zástupných znakoch môžete nájsť v dokumentácií príkazu
[git-log](https://git-scm.com/docs/git-log).

Hodnota pre formátovaný výstup môže vyzerať napríklad takto:

```
format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset'
```

Pre lepšie porozumenie si teraz rozoberme jednotlivé parametre v reťazci.
V prvej časti môžeme vidieť `%Cred%h%Creset`. Zástupné znaky `%Cred` a `%Creset`
špecifikujú farbu pre zástupný znak `%h`, ktorý je skrátenou verziou
kryptografického súčtu. V ďalšej časti `%Cyellow%d%Creset`, zástupné znaky
`%Cyellow` a `%Creset` opäť špecifikujú farbu pre `%d`,  čo je zástupný znak
pre meno referencie. Nasleduje `%s`, čo je zástupný znak pre predmet commit-u.
`%Cgreen(%cr)` nám určuje farbu dátumu commit-u `%cr`, a na koniec
`%C(bold blue)<%an>%Creset` určuje farbu pre meno autora zmeny `%an`.

Výstup pre formát definovaný vyššie bude vyzerať nasledovne:

![Vizuálne zobrazenie git vetiev](/img/git-lg-alias.jpg)

Toto je zápis úplného príkazu s formátovaným výstupom:

```
git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset'
```

Pre zjednodušenie použitia príkazu môžete v globálnom súbore pre konfiguráciu 
`~/.gitconfig` pomocou príkazu `git config` vytvoriť alias:

```
git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset'"
```

Po otvorení súboru by ste vytvorený alias následne mali vidieť:

```
$ cat ~/.gitconfig
[alias]
  lg = log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset'
...
```

Vytvorený alias môžete nakoniec použiť kdekoľvek pomocou príkazu `git lg`. 

Ako vidíte informácie vo vizuálnom zobrazené môžete nakonfigurovať podľa vašich
požiadaviek. Pre ďalšiu inšpiráciu si môžete prečítať niektoré z odpovedí na
Stack Overflow:

* [Visualizing branch topology in git](http://stackoverflow.com/questions/1838873/visualizing-branch-topology-in-git/34467298#34467298)
* [Pretty git branch graphs](http://stackoverflow.com/questions/1057564/pretty-git-branch-graphs/9074343)

Bližšie informácie o konfigurácii a úplný zoznam konfiguračných parametrov
nájdete na stránke dokumentácie príkazu [git-log](https://git-scm.com/docs/git-log).
