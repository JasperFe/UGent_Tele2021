## Textuuranalyse

Beeldtextuur kan worden gedefinieerd als *‘de set van metrieken die berekend worden om bepaalde waargenomen textuureigenschappen van het beeld te kwantificeren’*. Het geeft informatie over de spatiale verspreiding van kleuren of intensiteiten binnen het beeld of een bepaalde regio.
De spectrale reflectie van wolken en ijs kan bijvoorbeeld zeer gelijkend zijn, maar de textuur zeer verschillend. Textuur toevoegen als een extra input-band kan dus helpen bij het optimaliseren van een beeldclassificatie.

Textuur wordt steeds berekend binnen een bepaalde zone, ook wel *neighborhood* genoemd.

In volgende paragraaf, zullen we enkele textuur indices berekenen. We keren hiervoor even terug naar ons satellietbeeld uit Belém, gezien dit beeld geschikt is ter illustratie van textuur. Hier nog even de code om tot het beeld te komen:

```javascript  
//Inladen van het gekende Belém Sentinel-2 beeld
var S2_Belem = ee.Image('COPERNICUS/S2_SR/20200808T134219_20200808T134214_T22MGD')

// Zoom in de Map-view in naar het beeld, met Zoom-factor 9
Map.centerObject(S2_Belem, 9);
Map.addLayer(S2_Belem, {min: 0, max: 3000, bands: ['B4', 'B3', 'B2']});
```


### Standaard deviatie

De **Standaard Deviatie** (SD) berekent de spreiding van de pixelwaarde-distributie binnen de neighborhood.

```javascript
//1) Standaard deviatie
// Compute standard deviation (SD) as texture van B8
var stdev = S2_Belem.select('B8').reduceNeighborhood({
  reducer: ee.Reducer.stdDev(),
  kernel: ee.Kernel.circle(7),
});
Map.addLayer(stdev, {min: 0, max: 2000}, 'SD of NDVI');

```

### Grey Level Co-occurence matrix

Voor het beschrijven van de textuur wordt gebruik gemaakt van de grey level co-occurence
matrix (GLCM) van Haralick et al. (1973). Het is een matrix dat weergeeft hoeveel verschillende combinaties van pixelgrijswaarden voorkomen in een bepaalde *neighborhood*. Aan de hand van de GLCM kunnen vervolgens verschillende textuurattributen berekend worden om de textuur te beschrijven.


### Entropie
Een eerste voorbeeld is de entropie. De entropie van een beeld vertaalt de *randomness* van de intensiteit naar een index. Het kan worden gezien als een maat voor ‘pixeldiversiteit’ binnen een bepaalde zone (*neighborhood*).  In dit voorbeeld wordt een kernel aangemaakt met radius 7m, waardoor de textuurberekening dus steeds binnen een radius van 7m wordt berekend. Gezien een entropy-berekening enkel kan worden uitgevoerd op discrete waarden, dienen we het pixeltype eerst om te zetten naar een integer.

```javascript
// Compute the gray-level co-occurrence matrix (GLCM), get contrast.
var glcm = S2_Belem.select('B8').glcmTexture({size: 4});
var contrast = glcm.select('B8_contrast');

//Mappen van contrast: speel met de min, max waarden (via stretching)
Map.addLayer(contrast,
             {},
             'contrast');
var variance = glcm.select('B8_var')
Map.addLayer(variance,
             {},
             'variance');
var ent = glcm.select('B8_ent');
Map.addLayer(ent,
             {},
             'Entropy');
```

!!! warning "GLCM-textuur in Earth Engine"
    Je merkt wellicht dat de textuurafbeelding van verschillende textuurmaten uit de GLCM varieert naarmate je in- en uitzoomt. Dit is ligt aan het feit dat Earth Engine dit steeds per 'view window' uitrekend, waardoor de resultaten zo verschillend lijken. Echter, als je het beeld exporteert uit Earth Engine en bijvoorbeeld in SNAP opent, bekom je wel een visueel begrijpbaar resultaat.
   
   De berekende banden kunnen in Earth Engine echter wel verder gebruikt worden voor classificatie.


### EXTRA: Export-script

Hieronder kun je een voorbeeldscriptje vinden om een raster naar je Google Drive te exporteren. Na een 10-tal minuten kun je dit terug vinden op je persoonlijke Google Drive en downloaden naar je desktop.

```javascript
Export.image.toDrive({
      image: entropy,
      description: 'B8_Entropy',
      scale: 10, //RESOLUTIE
      folder: 'Sentinel_2_export',
       maxPixels: 1e12 //Vergroot de exportcapaciteit
     });



