
## Visualisatie van een enkelvoudig satellietbeeld 

Laten we simpel starten met het afbeelden van een enkel rasterbeeld. In Practicum 2 gingen we te werk met een Sentinel-2 beeld van de Braziliaanse stad Bélem uit 2021. Aangezien de volledige [Sentinel-bibliotheek](https://developers.google.com/earth-engine/datasets/catalog/sentinel) beschikbaar is binnen Earth Engine, kan dit beeld eenvoudig worden ingeladen. Bekijk hiervoor eerst de naam nog eens van je gedownload S2-bestand, bijvoorbeeld:

> S2B_MSIL1C_**20200808T134219_N0209_R124_T22MGD**_20200808T153444.SAFE

In Earth Engine is het vette gedeelte van de filenaam belangrijk. Dit wordt als volgt in earth-engine ingeladen, via **'ee.Image'**:

```javascript
//Voorbeeld: Sentinel-2 beeld van vorig practicum
var S2_Belem = ee.Image('COPERNICUS/S2_SR/20200808T134219_20200808T134214_T22MGD')
print(S2_Belem)

// Zoom in de Map-view in naar het beeld, met Zoom-factor 9
Map.centerObject(S2_Belem, 9);
```

Hiermee werd slechts een variabele aangemaakt die het beeld omvat. Om het beeld te visualiseren wordt gebruik gemaakt van de functie **Map.addLayer()**:

```javascript
//Visualiseren van het satellietbeeld
Map.addLayer(S2_Belem);
```
Bij het uitvoeren van bovenstaande code bekomen we een zwart vlak, niet bepaald de visualisatie die we wensen.

<p align="center">
  <img src="Images/MapaddLayer_1.JPG" width="400">  <br>
</p> 

Bij het uitvoeren van bovenstaande code bekomen we een zwart vlak, niet bepaald de visualisatie die we wensen. Dit komt omdat we nog geen visualisatieparameters hebben aangegeven, waardoor de eerste 3 banden naar de rode, groene en blauwe band respectievelijk worden toegekend en de pixelrange zo groot is dat alle pixels een zwarte kleur krijgen. Om dit manueel aan te passen, zoek je je toegevoegde laag in 'Layers' in de Map-view. Klik op het tandwieltje. Een visualisatie-scherm springt open. Pas de parameters aan, zodat je een normale kleurencomposiet verkrijgt, met een stretch van 3 sigma en druk op 'Apply'. Een visueel beter resultaat wordt verkregen.

!!! info "Image stretching"
    Verschillende stretch opties laten toe de histogrammen van het beeld te strechten om een betere visualisatie te krijgen. De stretch wordt uitgevoerd op basis van de huidige map view: ben je bijvoorbeeld ingezoomd om een stuk homogeen bos, wordt de stretch hierbinnen uitgevoerd. 
    <p align="center">
    <img src="Images/MapView_VisParam.JPG" width="400">  <br>
    <em> Instellen van de visualisatieparameters kan via 'Layers' in de Map view. </em>
    </p> 
    

<p align="center">
  <img src="Images/MapaddLayer_2.JPG" width="400">  <br>
</p> 

Het is echter niet handig om steeds opnieuw de visualisatie handmatig in te stellen. Gelukkig kan deze ook als code geïmporteerd worden in GEE (klik op 'Import'). De visualisatieparameters worden toegevoegd in de Imports. Deze kunnen dan in de Map.addLayer() -functie worden meegeven tijdens het visualiseren.

<p align="center">
  <img src="Images/imageVisParam.JPG" width="400">  <br>
</p> 

In de code-editor zelf kunnen de visualisatieparameters eveneens gedefinieerd worden als een Object.
```javascript
// Aanmaken van visualizatieparameters
var visualization = {
  min: 0,
  max: 3000,
  bands: ['B4', 'B3', 'B2'],
};
Map.centerObject(S2_Belem, 9);
Map.addLayer(S2_Belem, visualization,'Bèlem_met_Vis');
```

## Beeldcollecties zoeken en filteren

In voorgaande paragraaf visualiseerden we een Sentinel-2 beeld die we reeds hadden opgezocht waarvan wisten dat de kwaliteit goed zat én waarvan we de bestandsnaam reeds kenden. Het is natuurlijk niet handig om steeds een filenaam te moeten kennen om verder te kunnen werken in Earth Engine. Daarmee zouden we ook de geweldige kracht van het programma om doorheen vele petabytes aan aardobservatiedata te zoeken onbenut laten.  

In wat volgt gaan we op basis van een locatie op zoek gaan naar geschikte satellietbeelden, door het filteren van gehele beeldcollecties.

### Region of Interest (ROI)

Starten doen we met het intekenen van een gewenste **Region Of Interest** (ROI) in de Map View. Een ROI is niets anders dan de afbakening van het studiegebied, waarbinnen we onze data wensen te verkrijgen.  

Er kan rechtstreeks gezoomd worden naar een locatie via de zoekbalk bovenaan of door het scrollen met de muis. Teken vervolgs een gewenste gebied in door gebruik te maken van de toolknoppen in de "Map View": ![](Images/GEE_knoppen.jpg){: style="height:25px"}.  

In dit voorbeeld kiezen we voor de Konigin der badsteden, Oostende, als studiegebied:

<p align="center">
  <img src="Images/Oostende_polygon.JPG" width="1000">  <br>
</p> 

Automatisch wordt een nieuwe variabele aangemaakt onder de naam 'geometry', welke eenvoudig hernoemd kan worden naar een eenvoudig te gebruiken variabelenaam: 

<p align="center">
  <img src="Images/Ostend_Polygon.JPG" width="400">  <br>
</p> 

Bekijk de eigenschappen van de polygoon door het naar de console te printen:

```javascript
//Polygoon-informatie naar de console schrijven:

print(Oostende)
```


### Inlezen en filteren van een ``` ImageCollection```

Voor deze oefening maken we als afwisseling gebruik van Landsat-8 beelden (zie ook het stukje omtrent de [Earth Engine data catalog]()). De importeer-code kan gekopieerd worden uit de data catalog en ziet er als volgt uit:

```javascript
var L8 = ee.ImageCollection('LANDSAT/LC08/C02/T1_L2')
print('Grootte van de L8-collectie :', L8.size())
```

Hiermee verwijst de variabele 'L8' naar de volledige Landsat-8 collectie (surface reflectance). De '.size()'-functie berekent het aantal beelden dat in deze collectie zijn begrepen. Een hele hoop, sinds de collectie alle L8-beelden van de volledige aarde omvat. Om hier verder mee te werken dient de verzameling bijgevolg gefilterd te worden. Filteren kan op basis van de metadata:

```javascript
//Filteren o.b.v. datum, locatie:
var L8 = L8.filterDate('2020-01-01', '2020-12-31') //Op basis van datum
           .filterBounds(Oostende) //op basis van locatie (de AOI);

//Printen van de nieuwe grootte
print('L8 size na filtering',L8.size())

// Printen van de collectie voor inspectie
print('Filtered collection: ', L8)
```

De beelden in de collectie zijn standaard gesorteerd op datum, indien we dus het bovenste beeld eruit halen, zal dit het eerste Landsat-8 beeld zijn gemaakt in 2021. Met de functie *.first()*, halen we deze eruit.  Print deze naar de console en bekijk het verschil met de de Imagecollectie.

```javascript
// Krijg het eerste (standaard oudste) beeld uit de collectie:
var L8_first = L8.first()
print('Eerste Beeld:', L8_first)

```

In een volgende stap kunnen we dit beeld ook gaan visualiseren, met ```javascript Map.addLayer() ```. 6)	Ook nu kunnen we dit als een echte kleurencomposiet visualiseren (voor Landsat 8 betekent dit dus B2 (blauw), B3 (groen) en B4 (rood)). De bandnamen voor Landsat-8 Surface Reflectance beelden in google earth engine werden aangepast naar SR_B*. 


```javascript
// Landsat-8 visualisatie instellen.
var trueColor = {
  bands: ['SR_B4', 'SR_B3', 'SR_B2'],
  min: 8000,
  max: 15000,
  gamma: 1.4,
};
Map.addLayer(L8_first, trueColor, 'L8_TrueColorComposite')
```

<p align="center">
  <img src="Images/Oostende_first.JPG" width="500">  <br>
  <em> Eerste Landsat 8 beeld binnen de gefilterede collectie </em>
</p> 

Mogelijk is dit eerste beeld niet het meest ideale wat betreft de wolkbedekking, waardoor er weinig te zien valt. Laten we nu op zoek gaan naar het beeld met de laagste wolkenbedekking binnen de collectie. Dit doen we in eerste instantie door de collectie te sorteren volgens het percentage cloudcover, wat standaard tot de metadata van een Landsatbeeld behoort.
Bekijk het beeld. Wat valt je op? Wordt het volledige gebied bedekt?

```javascript
//Sorteren van de collectie obv cloud cover
var L8_sortedCC = L8.sort('CLOUD_COVER',true);
Map.addLayer(L8_sortedCC.first(), trueColor, 'Least Cloud cover 2020')

```

<p align="center">
  <img src="Images/Oostende_LC.JPG" width="500">  <br>
  <em> Landsat 8-beeld met laagste wolkbedekking binnen de gefilterede collectie </em>
</p>  

Bekijk op welke dag de sensor dit beeld heeft genomen. Gebruik hiervoor de ‘inspector’ om de beeldeigenschappen verder te bekijken.

<p align="center">
  <img src="Images/Inspector.JPG" width="500">  <br>
  <em> De inspector </em>
</p>  


    

### Opdracht 3.1 - Valse kleurencomposiet voor Gent
Visualiseer in een nieuw script een valse kleurencomposiet van een Sentinel-2 beeld (Tier 1, Surface Reflectance). Neem hierbij Gent als ROI, met een beeld uit 2019 met de laagste wolkbedekking.  
    
Voor het sorteren van de wolkenbedekking, zoek je de gepaste eigenschap om op te sorteren. Deze kun je [hier](https://developers.google.com/earth-engine/datasets/catalog/COPERNICUS_S2_SR#image-properties){target=_blank} vinden.

Bewaar je script.

??? check "Oplossing"
    GEE script: [https://code.earthengine.google.com/0861ba83272bc305848ee2d113c4d3ef](https://code.earthengine.google.com/0861ba83272bc305848ee2d113c4d3ef){target=_blank}


