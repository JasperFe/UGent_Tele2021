In de gesuperviseerde classificaties testten we reeds 3 'classifiers', waarvan de *Random Forest* classifier leidde tot een iets betere algemene accuraatheid. Desondanks ze al tot acceptabele percentages leidden, bestaan er enkele opties om de classificatie nog te verbeteren.

## Aanpassen van het aantal trainingssamples en aanpassen *sampling*-strategie
De opgemaakte trainingssamples kunnen worden verbeterd door het aantal samples omhoog te trekken, of door de *sampling*-strategie aan te passen. In dit voorbeeld tekenden we zelf polygonen in op basis van visuele inspectie, waardoor we enkel de pixels werden aangeduid waar we relatief zeker waren van de desbetreffende klasse.

Een betere techniek zou erin bestaan om op voorhand willekeurige locaties door de computer aan te laten duiden, eventueel gestratificeerd per landbedekkingsklasse ('*stratified sampling*').

## Toevoegen van Indices
Ook door de input-banden aan te passen of extra banden toe te voegen, kan er voor zorgen dat de onderscheiding van de klassen wordt verbeterd. Zo kan:

  * er hoogtedata (zgn. *Digital Elevation Model* of DEM) worden toegevoegd, waaruit bv de hellingsgraad van het terrein kan worden berekend. Zo kunnen land*features* met een specifieke topografie zoals basins, kanalen, toppen, dalen, hellingen gemakkelijker worden geïdentificeerd.

 * er informatie uit berekende indices helpen voor versterking van verschillen tussen klassen, zoals NDVI, MNDWI of in de context van mangrove-classificatie de MVI.

## Opdracht

Tracht je classificatie te verbeteren door:  

  * enkele indices toe aan het Sentinel-2 beeld van vorige oefening, zoals de MVI, NDVI en NDWI.  
 
  * Textuurmatrices toe te voegen. Zie hiervoor [P5 - Textuur](#textuuranalyse)
Gebruik deze keer enkel de Random Forest classifier. Bekijk de bekomen accuraatheid. Merk je verbeteringen op?