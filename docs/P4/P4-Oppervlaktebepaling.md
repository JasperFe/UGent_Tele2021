## Oppervlaktebepaling
Nu de classificatie is uitgevoerd, kunnen we per finale landbedekkingsklasse ook de oppervlakte bepalen. Gezien het bekomen classificatieresultaat een rasterbeeld met pixels is, zal de oppervlakte een klasse gelijk zijn aan de som van de oppervlakte van elke pixel binnen de klasse.

  - ```.area()```: wordt gebruikt om de oppervlakte van ```Features``` te bepalen.  

  - ```ee.Image.pixelArea()```: wordt gebruikt om de oppervlakte van pixels binnen een rasterbeeld te bepalen. Het resultaat wordt weergegeven in m<sup>2</sup>.  


In Google Earth Engine zijn hier verschillende methodes voor de oppervlakteberekening. 

  - **Stap 1** - afzonderen van de klasse met een .eq()-functie. Het resultaat is een binair beeld, waarbij de '1'waarde de geselecteerde klasse(n) voorsteld, waarde '0' de overige pixels.  
```javascript
// Mangroveklasse afzonderen uit het geclassificeerd beeld
var Mangrove_class = classified_RF.eq(1)

// Visualiseren van het beeld
Map.addLayer(Mangrove_class, {min:0, max:1, palette: ['white', 'green']}, 'Mangrove Cover')
```

  - **Stap 2** - pixelwaarde vervangen door de oppervlakte van de pixel.  
```javascript
//Pixelwaarden (=1) vermenigvuldigen met de pixeloppervlakte.
var areaImage = Mangrove_class.multiply(ee.Image.pixelArea())
```

  - **Stap 3** - Nu elke pixel van de Mangrove-klasse een waarde heeft gelijk aan de oppervlakte, kunnen we de som nemen van alle pixels in de regio om de totale oppervlakte te verkrijgen.Dit doen we met een Reducer (zie ook P4)  
  
```javascript
var area_Mangrove = areaImage.reduceRegion({
  reducer: ee.Reducer.sum(),
  geometry: ROI,
  scale: 10, //minimumpixelgrootte zorgt voor meest accurate waarde.
  maxPixels: 1e10 //Vergroten rekencapaciteit
  })

```

  - **Stap 4** - Het resultaat is een dictionary, waarbij de enige 'waarde' de oppervlakte is (check dit door het resultaar naar de Console te prunten).Print nu de oppervlakte naar de console. Deze is momenteel uitgedrukt in m<sup>2</sup>

```javascript
var MangroveAreaHa = ee.Number(area_Mangrove.get('classification')).divide(1e4).round()
print('Oppervlakte Mangrove: ', MangroveAreaHa)

```

### Opdracht
Bereken en print ook de oppervlakte van de klasse 'Ander bos' binnen het studiegebied.

## EXTRA: Oppervlaktehistogram
Hierboven werd een standaardmethode behandeld om de oppervlakte per klasse in een geclassificeerd beeld afzonderlijk te berekenen. Met onderstaande code kun je meteen de oppervlakte per klasse gezamenlijk berekenen en via de ```ui.Chart.image.byClass()``` functie een histogram visualiseren die de oppervlaktes weergeeft.

```javascript
var areaImageHa = ee.Image.pixelArea().divide(1e4).addBands(classified_RF);

// Berekenen oppervlakte per klasse met een reduceRegion()-functie
// We voeren m.a.w. een reductie uit, waarbij ons classificatieresultaat als regio's gelden, waarbinnen de soms wordt berekend. de .group()-functie maakt dit mogelijk
var areas = areaImageHa.reduceRegion({
      reducer: ee.Reducer.sum().group({
      groupField: 1,
      groupName: 'classification', //De bandnaam van het geclassificeerd beeld is standaard 'classification'
    }),
    geometry: ROI,
    scale: 10,
    tileScale: 6,
    maxPixels: 1e10
    }); 

var classAreas = ee.List(areas.get('groups'))
print(classAreas)

var areaChart = ui.Chart.image.byClass({
  image: areaImageHa,
  classBand: 'classification', 
  region: ROI,
  scale: 20, //Op 20m pixelgrootte berekenen voor beperken rekencapaciteit
  reducer: ee.Reducer.sum(),
  classLabels: ['','Mangrove', 'Other Forest', 'Water', 'Crop','Urban','Bare Soil'], //'0' is een lege klasse
}).setOptions({
  hAxis: {title: 'Classes'},
  vAxis: {title: 'Area Km^2'},
  title: 'Area by class',
  series: {
    0: { color: 'red' },
    1: { color: 'green' },
    2: { color: 'blue' },
    3: { color: 'yellow' },
    4: { color: 'black' },
    5: { color: 'brown' }
  }
});
print(areaChart); 
```