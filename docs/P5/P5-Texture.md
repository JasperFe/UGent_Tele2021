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

### Entropie
Een eerste voorbeeld is de entropie. De entropie van een beeld vertaalt de *randomness* van de intensiteit naar een index. Het kan worden gezien als een maat voor ‘pixeldiversiteit’ binnen een bepaalde zone (*neighborhood*).  In dit voorbeeld wordt een kernel aangemaakt met radius 7m, waardoor de textuurberekening dus steeds binnen een radius van 7m wordt berekend. Gezien een entropy-berekening enkel kan worden uitgevoerd op discrete waarden, dienen we het pixeltype eerst om te zetten naar een integer.

```javascript
// Define a neighborhood with a kernel.
var square = ee.Kernel.square({radius: 4});

// Compute entropy and display.
var entropy = nS2_Belem.select('B8').entropy(square);
Map.addLayer(entropy,
             {min: 1, max: 5, palette: ['0000CC', 'CC0000']},
             'entropy');
```

### Standaard deviatie

De **Standaard Deviatie** (SD) berekent de spreiding van de pixelwaarde-distributie binnen de neighborhood.

```javascript
```

Een eerste voorbeeld is de entropie. De entropie van een beeld vertaalt de *randomness* van de intensiteit naar een index. Het kan worden gezien als een maat voor ‘pixeldiversiteit’ binnen een bepaalde zone (*neighborhood*).  In dit voorbeeld wordt een kernel aangemaakt met radius 7m, waardoor de textuurberekening dus steeds binnen een radius van 7m wordt berekend. Gezien een entropy-berekening enkel kan worden uitgevoerd op discrete waarden, dienen we het pixeltype eerst om te zetten naar een integer.