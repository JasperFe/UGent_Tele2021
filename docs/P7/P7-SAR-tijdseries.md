Dit laatste onderdeel vormt een introductie tot het verwerken en bekijken van SAR-data in Google Earth Engine. SAR staat voor *'Synthetic Aperture Radar'* (NL: Apertuursyntheseradar). Het is wellicht het meest gebruikte type Radar-systeem binnen de Remote Sensing en biedt hoge resolutie radarbeelden.

Het voordeel van Radar ten opzichte van multispectrale beelden:

- SAR-golven penetreren door wolkbedekking, waardoor wolken geen belemmering vormen tijden het monitoren van landbekking. Zeker in tropische landen biedt deze capaciteit van een sterk voordeel bv tegen illegale ontbossing gedurende grote regenperiodes.

- Afhankelijk van het type SAR, penetreren de SAR-golven (gedeeltelijk) doorheen het kronendak, waardoor betere biomassa- en structuurinschattingen mogelijk zijn.

- SAR remote sensing is een actieve vorm van teledetectie: de satelliet zorgt zelf voor energiebron. Hierdoor is het ook tijdsonafhankelijk en kan het zowel gedurende de dag- als nacht opereren.

Bijgevolg is SAR een uitstekend middel om aan bosmonitoring, flood mapping en disaster management te doen. Voor de geïnteresseerden: [The SAR handbook (NASA, 2019)](https://servirglobal.net/Global/Articles/Article/2674/sar-handbook-comprehensive-methodologies-for-forest-monitoring-and-biomass-estimation) vormt een uitstekend handboek waar zowel de theoretische achtergrond als enkele toepassingen worden uitgelicht.


Onderstaande voorbeelden hebben als doel slechts een introductie te bieden tot het gebruik van SAR in Google Earth Engine. Er wordt gewerkt met Sentinel-1 data.

??? info "Over Sentinel-1"
    <p align="center">
    <img src="images/Sentinel_1.jfif" width="350">  <br>
    </p>   

    Sentinel-1 is een onderdeel van het ESA Copernicus-programma. Deze missie bestaat net zoals Sentinel-2 uit twee satellieten die 180° tegenoverklaar in orbit rond de aarde zweven. De waarnemingen gebeuren in de C-band (ca. 5,405 GHZ). Door deze positie dekt de missie om de twee weken de totale aarde. De meest voorkomende S1-doelstellingen zijn:
    
    * Het opvolgen van zee-ijs verschuivingen  
    * In kaart brengen van humanitaire hulp in crisistijd  
    * Monitoring van het mariene milieu  
    * Monitoring van landbouw en bosbouw  


## Voorbeeld 1 - Near-Real Time Forest Monitoring (NRTM)

### Over NRTM
**Near-Real Time Forest Monitoring** of **NRTM** is het principe waarbij bosbedekking in gebieden met veel illegale houtkap, van nabij wordt gemonitord waarbij steeds meest recente beschikbare satellietdata wordt gebruikt om eventuele wijzigingen in het kronendak op te sporen. Hierdoor kunnen eventuele illegale houtkapactiviteiten worden onderschept en stopgezet, om erger te vermijden.


### Visualiseren van Sentinel-1 data

* Open een nieuw scriptje in Earth Engine.

* Zoek naar Sentinel-1 in de zoekbalk en lees kort de achtergrondinformatie door.
<p align="center">
<img src="images/GEE_sentinel-1.jpg" width="350">  <br>
</p>  

* Sentinel-1 beelden bevat verschillende polarisaties: HH, HV, VV, VH. Uit de theorieles weten we dat bijvoorbeeld 'VV' staat voor verticaal gepolariseerd signaal uit en verticaal gepolariseerd resultaat ontvangen. 

* We starten met de Sentinel-1 collectie (in earth engine: COPERNICUS/S1_GRD) in te laden en te filteren op basis van een Region of interest. In dit geval focussen we ons op een stuk tropisch regenwoud, ten oosten van het Brokopondo-meer in Suriname.
<p align="center">
<img src="images/GEE_ROI_brokopondo.jpg" width="350">  <br>
</p> 

```javascript
// Load Sentinel 1 C band SAR Ground Range collection (log scale, VV, descending)

Map.centerObject(ROI,13);
//VV-polarisatie
var VV_coll = ee.ImageCollection('COPERNICUS/S1_GRD')
        .filter(ee.Filter.listContains('transmitterReceiverPolarisation', 'VV'))
        .filter(ee.Filter.eq('instrumentMode', 'IW'))
        .filter(ee.Filter.eq('orbitProperties_pass','DESCENDING'))
        .select('VV')
        .map(function(image) {
          var edge = image.lt(-30.0);
          var maskedImage = image.mask().and(edge.not());
          return image.updateMask(maskedImage);
        });

//VH-polarisatie
var VH_coll = ee.ImageCollection('COPERNICUS/S1_GRD')
        .filter(ee.Filter.listContains('transmitterReceiverPolarisation', 'VH'))
        .filter(ee.Filter.eq('instrumentMode', 'IW'))
        .filter(ee.Filter.eq('orbitProperties_pass','DESCENDING'))
        .select('VH')
        .map(function(image) {
          var edge = image.lt(-30.0);
          var maskedImage = image.mask().and(edge.not());
          return image.updateMask(maskedImage);
        });
        
```

* In dit voorbeeld bekijke we voor 3 tijdstippen de verandering in bosbedekking van dit gebiedje; 2017, 2018, 2019. Maak 3 VV_beelden aan per jaar. Hieronder een voorbeeldje voor 2017. We nemen de gemiddelde waarde voor de periode Augustus.
```javascript
// Filteren op basis van locatie en tijdstip    
var VV_2017= VV_coll.filterBounds(ROI).filterDate('2017-08-01','2017-08-31').mean()
```

* Laat ons eens bekijken hoe elk afzonderlijk VV-gepolariseerd beeld per jaar er uit ziet:
var VV_Param = {"opacity":1,"bands":["VV"],"min":-12.695602621422937,"max":-2.5938492158251467,"gamma":1}; 
// Afzonderlijke beelden mappen: visueel weinig verschil te zien
Map.addLayer(VV_2017,VV_Param,'VV_2017',0)
Map.addLayer(VV_2018,VV_Param,'VV_2018',0)
Map.addLayer(VV_2018,VV_Param,'VV_2019',0)

* Doe nu hetzelfde, maar voor de VH-polarisatie. Bekijk in welke maat de sensitiviteit voor verschillende landoppervlakten verschilt in beide polarisaties.

* Op zich bieden de beelden afzonderlijk weinig informatie. In een volgende stappen creëeren we een beeldcomposiet, met volgende samenstelling: RGB= ['VV_2017', 'VV_2018', 'VV_2019']. Wat valt je op?

```javascript
//Afzonderlijke jaren samenvoegen
var VV_yearly = ee.Image(VV_2017).addBands(VV_2018).addBands(VV_2019);
Map.addLayer(VV_yearly, {min: -20, max: -5},'VV_2017_2018_2019')
```

* Tracht nu ook hetzelfde te doen voor de VH_polarisaties.

* Laat ons nu even kijken naar de verschillen tussen 2 jaren. Onderstaande redenering is als volgt: door het aftrekken van VV_2017 van VV_2019, worden de pixels waar in 2017 wel nog bos waren, maar in 2019 niet meer negatief. Uit trial-and-error weten we dat bij grove benadering de waarden tussen -6 en -15 overeenkomen met ontbossing.

```javascript
//Tussen 2 jaren: verschil nemen (2019 - 2017)
var VV_20172019 = VV_2019.subtract(VV_2017)

// REMAP: om verlies te bepalen (-6 tot -15 komt grofweg overeen met ontbossing)
var VV_20172019= VV_20172019.ceil(); // afronden naar boven
var loss_20172019= VV_20172019.remap([-6,-7,-8,-9,-10,-11,-12,-13,-14,-15],[1,1,1,1,1,1,1,1,1,1])

Map.addLayer(loss_20172019,{palette: 'red'},'Loss_2017_2020')
```

## Opdracht
* Bekijk de wijzigingen in mangrove langs de volledige kustlijn in Suriname. Tussen 2018-2019-2020.

* Plot tevens ook 2 S2-beelden (van 2017 en 2019) en bekijk ook hier de ontbossing.

## Voorbeeld 2 - Kartering van overstromingen met Sentinel-1

Note: Inspiratie via [UN-Spider](https://code.earthengine.google.com/f5c2f984c053c8ea574bfcd4040d084e).

SAR-gebaseerde overstromingskartering is een betrouwbare manier om op een snelle manier een inschatting te verkrijgen van de oppervlak waarover een overstroming zich heeft uitgestrekt. Gezien de onafhankelijkheid van de weersomstandigheden, kan deze cruciale informatie steeds asap worden afgeleid.

In wat volgt bekijken we een methode om op een snelle en eenvoudige manier dergelijke inschatting te maken, op basis van een analyse op 2 tijdstippen: voor en na een overstroming.

```javascript
var imgVV = ee.ImageCollection('COPERNICUS/S1_GRD')
        //.filter(ee.Filter.listContains('transmitterReceiverPolarisation', 'VV'))
        .filter(ee.Filter.eq('instrumentMode', 'IW'))
        .filterBounds(geometry2)
        //.select('VH')
        .map(function(image) {
          var edge = image.lt(-30.0);
          var maskedImage = image.mask().and(edge.not());
          return image.updateMask(maskedImage);
        });
```

### Opdracht
* Bekijk bovenstaande code voor het gebied. Lees het door en bekijk wat er gebeurt.  

* Bereken het overstromingsgebied voor de overstromingen die plaatsvonden in Kerala, India in Augustus 2018. Dit was een zeldzame overstroming, die ervoor zorgde dat ongeveer een miljoen inwoners in het gebied hun woningen zagen worden weggespoeld. Een Landsat-8 foto van voor en na de overstroming vind je via de website van [visible earth](https://landsat.visibleearth.nasa.gov/view.php?id=92669).

??? check "Oplossing"
    Via dit scriptje: [https://code.earthengine.google.com/6831f71b3c4154f2b3fbbed5b30bfaf1](https://code.earthengine.google.com/6831f71b3c4154f2b3fbbed5b30bfaf1)