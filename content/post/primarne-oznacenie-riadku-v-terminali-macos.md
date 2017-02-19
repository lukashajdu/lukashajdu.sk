---
title: "Zmena primárneho označenia riadku v termináli macOS"
date: "2017-02-11T10:44:00Z"
tags: ["terminál", ".bash_profile"]
categories: ["Development"]
---

Po otvorení vášho terminálu uvidíte pravdepodobne niečo podobné:

```
Mac-3c07543c135a:~ lukas$
```

Tento reťazec predstavuje primárne označenie príkazového riadku a môže poskytnúť
cenné informácie. Predvolený formát tohto reťazca sa dá zmeniť editáciou 
premennej prostredia `PS1` ([Prompt Statement 1](http://tldp.org/HOWTO/Bash-Prompt-HOWTO/index.html)).
Ak sa pozrieme na hodnotu tejto premennej, tak uvidíme čo sa skrýva za hodnotami
primárneho označenia príkazového riadku:

```
Mac-3c07543c135a:~ lukas$ echo $PS1
\h:\W \u\$
```

Tieto špeciálne znaky predstavujú nasledovné:

* `\h`: názov počítača (**h**ostname)
* `\W`: základný názov aktuálneho adresára (**w**orking directory)
* `\u`: meno užívateľa (**u**ser)

Zoznam znakov môžete nájsť napríklad na
[tejto adrese](https://linuxconfig.org/bash-prompt-basics#h1-bash-ps1-prompt-variable).

Povedzme, že chceme, aby označenie príkazového riadku malo formát `<meno-uzivatela>@<nazov-pc>: <aktualny-adresar>$ `.
Toto dosiahneme nasledovne:

```
Mac-3c07543c135a:~ lukas$ export PS1='\u@\h: \W\$ '
lukas@Mac-3c07543c135a: ~$
```

Znak `~` v tomto prípade predstavuje domovský adresár užívateľa.

Aby bol tento formát perzistentný po reštartovaní terminálu, alebo otvorení nového terminálového okna, je potrebné
uchovať jeho hodnotu v súbore `~/.bash_profile`:

```
Mac-3c07543c135a:~ lukas$ echo "export PS1='\u@\h: \W\$ '" > ~/.bash_profile
Mac-3c07543c135a:~ lukas$ source ~/.bash_profile
lukas@Mac-3c07543c135a: ~$
```

`~/.bash_profile` predstavuje konfiguračný súbor pre bash shell, ktorý je vyvolaný po spustení konzoly.

Dodatočne je taktiež pomocou príkazu
[scutil](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man8/scutil.8.html)
možné zmeniť predvolený názov počítača:

```
Mac-3c07543c135a:~ lukas$ sudo scutil --set HostName hajdu
```

Vďaka čomu sa výsledne dopracujeme k označeniu:

```
lukas@hajdu: ~$
```
