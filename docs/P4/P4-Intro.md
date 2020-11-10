
## De Google Earth Engine Interface

<p align="center">
  <img src="images/GEE_interface.JPG" width="700">  <br>
  <em> The Google Earth Engine code editor interface. </em>
</p> 

De interface van de Earth Engine code editor is op zich vrij simpel. Er kunnen 5 grote blokken onderscheden worden:

  1. Het linkerpaneel, met 3 tabs:  
      1. **Scripts**: je eigen bibliotheek met scripts, onder te verdelen in repositories, folders en scripts. Ook de scripts waar je schrijf- en leesrechten hebt kun je hierin terugvinden.  
      2. **Docs**: Bevat informatie over de functies die beschikbaar zijn in Earth Engine. Hier kun je snel de functionaliteiten en beschrijving van de input- en outputparameters terugvinden.  
      3.  **Assets**: oplijsting met de 'assets' die je opgeladen/aangemaakt hebt in Earth Engine. Assets kunnen rasters of vectoren (met bijvoorbeeld trainingsdata of studiegebied).   
  2. De **code editor** zelf in het middenpaneel, waar je scripts kunt aanmaken/bewerken, delen en opslaan. 
  
  3. Het rechterpaneel, met 3 tabs:  
      1. De **Console**: waar eventuele output of foutmeldingen naar geschreven worden. de 'print()'-functie wordt steeds gebruikt om hier informatie te bekijken.  
      2. De **Inspector**: hiermee kun je op specifieke pixels in de 'map view' klikken, waarna de overeenkomstige pixelinformatie wordt gevisualiseerd.
      3. De **Tasks**: bevat een oplijsting van de 'exports' die in het script werden aangemaakt (als je bijvoorbeeld een satellietbeeld naar je Google Drive wenst te sturen). Een export moet hier steeds nog manueel gestart worden.

  4. De **Map View**: waar het beeldmateriaal wordt gevisualiseerd.

  5. De **zoekfunctie** waarmee beeldmateriaal beschikbaar binnen de Google Cloud kan worden opgezocht.


## Earth Engine code: Javascript 101

Google Earth Engine maakt voor zijn code-editor gebruik van Javascript als programmeertaal, maar om vertrouwd te geraken met GEE hoef je geen Javascript-expert te worden. GEE gebruikt namelijk hoofdzakelijk eigen 'classes' en functionaliteiten, waardoor je slechts een basiskennis javascript nodig hebt.  

Daarom starten we eerst met een spoedcursus Javascript, waarop we onze verdere 'Earth Engine'-magie kunnen bouwen. 

### "Hello World"

#### De 'print'-functie

Zoals gebruikelijk is bij het leren van een programmeertaal, groeten we de wereld met ons eerste lijntje code.  
  
  * Open [https://code.earthengine.google.com/](https://code.earthengine.google.com/), en voeg volgend lijntje toe aan het nieuwe script. 

```javascript
//Printen van Hello World
print('Hello World')
```
Klik daarna op 'Run'. Proficiat! Het eerste scriptje is geschreven. Hiermee heb je onmiddellijk ook een eerste uitermate handige functie gezien. De ‘print’-functie kun je gebruiken om bepaalde informatie naar de Console te schrijven, zoals metadata, ... .  

Verder valt hieruit ook op te merken dat een dubbele voorwaartse **slash '//**' gebruikt wordt om notities te nemen binnen de code.

#### Strings
Proficiat! Het eerste scriptje is geschreven. Laat ons deze string nu onderbrengen in een variabele. In Javascript dient een variabele altijd geïniteerd te worden met ```var``` statement. Indien je dit zou weglaten, zal je op een 'error' stoten.

```javascript
//Aanmaken van de variabele 'aString'
var aString = 'Hello World'
print(aString)
```

Om het datatype van de variabele ```aString``` na te gaan, kun je dit oproepen met de functie ‘typeof()’-statement:

```javascript
// Type van de variabele aString naar de Console schrijven
print(typeof(aString))
```

#### Functies
In een volgende stap maken we een **functie** aan, waarbij je een string naar keuze kunt groeten. Een functie in Google Earth Engine ziet er uit volgens volgende opbouw:

```javascript
var functienaam = function(inputvariabelen) {
//Hier de functie-bewerkingen
//output = a + b
Return  output
};
```
Bijvoorbeeld:

```javascript
//Hello Function:
var hello_function = function(String) {
   var goeindag = 'Hello ' + String
   return goeindag
};

//Functie uitvoeren:
var hallo = hello_function('Boerekot');
print(hallo)

//Variabelen aangemaakt binnen de functie worden enkel daar gebruikt:
print(goeindag)
```

#### Lijsten
Een lijst in Javascript wordt steeds opgegeven met [ en ]. Een lijstindex begint steeds vanaf '0', waarbij de eerste waarde dus op positie 0 staat.

```javascript
var lievelingsnummers = [8,6,3,27]
print('Eerste lievelingsnummer in de lijst = ', lievelingsnummer[0])

//Lijstelementen aanpassen
var automerken = ["BMW","Volkswagen","Minerva"]
var automerken[2] = ["Opel"]

print(automerken)

```

#### Objecten
Een Object wordt aangegeven met '{' en '}'. Aan een object hangen steeds enkele variabelen die tot het object behoren.

```javascript
//object
var dict = {
  Sensor: "Sentinel 2" 
  Regios: ["Belgium","France","Vaticano"]
  Aantal beelden: 2
  1: "Ja"
}
```

Om een eigenschap van een object op te roepen, wordt stees een puntje '.' gebruikt: object.eigenschap. Indien we bijvoorbeeld de sensor van ons aangemaakte beeldmateriaal willen nagaan:

```javascript
// Sensor bekijken
print(beelden.Sensor)

// Andere methode via haakjes []
print('Regios: ', dict['Regios'])
```


## Specifieke Earth Engine objecten

### ee.Thing

Om objecten/elementen/processen richting de google server te sturen, wordt gebruik gemaakt van zogenaamde 'containers'. Om dit aan te duiden wordt gebruik gemaakt van een ee.Thing structuur. Dit zal doorheen de komende practica wat duidelijker worden.

### ee.Images

Een ```Image``` is rasterdata bestaande uit één of meerdere banden, waarvan elke band een eigen naam, datatype, resolutie en projectie heeft. Een enkel Sentinel-2 beeld zoals in Practicum 3 gedownload werd, zal als één ```Image``` kunnen worden opgeslagen.

Om een Image in te laden en Earth Engine wordt gebruik gemaakt van ```ee.Image```. In volgend hoofdstuk wordt dit geïllustreerd. 

### ee.ImageCollections

Een ```ImageCollection``` is een collectie van meerdere ```Image```'s, zoals bijvoorbeeld de volledige Sentinel-2 collectie. Het bevat m.a.w. heel wat beelden die in een bepaalde volgorde gesorteerd zijn. Standaard is dit o.b.v. datum, maar aangepaste sorteringen zijn eveneens mogelijk, zoals we in een komende oefening gaan zien.

