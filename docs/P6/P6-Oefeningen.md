## OEF 6.1 - Classificatie van België

## Gegeven
In volgende oefening maken we een landclassificatie van België op basis van een Landsat-8 beeldcomposiet. Om de grenzen van België te bekomen, maken we gebruik van volgende dataset: [https://developers.google.com/earth-engine/datasets/catalog/USDOS_LSIB_SIMPLE_2017](https://developers.google.com/earth-engine/datasets/catalog/USDOS_LSIB_SIMPLE_2017)

Om hieruit België te filteren, maak je gebruik van onderstaande code:

```javascript
var countries = ee.FeatureCollection( 'USDOS/LSIB_SIMPLE/2017' );
var bel = countries.filterMetadata( 'country_na', 'equals', 'Belgium' );
```

## Opdracht

* Maak één wolkenvrij Landsat-8 beeld aan voor 2019. Weerhoud enkel de bruikbare banden voor classificatie: voor Landsat-8 dit zijn volgende banden: blauw, groen, rood, nir, swir1, swir2. 

* Clip je resulterende beelden op basis van de Belgische grens.

* Voer een classificatie uit met volgende klassen: *bos, water, grasland, landbouw, urban, bodem*. Maak hiervoor je eigen trainingssamples aan. Vergeet ze niet samen te voegen tot één ```FeatureCollection```.

* Kies 2 classifiers naar keuze.

* Visualiseer je resultaat naar eigen keuze. Evalueer het visueel: is je classificatie geslaagd? Welke classifier lijkt beter? 

* Bereken de *Overall Accuracy* van je classificatie.