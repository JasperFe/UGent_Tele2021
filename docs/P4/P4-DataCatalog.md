### De Earth Engine Data Catalog

Om het aanbod aan Aardobservatie-data in GEE te bekijken en te doorzoeken, kan gebruik gemaakt worden van de Data Catalog: [https://developers.google.com/earth-engine/datasets](https://developers.google.com/earth-engine/datasets). Via deze catalogus kun je eenvoudig rasterdata allerhande opzoeken en de noodzakelijke code om de beelden in je script te importeren vinden.

In de komende voorbeeldoefening maken we gebruik van Landsat-data. Zoek in de Earth Engine Data Catalog naar een geschikte Landsat datacollectie, dat het jaar 2020 omvat. In Earth Engine zijn Landsatbeelden eerste instantie opgedeeld op basis van de uitgevoerde correcties (*'Surface reflectance'*, *'Top-Of-Atmosphere'*, *'Raw Images'*), anderzijds op basis van de kwaliteit:  

<p align="center">
  <img src="images/Landsat_GEE_Quality.JPG" width="700">  <br>
</p> 

   - **Tier 1**: De meest kwalitatieve beelden, geschikt voor tijdserie-analyse. De beelden zijn zowel geometrisch als radiometrisch kwalitatief goed bevonden.  
   - **Tier 2**: De beelden zijn geometrisch en/of radiometrisch minder kwalitatief bevonden, maar kunnen voor bepaalde doeleinden wel nog inzetbaar zijn.  
   - **Tier 1 + Real-Time**: De Tier-1 database uitgebreid met de meest recente data die nog niet kwalitatief zijn gekeurd en bijgevolg dus nog fouten kunnen bevatten. 

!!! info "Het Landsat programma"
    Landsat is het langst lopende aardobservatie satellietprogramma en is sinds 1972 continue operationeel. Het is een samenwerking tussen de *United States Geological Survey* (USGS) en de NASA. De meest recente lancering was deze van Landsat 8 in 2013, de lancering van Landsat 9 gepland staat voor 2022.

    <p align="center">
    <img src="images/Landsat_overzicht.jpg" width="500">  <br>
    </p> 
    
    Onderstaande grafiek geeft een vergelijking tussen de bandverdeling van de huidige operationele Landsatsatellieten: Landsat 8 (OLI/TIRS) en Landsat 7 (Bron: [NASA](https://landsat.gsfc.nasa.gov/landsat-8/landsat-8-overview))

    <p align="center">
    <img src="images/Landsat.jpg" width="700">  <br>
    </p> 

