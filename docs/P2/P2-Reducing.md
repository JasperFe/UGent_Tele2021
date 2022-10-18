## Over Reducing
‘Reducing’ een beeld- of datacollectie in Google Earth Engine is het proces waarbij de beeldcollectie wordt geaggregeerd over tijd, ruimte, banden, ....  

In dit proces wordt een beeldcomposiet aangemaakt van de beschikbare beelden in de collectie, waarbij per pixel een bepaalde vooropgestelde waarde wordt gekozen, zoals het min, max, gemiddelde, mediaan,… De collectie wordt als het ware 'gereduceerd' tot één enkel visualiseerbaar beeld.

<p align="center">
  <img src="Images/Reducer_image.png" width="200">  <br>
  <em> Reducing an ImageCollection: principe. </em>
</p>  

Een voorbeeld: neem de eerste pixel van de gefilterde en gesorteerde Landsat 8 collectie ```L9_sortedCC ```. Visualiseer het resultaat. Wat valt je op? Is ditmaal het volledige gebied bedekt?

```javascript
//  Reducer over de L9_sortedCC collectie, waarbij steeds de eerste pixel genomen wordt.
var L9_first_red = L9_sortedCC.reduce(ee.Reducer.first());

//Bekijk de eigenschappen van het gereduceerd beeld
print(L9_first_red)

var visParams_first = {
  bands: ['SR_B4_first', 'SR_B3_first', 'SR_B2_first'],
  min: 0,
  max: 0.3,
  gamma: 1.4,
};

// Visualiseren als een normale kleurencomposiet
Map.addLayer(L9_first_red, visParams_first, 'L9_First pixels')

```

!!! warning "Bandbenaming na reducing"
    Let ook, bij het aanroepen van ```ee.reducer```, worden ook de banden hernoemd. Houd hier rekening mee bij het visualiseren. Het eventueel hernoemen van banden kan via de functie **```.rename()```**
    <p align="center">
    <img src="Images/First_reducer_bands.JPG" width="250">  <br>
    <em> Bandnamen bij de 'First'-gereduceerde collectie. </em>
    </p>

!!! warning "```ee.Reducer.first()``` VS ```.first()```"
    In een eerdere oefening namen we reeds het eerste beeld uit een hele collectie met de ```.first()``` functie. Dit is dus niet hetzelfde, gezien een reducer zicht niet beperkt tot één enkel beeld, maar de volledige collectie gaat reduceren. 

## Shortcut syntax

Bepaalde – veel gebruikte – reducers hebben ook een zogenaamde ‘shortcut’ syntax in Earth engine: ```mean()```, ```median()```, ```min()``` en ```sum()``` kunnen rechtstreeks gebruikt worden. Deze shortcut syntax zorgt ervoor dat een collectie eenvoudiger te reduceren is, zonder de hele ```.reduce((ee.Reducer.mean())``` syntax te moeten gebruiken. Een voorbeeld:

```javascript

//Een Median() Reducer over the Landsat-8 collectie
var L9_median = L9.reduce(ee.Reducer.median());

//Of via de short-syntax (geeft zelfde resultaat)
var L9_median = L9.median();

// Visualiseren
Map.addLayer(L9_median, trueColor, 'L9_median')

```

<p align="center">
  <img src="Images/Voorbeeld_median_reducer.JPG">  <br>
  <em> Voorbeeld mediane reducer over de L9_sortedCC-collectie </em>
</p>  

### Opdracht 3.2
Probeer enkele van de Reducers uit op je Sentinel-2 collectie van Gent.
Bewaar je script.
