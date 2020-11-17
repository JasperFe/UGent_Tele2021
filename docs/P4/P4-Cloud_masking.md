## Cloud Masking

Wolkbedekking is een grote barrière tijdens het analyseren en processen van (spectrale) satellietbeelden. Recente satellietdata komen veelal ook met automatische classificaties van de wolkbedekking, waardoor deze relatief eenvoudig uit het beeld verwijderd kunnen worden (zie ook [P3: cloud masking](../P3-image-preprocessing.md#intermezzo-cloud-masks)).  

Earth engine bevat naast deze standaard ‘cloud masks’ ook eigen algoritmes om de wolken en wolkschaduw te verwijderen uit het beeld, maar bevat ook enkele andere mogelijkheden om de aanwezigheid ervan veel mogelijk te minimaliseren zoals het toepassen van reducties op beeldcollecties.

### 1) Filteren van de ImageCollection op wolkbedekking

Een eerste optie is om een beeldcollectie te filteren (zie [voorgaand](P4-ImageVisualization.md#datacollecties-filteren-en-visualiseren)) op wolkbedekking, waardoor enkel de beelden binnen een paalde range van wolkenpercentages worden weerhouden:

```javascript

L8 = L8.filterMetadata('CLOUD_COVER', 'less_than', 30) 

```
!!! info "FilterMetaData"
    Bij de voorgande filters gebruikten we de redelijk eenvoudige functies ```.filterBounds()``` en ```.filterDate()```, twee standaardfilters om op respectievelijk locatie (van een geometrie) en datum te filter.  

    De functie ```.filterMetadata()``` wordt gebruikt om te filteren op eender welke Metadata-eigenschap dat een beeld bevat. Gebruik de **Docs** om het gebruik van deze functie verder te bekijken.


### 2) Cloud Masking optie 1
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