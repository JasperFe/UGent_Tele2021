Dit laatste onderdeel vormt een introductie tot het verwerken en bekijken van SAR-data in Google Earth Engine. SAR staat voor *'Synthetic Aperture Radar'*. Het is wellicht het meest gebruikte type Radar-systeem binnen de Remote Sensing en biedt hoge resolutie radarbeelden.

Het voordeel van Radar ten opzichte van multispectrale beelden:

- SAR-golven penetreren door wolkbedekking, waardoor wolken geen belemmering vormen tijden het monitoren van landbekking. Zeker in tropische landen biedt deze capaciteit van een sterk voordeel bv tegen illegale ontbossing gedurende grote regenperiodes.

- Afhankelijk van het type SAR, penetreren de SAR-golven (gedeeltelijk) doorheen het kronendak, waardoor betere biomassa- en structuurinschattingen mogelijk zijn.

- SAR remote sensing is een actieve vorm van teledetectie: de satelliet zorgt zelf voor energiebron. Hierdoor is het ook tijdsonafhankelijk en kan het zowel gedurende de dag- als nacht opereren.

Bijgevolg is SAR een uitstekend middel om aan bosmonitoring, flood mapping en disaster management te doen. Voor de geïnteresseerden: 'The SAR handbook' (NASA, 2019) vormt een uitstekend handboek waar zowel de theoretische achtergrond als enkele toepassingen worden uitgelicht. Dit boek is via deze [link](https://servirglobal.net/Global/Articles/Article/2674/sar-handbook-comprehensive-methodologies-for-forest-monitoring-and-biomass-estimation) gratis te downloaden.


Onderstaande voorbeelden hebben als doel slechts een introductie te bieden tot het gebruik van SAR in Google Earth Engine. Er wordt gewerkt met Sentinel-1 data.

!!! info "Over Sentinel-1"



## Voorbeeld 1 - Ontbossingsmonitoring met Sentinel-1
**Near-real time Forest Monitoring**


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