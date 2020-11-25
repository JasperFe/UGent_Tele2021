Onderstaande oefeningen kunnen gebruikt worden om P4 verder in te oefenen.

## Oefening 4.1 - Area 51

<p align="center">
  <img src="images/Area51.jpg">  <br>
</p> 


**Niveau**: gemakkelijk

Stappen: 

1.  Open een nieuw script

3.  Laad een Landsat-8 collectie in. Ga voor de beelden met de hoogste kwaliteit. Welke collectie kies je dan?

4.  Filter je collectie op basis van volgende gevens:  

    - **Periode**: september 2019

    - **Locatie**: Area 51. Hiervoor kun je volgende punt-locatie in je script gebruiken: 
```javascript
var Area51 = ee.Geometry.Point([-115.81441562461978, 37.2386297535804]);
```
    - **Wolkbedekking**: minder of gelijk aan 10%

5. Hoeveel beelden blijven er nog over die aan bovenstaande criteria voldoen?

6. Van de resterende beeldencollectie neem je het eerste beeld (niet gesorteerd). Bekijk de metadata. Van welke datum is dit beeld afkomstig?

7. Visualiseer het beeld als een Normale Kleuren composiet, een Valse Kleurencomposiet. Je kunt zelf je visualisatieparameters defeniëren door ze handmatig aan in te stellen.

8. Analyseer het volledige door jezelf volgende vragen te stellen:

    - Waar is er vegetatie te vinden? Waar is dit natuurlijk, waar onnatuurlijk?

    - Welke features herken je thv Area 51?

9. Het gebied geeft ook een ideale aanleiding om de 'Geology'-composiet eens uit te testen. De combinaties is als volgt: **RGB =  SWIR2-SWIR1-BLUE** . Vertaal dit zelf naar de Landsat-8 banden. (Maak gebruik van de [Landsat 8 bandentabel](https://landsat.gsfc.nasa.gov/sites/landsat/files/2013/01/BandpassesL7vL8_Jul20131.jpg)). Deze composiet maakt visuele inspectie van grote structurele eigenschappen van gesteenten (zoals plooien en breuken) gemakkelijker.

??? check "Oplossing"
    Script: [https://code.earthengine.google.com/724bbee7796ebd4ff9657dc5f0c1baaa](https://code.earthengine.google.com/724bbee7796ebd4ff9657dc5f0c1baaa)


## Oefening 4.2 - 