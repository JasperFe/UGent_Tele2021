Als laatste onderdeel van dit practicum, kijken we even naar de spectrale signaturen van onze klassen. Uit deze curves kunnen we volgende zaken afleiden (zie ook Practicum 1):

  * Welke banden/indices zorgen voor onderscheid tussen klassen?
  
  * Welk spectrale curves heeft elk van onze klassen? Kunnen we deze curves ook verklaren?

## Toevoegen van Spectrale Responsecurve aan de classificatie

Werk verder met het script van de gesuperviseerde classificatie. Om zoveel mogelijk aan rekenkracht te besparen, dan verwijder of maskeer je best alle 'prints'. Maak eventueel een nieuw scriptje aan waar je dan alle prints verwijdert.

Nu kunnen we een Chart-variabele aanmaken die de gemiddelde waarden per band per klasse van de classificatie zal plotten. Voeg hiervoor eerst de classificatie toe aan het gebruikte Sentinel-2 beeld. We gebruiken hier de Random Forest classificatie:

```javascript
// classificatie toevoegen aan Image
var S2_im = S2_im.addBands(classified_RF)


// Aanmaken van de Chart
var spectraChart = ui.Chart.image.byClass({
  image: S2_im,
  classBand: 'classification',
  reducer: ee.Reducer.mean(),
  scale:30,
  classLabels: ['','Mangrove', 'other forest','water', 'Crop/Grass','Urban','Soil'] //Klasse '0' is leeg
}).setChartType('ScatterChart')

// Extra opties om de plot mooi te maken

var plotOptions = {
  title: 'Sentinel-2 surface reflectance spectra',
  hAxis: {title: 'Wavelength (nanometers)'},
  vAxis: {title: 'Reflectance'},
  lineWidth: 1,
  pointSize: 4,
  series: {
    0: {color: 'red'}, // Mangrove
    1: {color: 'green'}, // Forest
    2: {color: 'blue'}, // water
    3: {color: 'yellow'}, //crops/grass
    4: {color: 'black'}, //Urban
    5: {color: 'brown'} //soil
  }};

print(spectraChart.setOptions(plotOptions))
```


