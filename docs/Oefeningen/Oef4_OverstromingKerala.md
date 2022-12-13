Bereken het overstromingsgebied voor de overstromingen die plaatsvonden in Kerala, India in Augustus 2018. Gebruik hiervoor Sentinel-1 beelden. Dit was een zeldzame overstroming, die ervoor zorgde dat ongeveer een miljoen inwoners in het gebied hun woningen verloren. Een Landsat-8 foto van voor en na de overstroming vind je via de website van [visible earth](https://landsat.visibleearth.nasa.gov/view.php?id=92669).

```javascript
var Kerala = ee.Geometry.Polygon(
        [[[76.24084197914547, 9.781021282526606],
          [76.24084197914547, 9.24061207598835],
          [76.57592498695797, 9.24061207598835],
          [76.57592498695797, 9.781021282526606]]], null, false);
```

??? check "Oplossing"
    [https://code.earthengine.google.com/8db2ef126747c691b6ce4402996fd2dc](https://code.earthengine.google.com/8db2ef126747c691b6ce4402996fd2dc)

