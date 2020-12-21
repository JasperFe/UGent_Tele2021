## OEF 6.1 - Classificatie van België

### Gegeven
In volgende oefening maken we een landclassificatie van België op basis van een Landsat-8 beeldcomposiet. Om de grenzen van België te bekomen, maken we gebruik van volgende dataset: [https://developers.google.com/earth-engine/datasets/catalog/USDOS_LSIB_SIMPLE_2017](https://developers.google.com/earth-engine/datasets/catalog/USDOS_LSIB_SIMPLE_2017)

Om hieruit België te filteren, maak je gebruik van onderstaande code:

```javascript
var countries = ee.FeatureCollection( 'USDOS/LSIB_SIMPLE/2017' );
var bel = countries.filterMetadata( 'country_na', 'equals', 'Belgium' );
```

### Opdracht

* Maak één wolkenvrij Landsat-8 beeld aan voor 2019. Weerhoud enkel de bruikbare banden voor classificatie: voor Landsat-8 dit zijn volgende banden: blauw, groen, rood, nir, swir1, swir2. 

* Clip je resulterende beelden op basis van de Belgische grens.

* Voer een classificatie uit met volgende klassen: *bos, water, grasland, landbouw, urban*. Maak hiervoor je eigen trainingssamples aan. Vergeet ze niet samen te voegen tot één ```FeatureCollection```.

* Kies 2 classifiers naar keuze.

* Visualiseer je resultaat naar eigen keuze. Evalueer het visueel: is je classificatie geslaagd? Welke classifier lijkt beter? 

* Bereken de *Overall Accuracy* van je classificatie. Maak gebruik van deze validatieset:  [P6_Oef1_validation.zip](https://users.ugent.be/~jfeyen/P6/P6_Oef1_validation.zip). Hierbij maak je gebruik van de eigenschap 'val', waarbij volgend schema geldt:

<center>

|  val  | Landbekkingsklasse |
|:-----:|:------------------:|
|   1   |        Bos         |
|   2   |        Water       |
|   3   |       Grasland     |
|   4   |      Landbouw      |
|   5   |       Urbaan       |


</center>

??? check "Oplossing Oef 6.1"
    Via dit scriptje: [https://code.earthengine.google.com/75c71e349c6640eee8a7a3f891c73212](https://code.earthengine.google.com/75c71e349c6640eee8a7a3f891c73212)
    Opgelet: het spreekt voor zich dat de gebruikte trainingsvectoren in dit voorbeeld zeer rudimentair zijn, met een ruwe classificatie tot gevolg.
    Daarbij werden de testpunten ook gehaald uit een bestaande landcover classificatie van België, maar met een resolutie van 100m, waardoor de kwaliteit van de testdata niet perfect is. Deze oefening is dus louter ter illustratie!


## OEF 6.2 - Eyjafjallajökull
<p align="center">
<img src="images/Oef6_2.jpg" width=500>  <br>
</p>

De gletsjer Eyjafjallajökull is een van de kleinere gletsjers op IJsland en heeft een oppervlakte van ongeveer 100 km². De Eyjafjallajökull ligt ten noorden van het plaatsje Skógar. Op de oostflank van de vulkaan, nabij de bergpas Fimmvörðuháls, vond op 20 maart 2010 nieuwe vulkanische activiteit plaats. Een tweede explosievere uitbarsting in de hoofdkrater van de Eyjafjallajökull, begon op 14 april 2010. In grote delen van Europa werd het vliegverkeer dagenlang volledig stilgelegd vanwege de aswolken die de vliegtuigen kunnen beschadigen.

### Gegeven
Maak gebruik van volgend Sentinel-2 beeld en ROI, genomen in 2019:  

```javascript
var S2 = ee.Image('COPERNICUS/S2_SR/20190810T125311_20190810T125306_T27VWL')
var ROI =  ee.Geometry.Polygon(
        [[[-19.967455239503007, 63.845568279400595],
          [-19.967455239503007, 63.398959439658746],
          [-18.824877114503007, 63.398959439658746],
          [-18.824877114503007, 63.845568279400595]]], null, false);
var S2 = ee.Image('COPERNICUS/S2_SR/20190810T125311_20190810T125306_T27VWL').clip(ROI)
```

* In deze oefening gaan we geen cloud mask toevoegen, maar de wolken en wolkenschaduwen opnemen in de classificatie. 
* Training data: Chinese experten digitaliseerden verschillende polygonen in het gebied rond de vulkaan. Hierbij werd onderscheid gemaakt in 5 klassen: 
1. Gletsjer
2. Schaduw
3. Bodem
4. Vegetatie
5. Water
6. Wolken  

De Trainingfiles werden reeds ondergebracht in een ```FeatureCollection``` en kunnen via deze link worden ingelezen:

```javascript
var traindata = ee.FeatureCollection("users/jasperfeyen/TELEDETECTIE2020/P6_oef2_training");
```

* **Referentie data**: tijdens een veldcampagne in 2019 werd het gebied rond de vulkaan intensief bemonsterd. Honderden pixels werden op het terrein bezocht en de landbedekking werd geregistreerd.  

Je kunt de shape-file hier downloaden: [P6_oef2_val.zip](https://users.ugent.be/~jfeyen/P6/P6_oef2_val.zip)

### Gevraagd
Classificeer het 2019 beeld met behulp van 2 supervised classifiers naar keuze. Voor de classificatie transformeer je de data, zodat je slechts 3 getransformeerde banden overhoudt die de meeste informatie bevatten.


??? check "Oplossing"
     Via volgend script: [https://code.earthengine.google.com/f0c9f029d8a0330a5d10266e4beae74c](https://code.earthengine.google.com/f0c9f029d8a0330a5d10266e4beae74c)
