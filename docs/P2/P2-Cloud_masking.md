## Cloud Masking

Wolkbedekking is een grote barrière tijdens het analyseren en processen van (spectrale) satellietbeelden. Recente satellietdata komen veelal ook met automatische classificaties van de wolkbedekking, waardoor deze relatief eenvoudig uit het beeld verwijderd kunnen worden.  

Earth engine bevat naast deze standaard ‘cloud masks’ ook algoritmes om de wolken en wolkschaduw te verwijderen uit het beeld. Een keten van filter, maskeer en reduce strategiën kan de aanwezigheid van wolken minimaliseren. Volgende 3 stappen kunnen onderdeel zijn van deze keten:

  1. Filteren op maximaal wolkenpercentage
  2. Cloudmasking algoritme toepassen om wolken te verwijderen
  3. Reduceren met een mediane reducer van de resterende collectie 

### 1. Filteren van de ImageCollection op wolkbedekking

Een eerste optie is om een beeldcollectie te filteren (zie [voorgaand](P2-ImageVisualization.md#datacollecties-filteren-en-visualiseren)) op wolkbedekking, waardoor enkel de beelden binnen een paalde range van wolkenpercentages worden weerhouden:

```javascript
// Filteren van de Landsat 9 collectie tot beelden met maximaal 40% wolkbedekking
L9 = L9.filterMetadata('CLOUD_COVER', 'less_than', 40) 

```
!!! info "FilterMetaData"
    Bij de voorgande filters gebruikten we de redelijk eenvoudige functies ```.filterBounds()``` en ```.filterDate()```, twee standaardfilters om op respectievelijk locatie (van een geometrie) en datum te filter.  

    De functie ```.filterMetadata()``` wordt gebruikt om te filteren op eender welke Metadata-eigenschap dat een beeld bevat. Gebruik de **Docs** om het gebruik van deze functie verder te bekijken.

    Beschikbare metadata kan steeds in de Earth Engine Catalog worden geraadpleegd. Voor Landsat 9: [https://developers.google.com/earth-engine/datasets/catalog/LANDSAT_LC09_C02_T1_L2#image-properties](https://developers.google.com/earth-engine/datasets/catalog/LANDSAT_LC09_C02_T1_L2#image-properties).


### 2. Cloud Masks
Als 2e stap kunnen de overgebleven wolken/wolkschaduwen per beeld worden ‘geknipt’ (cloudmask) door deze pixels naar een waarde 0 om te zetten. Voor Landsat 8 en 9 wordt dit gedaan op basis van het CFMask algoritme, dat automatishe detecties van wolken, wolkschaduwen en sneeuw of ijs nastreeft. Dit resultaat zit vervat in de metadata van elk beeld als binair raster, wat gebruikt kan worden om de wolken weg te knippen.

Voor Sentinel-2 wordt een gelijkaardig algoritme toegepast.


Toepassen van de cloudmask voor Landsat 9:

```javascript
// 1. Voeg een extra filter in obv wolkbedekking ('Cloud_cover')
L9 = L9.filterMetadata('CLOUD_COVER', 'less_than', 40) 


// Definieren van CloudMask-functie (gegeven)

function maskL89sr(image) {
  // Gebaseerd op de QA-waarde, wat de uitkomst is van het FMASK algoritme
  // Develop masks for unwanted pixels (fill, cloud, cloud shadow).
  // De waarden voor de eerste 4 QA_pixelbanden moet gelijk zijn aan 0 (dus geen wolken/schaduw aanwezig)
  var qaMask = image.select('QA_PIXEL').bitwiseAnd(parseInt('11111', 2)).eq(0);
  var saturationMask = image.select('QA_RADSAT').eq(0);

  // Apply masks.
  return image.updateMask(qaMask).updateMask(saturationMask);
}

// Pas de functie over elk beeld binnen de collectie toe met de .map-functie:
var L9_masked = L9.map(maskL89sr);

// Eerste beeld uit collectie zonder Cloudmask:
Map.addLayer(L9.first(), trueColor, 'L9 - 1e beeld - No Cloudmask')

// Eerste beeld uit collectie mét Cloudmask:
Map.addLayer(L9_masked.first(), trueColor, 'L9 - 1e beeld - met Cloudmask')
```
Resulterend is een ImageCollectie met dezelfde beelden, maar waaruit de wolken gemaskeerd zijn (mask toegepast). Echter kunnen wel sommige wolkenranden nog zichtbaar zijn, die de mask-functies hebben gemist.


## OPDRACHT 3.3
Maak de L9_masked collectie aan, en neem hiervan een .median() reducer. Visualiseer dit beeld. Merk je een verbetering in vergelijking met de voorgande .median()-gereduceerde beelden, zonder de *cloudmask*?


!!! info "De .map()-functie"
    In bovenstaand voorbeeld werd de cloudmask-functie toegepast door gebruik te maken van ```.map()```. D ```.map()``` wordt steeds gebruikt om een functie (die op afzonderlijke beelden dient toegepast te worden, zoals ```maskL9sr```) toe te passen over elk beeld binnen een ImageCollection afzonderlijk. Het is als het ware een veel efficiënte manier dan de aangemaakte functie te itereren via een for-loop.

## EXTRA: Sentinel-2 Cloud Masking optie 2 met S2Cloudless

Onderstaande Sentinel-2 cloudmask-procedure is ter aanvulling van bovenstaande principes. Het betreft een relatief rest open-source cloudmask-algoritme dat gebruik maakt van een externe detector. Resultaten zijn doorgaans accurater dan de "standaard" cloud-masking functie gegeven bij de Sentinel-2 collectie. Er kan voor komende oefeningen/ het praktisch examen met beide procedures gewerkt worden, dus zoals wat in [Cloud Masking met Cloudmasks](#2-Cloud-Masking-met-Cloudmasks) werd gebruikt.

Deze Sentinel-2 cloudmasking functie steunt op een afzonderlijke collectie: [Sentinel-2 Cloud Probablity](https://developers.google.com/earth-engine/datasets/catalog/COPERNICUS_S2_CLOUD_PROBABILITY). Het wordt opgeroepen op basis van 2 functies:

```getS2_SR_CLOUD_PROBABILITY```: dat zowel de Sentinel-2 Surface Reflectance collectie oproept als de 'S2 cloud probability' collectie. Deze functie behoeft dus geen parameters en geeft een ImageCollectie als resultaat waarbij beide collecties met elkaar gemerged zijn per beeld.

```maskClouds```: dat op basis van de cloudprobability een cloudmask aanmaakt en toepast.

Om dus tot een collectie te komen waarbij de 'cloudmask' is toegepast, gebruik je dus onderstaande code:

```javascript
       var getS2_SR_CLOUD_PROBABILITY = function () {
              var innerJoined = ee.Join.inner().apply({
                  primary: ee.ImageCollection("COPERNICUS/S2_SR_HARMONIZED"),
                  secondary: ee.ImageCollection("COPERNICUS/S2_CLOUD_PROBABILITY"),
                  condition: ee.Filter.equals({
                    leftField: 'system:index',
                    rightField: 'system:index'
                  })
                });
              var mergeImageBands = function (joinResult) {
                  return ee.Image(joinResult.get('primary'))
                        .addBands(joinResult.get('secondary'));
                };
              var newCollection = innerJoined.map(mergeImageBands);
              return ee.ImageCollection(newCollection);
            };

            
   // Mask out clouds en scaling factor Sentinel-2
        var maskClouds = function(image) {
          var cloudProbabilityThreshold = 40;
          var cloudMask = image.select('probability').lt(cloudProbabilityThreshold);
          return image.updateMask(cloudMask).multiply(0.0001);
        };

var S2_coll = getS2_SR_CLOUD_PROBABILITY()
                .filterMetadata('CLOUDY_PIXEL_PERCENTAGE','less_than',50) //Voorselectie obv wolken
                .filterDate('2022-08-01','2022-10-15') 
                .map(maskClouds)
```

Vervolgens kun je met S2_coll verder werken.

