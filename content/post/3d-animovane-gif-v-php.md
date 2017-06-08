---
title: "3D animované GIF v PHP"
date: "2017-06-01T12:01:00Z"
tags: ["php", "ImageMagic", "GIF"]
categories: ["Development"]
---

Nedávno sme si ukázali, ako pomocou knižnice ImageMagic vytvoriť v PHP animované
GIF. V tomto článku si na Lissajousových obrazcoch ukážeme, ako pomocou tejto
knižnice možno vytvoriť 3D animáciu.<!--more-->

Lissajousové obrazce sú krivky, ktoré vznikajú skladaním dvoch harmonických pohybov
vo dvoch navzájom kolmých priamkach. Možno ich popísať parametrickými rovnicami.

[![IMAGE ALT TEXT](http://img.youtube.com/vi/0L40c4fsZVk/0.jpg)](https://youtu.be/0L40c4fsZVk "Video Title")

Pre vytvorenie animovaného GIF použijeme rovnaký spôsob, ako sme si popísali v
[predchádzajúcom článku](/post/animovane-gif-v-php/), na ktorý tento článok
nadväzuje.

## Základný priebeh

Najprv si pomocou metódy `line()` nakreslime osi x a y, potom do vrstvy
vložme anotáciu pomocou metódy `annotation()`. Tá nám bude zobrazovať hodnoty ω a δ
počas priebehu.

```
$draw = new ImagickDraw();

$draw->setFillColor($secondaryColor);
$draw->line(0, $canvasSize, 2 $canvasSize, $canvasSize);
$draw->line($canvasSize, 0, $canvasSize, 2 $canvasSize);
$draw->annotation(
    10,
    (2 * $this->canvasSize) - 0.05 * 2 * $this->canvasSize,
    sprintf('ω = %.2f, δ = %.2f', $omega, $delta)
);
```

Základ by sme teda mali. Pridajme si teraz samotnú krivku. Polohu kmitajucého
bodu možno zapísať pomocou parametrickej rovnice:

```
x = A₁ sin(ω₁.t + δ)
y = A₂ sin(ω₂.t)
``` 

kde premenná A₁ predstavuje amplitúdu kmitov v smere osi x a A₂ v smere osi y. 
Premenná ω₁ označuje uhlovú frekvenciu kmitov v osi x a ω₂ v osi y. Ďalej 
premenná t označuje čas a premenná δ fázový posun. V prípade, že vzájomný pomer
uhlových frekvencií ω₁/ω₂ je racionálne číslo, krivky budú uzavreté. Ak hodnota
ω₁ bude 1, potom hodnota ω₂ bude predstavovať počet lalokov na krivke. 
Zjednodušme si teda rovnicu:

```
x = A₁ sin(t + δ)
y = A₂ sin(ω.t)
``` 

V kóde bude tento zápis vyzerať nasledovne:

``` 
$draw->setFillColor($primaryColor);
for ($angular = 0; $angular < 2 M_PI; $angular += 0.001) {
    $x = $amp1 * sin($angular + $delta);
    $y = $amp2 * sin($omega * $angular);
    $draw->point($x + $canvasSize, $y + $canvasSize);
}
```

Pre vykreslenie celej krivky musíme opísať kružnicu (2π) s dostatočne malým
krokom, aby bol priebeh spojitý. Vypočítané body vykreslíme do stredu plátna
pomocou metódy `point()`. Týmto spôsobom sa môžeme dostať k vykresleniu kriviek
podobných týmto:

| **Čiara**<br />ω = 1, δ = 0 | **Elipsa**<br />ω = 1, δ = π/4 | **Kruh**<br />ω = 1, δ = π/2 |
|----------------------|-------------------------|-----------------------|
| ![](/img/3d-animovane-gif/line.gif) | ![](/img/3d-animovane-gif/ellipse.gif) | ![](/img/3d-animovane-gif/circle.gif) |


| **2 laloky**<br />ω = 2, δ = 0 | **4 laloky**<br />ω = 4, δ = 0 | **6 lalokov**<br />ω = 6, δ = 0 |
|----------------------|-------------------------|-----------------------|
| ![](/img/3d-animovane-gif/2-lobes.gif) | ![](/img/3d-animovane-gif/4-lobes.gif) | ![](/img/3d-animovane-gif/6-lobes.gif) |

Vykreslenie priebehu máme teda hotové. Teraz vytvorme animáciu.

## Animácia priebehu

Animáciu priebehu môžeme vytvoriť zmenou fázového posunu (δ). To nám vytvorí
animáciu krivky obiehajúcej okolo osi.

``` 
$gif = new Imagick();
$gif->setFormat('gif');
for ($delta = 0; $delta < 2 M_PI; $delta += 0.5) {
    $draw = getDraw($amp1, $amp2, $omega, $delta)
    
    $frame = new Imagick();
    $frame->newImage(2 $canvasSize, 2 $canvasSize, $backgroundColor);
    $frame->setImageFormat(self::GIF);
    $frame->drawImage($draw);
    $frame->setImageDelay(1 / 5);

    $gif->addImage($frame);
}
```

Funkcia `getDraw()` nám vráti objekt podobný tomu zadefinovanému vyššie. V príklade
je kresba (premenná `$draw`) vložená do obrázka (premenná `$frame`) a tento obrázok
je následne s oneskorením pridaný na plátno `$gif`. Výstup nakoniec môžeme uložiť,
alebo vykresliť podobne ako vo vyššie spomínanom článku.

Týmto spôsobom môžeme dospieť k priebehom, ktoré budú podobné týmto:

| **Kruh**<br />ω = 1, δ = <0, 2π> | **2 laloky**<br />ω = 2, δ = δ = <0, 2π> | **4 laloky**<br />ω = 4, δ = δ = <0, 2π> |
|----------------------|-------------------------|-----------------------|
| ![](/img/3d-animovane-gif/animated-circle.gif) | ![](/img/3d-animovane-gif/animated-2-lobes.gif) | ![](/img/3d-animovane-gif/animated-4-lobes.gif) |

## Trieda `LissajousCurveDrawing`

Pre jednoduchšie experimentovanie som vytvoril triedu `LissajousCurveDrawing`
s funkcionalitou podobnej tej, ktorá bola použitá v tomto článku. Celý kód aj s príkladmi
si môžete pozrieť a stiahnuť tu: [lukashajdu/lissajous-curve-drawing](https://github.com/lukashajdu/lissajous-curve-drawing).

Farby priebehu a veľkosť plátna pre triedu zadefinujete pomocou argumentov
konštruktora:

```
$lissajous = new LissajousCurveDrawing(
    new ImagickPixel('rgb(0, 255, 0)'),
    new ImagickPixel('rgb(127, 127, 127)'),
    new ImagickPixel('rgb(0, 0, 0)'),
    100
);
```
 
 Vykreslenie priebehu môžete vytvoriť pomocou metódy `getCurve()`:
 
```
header('Content-Type: image/gif');
echo $lissajous->getCurve(80, 80, 1, 0);
```
 
 Vykreslenie animácie zas pomocou metódy `getDeltaTransformation()`:

```
header('Content-Type: image/gif');
echo $lissajous->getDeltaTransformation(80, 80, 1);
```

## Zhodnotenie

Knižnica ImageMagic mám ponúka bohatú funkcionalitu pre generáciu a úpravu obrázkov.
Jedna z týchto funkcionalít ponúka možnosť generácie animovaných obrázkov typu GIF. 
V tomto a predchádzajúcom článku sme si ukázali, ako je takéto obrázky možné generovať.
