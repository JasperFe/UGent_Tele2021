## About colour composites

Multispectral imagery, such as Sentinel-2, consists of several bands of data. As seen during in previous chapter, these bands can be displayed individually as a grey scale image (black = low reflectance, white = high reflectance), but they can also be displayed as a combination of three bands: a **colour composite** (NL: kleurcomposiet).  

When creating a colour composite: the three primary colours are used: red, green and blue. When they are combined in various proportions, different colours are produced per pixel. When 3 spectral bands (both visible as non-visible bands) are assigned to a primary colour, a colour composite is formed.

<p align="center">

  <img src="/assets/images/P3/rgb.gif" width="200">  <br>
  <em> By combining different proportions of the three primary colours Red, Green and Blue, various colours are created </em>
</p> 


## Some famous colour composites

### True Colour Composite
The most straightforward colour composite is the **true colour composite** (also **natural colour composite**), where the three visual primary colour bands of a multispectral image are assigned to their corresponding colour. 

For Sentinel 2, this composite is created as: Red: B4, Green: B3, Blue: B2.

<p align="center">
  <img src="/assets/images/P3/Gent_NormalColours.jpg" width="300">  <br>
  <em> Normal Colour Composite of Ghent. </em>
</p> 

### False Colour Composite

For Sentinel 2, this composite is created as: Red: B8, Green: B4, Blue: B3.

<p align="center">
  <img src="/assets/images/P3/Gent_FalseColour.jpg" width="300">  <br>
  <em> Normal Colour Composite of Ghent. </em>
</p> 

## Opening an RGB image

To open a RGB image in Snap, right-click on the image folder and click 'Open RGB image window':

<p align="center">

  <img src="/assets/images/P3/snap_openrgb.jpg" width="400">  

</p> 

Some typical S2 band combinations are (as Red Green Blue):

 - Natural Colours: **4 3 2** *  
 - False colour Infrared: **8 4 3** *  
 - False colour Urban: **12 11 4**  
 - Agriculture: **11 8 2**  
 - Atmospheric penetration: **12 11 8a** *  
 - Healthy vegetation: **8 11 2**  
 - Land/Water: **8 11 4**  
 - Natural Colours with Atmospheric Removal: **12 8 3 ** 
 - Shortwave Infrared: **12 8 4**  
 - Vegetation Analysis: **11 8 4**  

!!! question " "
    Only the band combinations with a * can now be displayed. Why is that? 

!!! note "Excercise: open band composites"
    - Open the image as a natural colour composite  
    - Open the image as a false colour infrared composite  
    - Tile the images evenly and explore the difference in colour (for example in the areas with green vegetation).