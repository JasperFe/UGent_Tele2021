# Practicum 3: Beeldclassificatie

## Doelstelling

In dit practicum verdiepen we ons in het proces van de beeldclassificatie, waarbij manieren om remote sensing beelden te vertalen in landgebruikskaarten. Als voorbeeld nemen we een classificatie van mangroves.

## Beeldclassificatie: introductie

Beeldclassificatie (Engels: *'Image Classification'*) is de (complexe) procedure waarbij een multiband rasterbeeld wordt ingedeeld in klassen om hieruit spatiale informatie te ontrekken. Het is wellicht de meest belangrijke handeling bij digitale beeldanalyse. Het resulterende beeld kan gebruikt worden om thematische kaarten aan te maken.  

Gedurende de classificatie worden pixels ingedeeld in groepen, op basis van hun gemeenschappelijke karakteristieken. Er bestaan twee grote groepen van classificatiemethoden: *Supervised* of gesuperviseerde en *Unsupervised* of niet-gesuperviseerde classificatie.

In een **niet-gesuperviseerde** classificatie deelt de computer pixels in die spectraal gezien gelijkend zijn aan elkaar in een aantal klassen. Dit aantal kan op voorhand worden vastgelegd, of in de classificatieprocedure worden bepaald. Belangrijk is dat de analyse wordt uitgevoerd zonder dat de gebruiker hiervoor voorbeeldklassen of trainingsdata als input moet voorzien. De enige noodzakelijke input door de gebruiker betreft de keuze van het algoritme en eventueel het aantal gewenste resulterende klasses. 

In een **gesuperviseerde** classificatie wordt de classifier *getrained* op basis van enkele voorbeeldklassen of *training data*. Nieuwe, ongeklasseerde data wordt dan aan het algoritme voorgelegd, die dit dan op basis van het getrainde algoritme gaat indelen. In een finale stap wordt dan een andere portie van gekende data, de *validatiedata*, gebruikt om de accuraatheid (*accuracy*) van de classificatie kwantitatief te bepalen. In dit type classificatie is het aantal resulturende klassen eveneens bepaald op basis van het aantal klassen meegegeven tijdens het trainen van de data.

##Supergeviseerde vs niet-gesuperviseerde classifiers

|                         Niet-gesuperviseerde classificatie                         |                      Gesuperviseerde classificatie                      |
|:----------------------------------------------------------------------------------:|:-----------------------------------------------------------------------:|
|                                          +                                         |                                    +                                    |
|                     Er is geen voorkennis nodig van het gebied.                    |     De trainingsklassen komen overeen met de eigenlijke landbekking     |
|                        Minimale input van menselijke fouten                        |     Trainingdata is herbruikbaar, tenzij de landbedekking verandert.    |
|                              Gemakkelijk uit te voeren                             |      Resultaat is meteen bruikbaar, gezien de klassen gekend zijn.      |
|                                          -                                         |                                    -                                    |
| De resulterende klassen komen niet per sé overeen met gewenste landbekkingsklassen | De opgegeven klassen komen niet altijd overeen met de spectrale klassen |
|       Spatiale relaties of spatiale context wordt niet meegenomen in de data       |         Zekerheid/consistentie is niet over alle klassen gelijk         |
|                           Interpretatie kan moeilijk zijn                          |    Verzamelen van (voldoende) trainingdata kost moeite, geld en tijd    |