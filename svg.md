# SVG

## `href` vs `xlink:href`
Mit der zweiten Version des SVG-Standards wird das bisher verwendete `xlink:href`-Attribut als veraltete angesehen. Stattdessen sollte `href`, das jetzt direkt Teil von SVG ist, verwendet werden. Zum jetzigen Zeitpunkt wird `href` jedoch noch nicht vno Safari (unter macOS und iOS) unterstützt.

## Relative Positionierung und Dimensionen

Grundsätzlich gibt es kein Konzept relativer Positionierung. Relative Positionen können aber mittels Verschiebung des Koordinatensystems erreicht werden:

```html
<svg>
  <g x="100" y="200" transform="translate(100,200)">
    <!-- Text an der absoluten Position (150,250). -->
    <text x="50" y="50">Lorem Ipsum</text>
  </g>
</svg>
  ```

Alternativ können verschachtelte SVG-Elemente verwendet werden, damit sind dann sämtliche Positions- und Dimensionsangaben der Kindelemente relativ zum SVG-Element.

```html
<svg>
  <svg x="100" y="200">
    <!-- Das Rechteck ist an der absoluten Position (150,250)
         und 50 x 100 Pixel groß
     -->
    <rect x="50" y="50" width="50%" height="50%"></rect>
  </svg>
</svg>
```
