## Cloud Masking

Wolkbedekking is een grote barrière tijdens het analyseren en processen van (spectrale) satellietbeelden. Recente satellietdata komen veelal ook met automatische classificaties van de wolkbedekking, waardoor deze relatief eenvoudig uit het beeld verwijderd kunnen worden (zie ook [P3: cloud masking](../P2/P2-image-preprocessing.md#intermezzo-cloud-masks)).  

Earth engine bevat naast deze standaard ‘cloud masks’ ook eigen algoritmes om de wolken en wolkschaduw te verwijderen uit het beeld, maar bevat ook enkele andere mogelijkheden om de aanwezigheid ervan veel mogelijk te minimaliseren zoals het toepassen van reducties op beeldcollecties.

### 1. Filteren van de ImageCollection op wolkbedekking

Een eerste optie is om een beeldcollectie te filteren (zie [voorgaand](P4-ImageVisualization.md#datacollecties-filteren-en-visualiseren)) op wolkbedekking, waardoor enkel de beelden binnen een paalde range van wolkenpercentages worden weerhouden:

```javascript

L8 = L8.filterMetadata('CLOUD_COVER', 'less_than', 30) 

```
!!! info "FilterMetaData"
    Bij de voorgande filters gebruikten we de redelijk eenvoudige functies ```.filterBounds()``` en ```.filterDate()```, twee standaardfilters om op respectievelijk locatie (van een geometrie) en datum te filter.  

    De functie ```.filterMetadata()``` wordt gebruikt om te filteren op eender welke Metadata-eigenschap dat een beeld bevat. Gebruik de **Docs** om het gebruik van deze functie verder te bekijken.


### 2. Cloud Masking met Cloudmasks
Als 2e stap kunnen de overgebleven wolken/wolkschaduwen per beeld worden ‘geknipt’ (cloudmask) door deze pixels naar een waarde 0 om te zetten. Een standaard algoritme is bij de meeste beelden reeds gegeven als voorbeeld onderaan in de catalogus:  

> ** Voor Landsat-8 **: [https://developers.google.com/earth-engine/datasets/catalog/LANDSAT_LC08_C01_T1_SR](https://developers.google.com/earth-engine/datasets/catalog/LANDSAT_LC08_C01_T1_SR)    

> ** Voor Sentinel 2 **: [https://developers.google.com/earth-engine/datasets/catalog/COPERNICUS_S2_SR](https://developers.google.com/earth-engine/datasets/catalog/COPERNICUS_S2_SR) ). 


Voor Landsat 8 wordt dit bijgevolg:

```javascript
L8 = L8.filterMetadata('CLOUD_COVER', 'less_than', 30) 

function maskL8sr(image) {
  // Bits 3 and 5 are cloud shadow and cloud, respectively.
  var cloudShadowBitMask = (1 << 3);
  var cloudsBitMask = (1 << 5);
  // Get the pixel QA band.
  var qa = image.select('pixel_qa');
  // Both flags should be set to zero, indicating clear conditions.
  var mask = qa.bitwiseAnd(cloudShadowBitMask).eq(0)
                 .and(qa.bitwiseAnd(cloudsBitMask).eq(0));
  return image.updateMask(mask);
}

// Pas de functie over elk beeld binnen de collectie toe:
var L8_masked = L8.map(maskL8sr);
```
Resulterend is een ImageCollectie met dezelfde beelden, maar waaruit de wolken gemaskeerd zijn (mask toegepast). Echter kunnen wel sommige wolkenranden nog zichtbaar zijn, die de mask-functies hebben gemist.


## OPDRACHT
Maak de L8_masked collectie aan, en neem hiervan een .median() reducer. Visualiseer dit beeld. Merk je een verbetering in vergelijking met de voorgande .median()-gereduceerde beelden, zonder de *cloudmask*?


!!! info "De .map()-functie"
    In bovenstaand voorbeeld werd de cloudmask-functie toegepast door gebruik te maken van ```.map()```. D ```.map()``` wordt steeds gebruikt om een functie (die op afzonderlijke beelden dient toegepast te worden, zoals ```maskL8sr```) toe te passen over elk beeld binnen een ImageCollection afzonderlijk. Het is als het ware een veel efficiënte manier dan de aangemaakte functie te itereren via een for-loop.

## EXTRA: Sentinel-2 Cloud Masking optie 2 met S2Cloudless

Onderstaande Sentinel-2 cloudmask-procedure is ter aanvulling van bovenstaande principes. Het betreft een relatief nieuw cloudmask-algoritme dat gebruik maakt van een externe detector. Resultaten zijn doorgaan iets beter dan de "standaard" cloud-masking functie gegeven bij de Sentinel-2 collectie. Er kan voor komende oefeningen/ het praktisch examen met van beide procedures gebruikt worden, dus zoals wat in [Cloud Masking met Cloudmasks](#2-Cloud-Masking-met-Cloudmasks) werd gebruikt.

Deze Sentinel-2 cloudmasking functie steunt op een afzonderlijke collectie: [Sentinel-2 Cloud Probablity](https://developers.google.com/earth-engine/datasets/catalog/COPERNICUS_S2_CLOUD_PROBABILITY). Het wordt opgeroepen op basis van 2 functies:

```getS2_SR_CLOUD_PROBABILITY```: dat zowel de Sentinel-2 Surface Reflectance collectie oproept als de 'S2 cloud probability' collectie. Deze functie behoeft dus geen parameters en geeft een ImageCollectie als resultaat waarbij beide collecties met elkaar gemerged zijn per beeld.

```maskClouds```: dat op basis van de cloudprobability een cloudmask aanmaakt en toepast.

Om dus tot een collectie te komen waarbij de 'cloudmask' is toegepast, gebruik je dus onderstaande code:

```javascript
       var getS2_SR_CLOUD_PROBABILITY = function () {
              var innerJoined = ee.Join.inner().apply({
                  primary: ee.ImageCollection("COPERNICUS/S2_SR"),
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
            
   // Mask out clouds
        var maskClouds = function(image) {
          var cloudProbabilityThreshold = 40;
          var cloudMask = image.select('probability').lt(cloudProbabilityThreshold);
          return image.updateMask(cloudMask);
        };

var S2_coll = getS2_SR_CLOUD_PROBABILITY()
                .filterMetadata('CLOUDY_PIXEL_PERCENTAGE','less_than',50) //Voorselectie obv wolken
                .filterDate('2019-08-01','2019-10-30') 
                .map(maskClouds)
```



