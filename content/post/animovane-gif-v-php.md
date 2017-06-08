---
title: "Animované GIF v PHP"
date: "2017-05-26T15:22:00Z"
tags: ["php", "ImageMagic", "GIF"]
categories: ["Development"]
---

Vytváranie obrázkov je v PHP možné vďaka mnohým knižniciam pre spracovanie
a generovanie obrázkov. V tomto článku si ukážeme ako generovať animované
GIF pomocou knižnice ImageMagic.<!--more-->

Pred prácou s ImageMagic je potrebné túto knižnicu nainštalovať. Či je knižnica
vo vašom systéme nainštalovaná môžete zistiť v termináli pomocou príkazu:

```
$ php -m | grep imagic
imagick
```

V prípade, že vám knižnica chýba, postupujte v inštalácii podľa manuálu.

## GIF obrázok bez animácie

Vytvorme si najprv jednoduchý obrázok bez animácie.
V prvom rade si vytvoríme objekt, ktorý bude uchovávať obrázok. Ako formát zvolíme `gif`:

```
$gif = new Imagick();
$gif->setFormat('gif');
```

Tento objekt nám v ďalších príkladoch bude slúžiť ako plátno, na ktoré budeme
vkladať ďalšie obrázky. Týmto spôsobom môžeme nakoniec vytvoriť animáciu.

Následne si zadefinujeme primárnu farbu, sekundárnu farbu, farbu pozadia,
priehľadnú farbu a veľkosť plátna:

```
$primaryColor = new ImagickPixel('rgb(0, 255, 0)');    // zelená
$secondaryColor = new ImagickPixel('rgb(255, 0, 0)');  // červená
$backgroundColor = new ImagickPixel('rgb(0, 0, 0)');   // čierna
$transparentColor = new ImagickPixel('rgba(0,0,0,0)'); // priehľadná
$canvasSize = 200;
```

Definícia farby pomocou RGBA je rozšírením RGB. Rozdiel je v dodatočnom parametre
pre definíciu alfa kanálu. Ten predstavuje mieru priehľadnosti pixlu. Hodnota 
0 v tomto príklade predstavuje plne priehľadný pixel.

Pre samotnú kresbu je potrebné použiť triedu `ImagickDraw`:

```
$draw = new ImagickDraw();
```

Trieda `ImagickDraw` predstavuje akúsi vrstvu, ktorú následne treba pridať
do obrázka. Vytvorený objekt nám pomocou vbudovaných metód umožňuje vytvoriť
rôzne goniometrické tvary, anotácie a pod.

Vytvorme napríklad kruh. Farbu výplne nastavíme na priehľadnú a farbu štetca
na primárnu farbu, ktorú sme si zadefinovali skorej:

```
$draw->setFillColor($transparentColor);
$draw->setStrokeColor($primaryColor);
```

Stred kruhu si nastavíme na stred plátna a veľkosť kruhu na 90% plátna:

```
$draw->circle($canvasSize / 2, $canvasSize / 2, $canvasSize / 2, 0.9 * $canvasSize);
```

Do kruhu si teraz nakreslime kolmice. Súradnice bodov x a y na kružnici
si vypočítame pomocou parametrickej rovnice kružnice. Táto rovnica nám taktiež
pomôže v neskorších krokoch vytvoriť animáciu pohybujúcej sa kolmice po obvode
kružnice. Kolmica sa bude posúvať o uhol 20° čo predstavuje asi 0.349066 radiánov.

```
$draw->setStrokeColor($secondaryColor);
for ($angle = 0; $angle < 2 * M_PI; $angle += 0.349066) {
    $x = 0.4 * $canvasSize * cos($angle) + $canvasSize / 2;
    $y = 0.4 * $canvasSize * sin($angle) + $canvasSize / 2;
    $draw->line($canvasSize / 2, $canvasSize / 2, $x, $y);
}
```

Vytvorenú kresbu pridáme do nového objektu obrázka:

```
$frame = new Imagick();
$frame->newImage($canvasSize, $canvasSize, $backgroundColor);
$frame->setImageFormat('gif');
$frame->drawImage($draw);
```

V tomto kroku je možné obrázok vykresliť, alebo uložiť pomocou metódy
`writeImage()` alebo `getImageBlob()` vyvolanej na premennej `$frame`.
Pridajme ale novovytvorený objekt s kresbou do predom vytvorenej premennej
`$gif`. Tento prístup nám v ďalších krokoch umožní pridanie viacerých
obrázkov na základné plátno.

```
$gif->addImage($frame);
```

Na záver programu nastavíme HTTP hlavičku na typ `image/gif` a vykreslime náš obrázok:

```
header('Content-Type: image/gif');
echo $gif->getImageBlob();
```

S použitím vbudovaného servera môžeme výsledok nášho programu otestovať v prehliadači
na adrese http://localhost:9999

``` 
php -S localhost:9999 basic-gif-creation.php
```

Vo vašom prehliadači by ste následne mali vidieť obrázok podobný tomuto:

![Kruh](/img/animovane-gif/kruh-1.gif)

Výsledný skript si môžete [stiahnuť tu](https://github.com/lukashajdu/lh-blog-codes/blob/master/animovane-gif-v-php/basic-gif-creation.php).

## Animovaný GIF obrázok

Poďme si teraz vytvoriť animáciu. Animáciu vytvoríme pridaním niekoľkých obrázkov
na základné plátno a pridaním oneskorenia medzi týmito obrázkami. Upravme si
teda slučku z predchádzajúceho programu nasledovne:

```
for ($angle = 0; $angle < 2 * M_PI; $angle += 0.0872665) {
    $draw = new ImagickDraw();
    $draw->setFillColor($transparentColor);
    $draw->setStrokeColor($primaryColor);
    $draw->circle($canvasSize / 2, $canvasSize / 2, $canvasSize / 2, 0.9 * $canvasSize);
    $draw->setStrokeColor($secondaryColor);

    $x = 0.4 * $canvasSize * cos($angle) + $canvasSize / 2;
    $y = 0.4 * $canvasSize * sin($angle) + $canvasSize / 2;
    $draw->line($canvasSize / 2, $canvasSize / 2, $x, $y);

    $frame = new Imagick();
    $frame->newImage($canvasSize, $canvasSize, $backgroundColor);
    $frame->setImageFormat('gif');
    $frame->drawImage($draw);
    $frame->setImageDelay(5);

    $gif->addImage($frame);
}
```

Ak si všimnete tak objekt pre vytvorenie kresby a aj obrázok boli presunuté
do tela slučky. Toto nám zabezpečí, že v každom kroku bude obrázok prekreslený
novým obrázkom. Všimnite si, že na premennej `$frame` je dodatočne vyvolaná
metóda `setImageDelay()`, ktorá nám pridá medzi vykreslením obrázkov oneskorenie.
Oneskorenie pre tuto metódu je udávané v „tiknutiach“ počas ktorých sa má tento 
obrázok zobraziť. Pre animované GIF obrázky to je 100 tiknutí za sekundu, čo
pre zobrazenie 20 obrázkov za sekundu predstavuje oneskorenie 5 tiknutí. Pre
plynulejší priebeh je krokovanie v slučke znížené na 5°. 

S týmto prístupom vygenerujeme obrázok podobný tomuto:

![Animovaný kruh](/img/animovane-gif/kruh-2.gif)

Pre zobrazenie, alebo uloženie animácie je potrebné použiť metódy
`writeImages()` a `getImagesBlob()`. V prípade použitia obdôb metód v jednotnom
čísle by bol uložený/vykreslený len posledný obrázok z animácie.

Výsledný skript si môžete [stiahnuť tu](https://github.com/lukashajdu/lh-blog-codes/blob/master/animovane-gif-v-php/basic-gif-creation.php).

V [pokračovaní](/post/3d-animovane-gif-v-php/) tohto článku si ukážeme ako možno
podobným spôsobom vytvoriť jednoduché 3D animácie.
