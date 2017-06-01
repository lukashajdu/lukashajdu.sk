---
title: "3D animované GIF v PHP"
date: "2017-06-01T12:01:00Z"
tags: ["php", "ImageMagic", "3D"]
categories: ["Development"]
---

Nedávno sme si ukázali, ako pomocou knižnice ImageMagic vytvoriť v PHP animované
GIF. V tomto článku si na Lissajousových krivkách ukážeme, ako pomocou tejto
knižnice môžeme vytvoriť 3D animácie.<!--more-->

Lissajousové krivky ste si mohli všimnúť na osciloskopoch v sci-fi filmoch zo
60. - 70. rokov, kde simulovali high-tech zariadenia, alebo napríklad na
abstraktných obrazoch Maxa Ernsta.

Lissajousové krivky patria do rodiny kriviek popísaných parametrickými rovnicami,
ktoré popisujú komplexný harmonický pohyb. Lissajous vo svojich pokusoch používal
zložené kyvadlo a piesok, ale my si krivky vygenerujeme pomocou skriptu v PHP.

[![IMAGE ALT TEXT](http://img.youtube.com/vi/0L40c4fsZVk/0.jpg)](https://youtu.be/0L40c4fsZVk "Video Title")

Pre vytvorenie animovaného GIF použijeme rovnaký spôsob, ako sme si popísali v
[predchádzajúcom článku](/post/animovane-gif-v-php/), na ktorý tento článok
nadväzuje. V tomto prípade však budeme trochu experimentovať.

Najprv si nakreslíme os x a y pomocou metódy `line()` a potom do vrstvy vložíme
anotáciu pomocou metódy `annotation()`, ktorá nám bude zobrazovať hodnoty ω a δ
počas priebehu.

```
$draw = new ImagickDraw();

$draw->setFillColor($secondaryColor);
$draw->line(0, $canvasSize, 2 $canvasSize, $canvasSize);
$draw->line($canvasSize, 0, $canvasSize, 2 $canvasSize);
$draw->annotation(
    10,
    (2 $canvasSize) - 0.05 2 $canvasSize,
    sprintf('ω = %.2f, δ = %.2f', $omega, $delta)
);
```

Základ by sme teda mali. Pridajme si teraz samotnú krivku. Krivka je definovaná
parametrickými rovnicami:

```
x(t) = A sin(ω.t + δ)
y(t) = B sin(t)
``` 

kde hodnoty A a B predstavujú amplitúdu, hodnota ω počet lalokov na krivke
a hodnota δ fázový posun. V kóde to bude vyzerať asi takto:

``` 
$draw->setFillColor($primaryColor);
for ($angular = 0; $angular < 2 M_PI; $angular += 0.001) {
    $x = $ampA sin($omega $angular + $delta);
    $y = $ampB sin($angular);
    $draw->point($x + $canvasSize, $y + $canvasSize);
}
```

Pre vykreslenie celej krivky musíme opísať kružnicu (2π) s dostatočne malým
krokom, aby bol priebeh spojitý. Vypočítané body vykreslíme do stredu plátna
pomocou metódy `point()`. Týmto spôsobom sa možeme dostať k vykresleniu kriviek.

Toto sú príklady pre niektoré špeciálne prípady:

| Čiara (ω = 1, δ = 0) | Elipsa (ω = 1, δ = π/4) | Kruh (ω = 1, δ = π/2) |
|----------------------|-------------------------|-----------------------|
| ![](/img/3d-animovane-gif/line.gif) | ![](/img/3d-animovane-gif/ellipse.gif) | ![](/img/3d-animovane-gif/circle.gif) |


| 2 laloky (ω = 2, δ = 0) | 4 laloky (ω = 4, δ = 0) | 6 lalokov (ω = 6, δ = 0) |
|----------------------|-------------------------|-----------------------|
| ![](/img/3d-animovane-gif/2-lobes.gif) | ![](/img/3d-animovane-gif/4-lobes.gif) | ![](/img/3d-animovane-gif/6-lobes.gif) |

Vykreslenie priebehu máme teda hotové. Vytvorme teraz animáciu. Animáciu priebehu
môžeme vytvoríť zmenou fázy (δ). To nám vytvorí animáciu krivky obiehajúcej okolo osi.

``` 
$gif = new Imagick();
$gif->setFormat('gif');
for ($delta = 0; $delta < 2 M_PI; $delta += 0.5) {
    $draw = getDraw($ampA, $ampB, $omega, $delta)
    
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
je následne s oneskorením pridaný na plátno `$gif`. Výstup nakoniec môžeme spracovať
podobne ako vo vyššie spomínanom článku.

Týmto spôsobom môžeme dospieť k priebehom, ktoré budú podobné týmto:

| Kruh (ω = 1, δ = <0, 2π>) | 2 laloky (ω = 2, δ = δ = <0, 2π>) | 4 laloky (ω = 4, δ = δ = <0, 2π>) |
|----------------------|-------------------------|-----------------------|
| ![](/img/3d-animovane-gif/animated-circle.gif) | ![](/img/3d-animovane-gif/animated-2-lobes.gif) | ![](/img/3d-animovane-gif/animated-4-lobes.gif) |

Pre jednoduchšie experimentovanie som vytvoril triedu `LissajousCurveDrawing`
s funkcionalitou podobnej tej popísanej vyšsie. Celý kód aj s príkladmi si môžete
pozrieť a stiahnuť tu: [lukashajdu/lissajous-curve-drawing](https://github.com/lukashajdu/lissajous-curve-drawing).

Farby priebehu a veľkosť plátna následne zadefunujete pomocou argumentov
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
