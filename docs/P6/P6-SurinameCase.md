## Over Mangroves

### Defenitie en verspreiding
Het **Mangrove-ecosysteem** dat kan gezien worden als een soort moerasecosyteem, gedomineerd door mangrovebomen/bossen. Het Mangrove-ecosysteem is de zone die zich doorgaans bevindt het intergetijdenzone tussen zee en land in zowel tropische als subtropische gebieden. Binnen dit Mangrove-ecosyteem bevinden zich de Mangrovebomen zelf (*'True Mangroves'*) en de geassocieerde soorten (*Mangrove associates*).
  
Hoewel er geen vastgelegde defenitie bestaat van mangrovebossen, kan deze van Tomilson (2016) worden gezien als de meest geaccepteerde:

> **True Mangroves** are plant species that:  
> 1. Occur only in mangrove forests and are not found in terrestrial communities  
> 2. Play a major role in the structure of the mangrove community, sometimes forming pure stands.  
> 3. Have morphological specialisations to the mangrove environment  
> 4. Have some mechanism for salt exclusion  

In totaal bestaan er zo'n 55 mangrovesoorten die aan bovenstaande defenitie voldoen, waarvan de hoogste soortendiversiteit zich voordoet rond het westelijk deel van de Stille Oceaan. Globale inschattingen van dit ecosysteem variëren tussen 110.000 tot 240.000 km² (Giri, 2016). 


### Mangroves in Suriname

In Suriname bestaan er "slechts" 5 mangrovesoorten waarvan 2 soorten de grootste dominantie vertonen: de zwarte mangrove of 'Parwa' (*Avicennia germinans*) die hoofdzakelijk langs de kustlijnen groeit in homogene bestanden en de rode mangrove of 'Mangro' (*Rhizopora mangle*), die landinwaarste en langs rivieroevers terug te vinden is.

<p align="center">
<img src="images/Mangrove_distribution.jpg" width=400>  <br>
 <em> Verspreiding van de 2 dominerende Mangrovesoorten in 2018 (SBB, 2019). </em> 
</p>


|    Parwa (*Avicennia germinans*)      |   Mangro (*Rhizopora Mangle*)|
|:------------------------------:|:--------------------------------:|
|![](images/Parwa_Suriname.jpg)  |  ![](images/Mangro_Suriname.jpg)|

## Mangrove mapping

### LU/LC klassen

In de voorbeelde voor beeldclassificatie gaan we zelf een LU/LC-kaart maken voor de kustzone ter hoogte van Paramaribo, de hoofdstad van Suriname. Hierin gaan we onderscheid maken tussen volgende klassen, zodat we Mangroves kunnen onderscheiden:

  1. Mangrove
  2. Ander bos
  3. Water
  4. Crops/grasvegetatie
  5. Urban (stedelijke omgeving)
  6. Naakte Bodem (soil)

### Onderscheiden van Mangroves op satellietbeelden

Als laatste voorbereidende stap gaan we na op welke manier we Mangroves (visueel) kunnen onderscheiden van de andere klassen (met in het bijzonder aangrenzend inlands bos). 

### Healthy Vegetation Composite

Het aanmaken van verschillende beeldcomposieten helpt om visueel de verschillende landbedekkingsklassen te onderscheiden. Naas de Normale Kleurencomposiet (gemakkelijkst interpreteerbaar voor het menselijk oog) en de Valse Kleurencomposiet (benadrukking verschil vegetatie - andere klassen) kan ook de *healthy vegetation composite* nuttig zijn.

Deze composiet is gemaakt met volgende banden: RGB = NIR, SWIR en Blauw, wat met de resepectievelijke Sentinel-2 banden *B8, B11, B2* kan worden aangemaakt. 

In deze composiet kleurt 'gezonde vegetatie' met toetsen van rood, orangje en geel. Doordat de SWIR-band wordt gebruik, kunnen verschillende fases van plantengroei en/of stress worden nagegaan. Mangrovebossen, kan op deze composiet worden onderscheden van andere bossen door hun specifieke watergebonden milieu.

!!! note "Aanmaken van Beeldcomposieten"
    Maak een 'Healthy Vegetation'-composiet aan van de Surinaamse kustlijn. Bekijk met welke stretch de mangrovezone het best tot uiting komt.

### Mangrove Vegetation Index (MVI)

Doorheen de jaren werden ook enkele specifieke Mangroves indices ontwikkeld, om de detectie van Mangroves te vergemakkelijken. Een goed voorbeeld is de **'Mangrove Vegetation Index'** of **MVI** ([Baloloy, 2020](https://www.sciencedirect.com/science/article/abs/pii/S0924271620301519)), dat recentelijk werd ontwikkeld op basis van de Sentinel-2 banden B2, B8, B11, die de specifieke 'groenheid' en vochtomstandigheid die de mangrovebossen typeert in de verf zet, om zo een onderscheid te kunnen maken met de naburige terrestrische bossen en vegetaties.

De MVI wordt als volgt geformuleerd:

$$MVI = { NIR - Green \over SWIR1 - Green}.$$

Wat zicht vertaald in Sentinel-2 banden als:

$$MVI = { B8 - B3 \over B11 - B3}.$$