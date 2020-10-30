# Practicum 3: Image download & preprocessing
## Obtaining images


Sentinel-2 is an Earth observation mission developed by ESA as part of the Copernicus Programme to perform terrestrial observations in support of services such as forest monitoring, land cover changes detection, and natural disaster management. It consists of two identical satellites, Sentinel-2A and Sentinel-2B.  


The Sentinel-2 mission has the following capabilities:

- Multi-spectral data with 13 bands in the visible, near infrared, and short wave infrared part of the spectrum
- Systematic global coverage of land surfaces from 56° S to 84° N, coastal waters, and all of the Mediterranean Sea
- Revisiting every 5 days under the same viewing angles. 
- Spatial resolution of 10 m, 20 m and 60 m
- 290 km field of view
- Free and open data policy

To achieve frequent revisits and high mission availability, the two identical Sentinel-2 satellites (Sentinel-2A and Sentinel-2B) operate simultaneously. The orbit is Sun synchronous at 786 km (488 mi) altitude.  


!!! note "Ex 3.1 - Downloading a Sentinel 2 Level 1C image"
    In the first exercise, you will download an image from the Copernicus Open Access Hub.  

    * Go to https://scihub.copernicus.eu/ 
    * Klick ‘Open hub’ to access the Interactive Graphical User Interface
    * Log in (or create an account) ![](/assets/images/P3/login_logo.jpg){: style="height:25px"}
    * Zoom to Belèm, a city in the north of Brazil, close to the gateway of the Amazon river
    * Switch the ‘Open street’ view to ‘sentinel-2 cloudless + Overlay’ view 
    * Switch to ‘navigation mode’
    * Draw a rectangle around Belèm:  
      ![](/assets/images/P3/belem_scihub.jpg){: style="height:250px"}  

    * At the button ‘Insert search criteria’: go for ‘advanced search’
    * Look for a 2020 image (sensing period), Sentinel-2A, level 1C (product type) with a cloud cover of maximum 10%. Then click on the search button:  
      ![](/assets/images/P3/Sentinel2_search.jpg){: style="width:500px"}  
    * Click on the search button  
    * Search for an image that contains the major part of the city (inspect the image in a quick look view )
      ![](/assets/images/P3/Quicklook.jpg)
    * Download this image to a folder on your computer.


## Sentinel naming convention
The naming of the Sentinel products follows the Compact Naming Convention:
> **MMM_MSIXXX_YYYYMMDDHHMMSS_Nxxyy_ROOO_Txxxxx_"Product Discriminator".SAFE**  

Where:    
> **MMM**: is the mission ID (S2A/S2B)  
> **MSIXXX**: MSIL1C denotes the Level-1C product level/ MSIL2A denotes the Level-2A product level (see ‘radiometric correction’).  
> **YYYYMMDDTHHMMSS**: the datatake sensing start time  
> **Nxxyy**: the Processing Baseline number (e.g. N0204)  
> **ROOO**: Relative Orbit number (R001 - R143)  
> **Txxxxx**: Tile Number field  
> **.SAFE**: Product Format (Standard Archive Format for Europe)  

The products contain two dates. The first date (YYYYMMDDHHMMSS) is the datatake sensing time. The second date is the "Product Discriminator" field, which is 15 characters in length, and is used to distinguish between different end user products from the same datatake. Depending on the instance, the time in this field can be earlier or slightly later than the datatake sensing time.
Thus, the following filename  

>**‘S2A_MSIL1C_20170105T013442_N0204_R031_T53NMJ_20170105T013443.SAFE’**  

identifies a Level-1C product acquired by Sentinel-2A on the 5th of January, 2017 at 1:34:42 AM. It was acquired over Tile 53NMJ(2) during Relative Orbit 031, and processed with PDGS Processing Baseline 02.04.


!!! note "Ex 3.2 - naming convention"
    * Explain the different components of the name: S2A_MSIL1C_20180812T143751_N0206_R096_T19KGA_20180812T182110 (example)

### Sentinel 2 band scheme



## Introduction to SNAP  

SNAP, the **SeNtinel Application Platform** is developed by the ESA specifically to process Sentinel-imagery, however also other remotey sensed images can be read. The current version is *8.0.0*. SNAP is a relatively new software especially designed for the analysis of Sentinel products (Sentinel 2A was launched in 2015) and hence still contains some bugs (especially for mac-users, might try the older version 7.0.0). Not all applications are supported that you will find in classic Image Processing programs such as ENVI, but it is very user friendly and ideal to introduce you to satellite image processing. Also, it is free!

We will use SNAP to examine some image composites, and necessary preprocessing steps. After that, we will do most other processing with Google Earth Engine.  

!!! note
    * Open the sentinel image that you have downloaded (you do not need to unzip it). You can do this in several ways:    
        1. Drag and drop the zip folder in the Products explorer  
        2. Click file > Open Products and browse to your zip-folder  
        3. Click ![](/assets/images/P3/snap_folder.jpg) and browse to your zip-folder.  
    
    * Unfold the image folder. Explore the files included. Open the Blue, Green, Red and NIR image. 
    * Test the tile buttons. Make sure you can see the four images simultaneously:  
     ![](/assets/images/P3/snap_viewbuttons.jpg)
    * Explore the navigation panel.  

### The navigation window

The **Navigation Window** is used to move the viewport of an Image View, to zoom in and out of it and to rotate the image in steps of 5 degrees using the spinner control below the image preview. The current viewport is depicted by a semi-transparent rectangle, which can be dragged in order to move the viewport to another location. 

<figure>
  <img src="/assets/images/P3/navigation_window.jpg" />
  <figcaption>The navigation window</figcaption>
</figure>

In the bottom left, you will find the zoom factor: zoom is relative to the drawing extents.   
  
A **scale** factor of:  
1 shows a part of the image  
2 shows entities twice as large  
0.5 shows entities half as large  

The text box at the left side of slider can be used to adjust the zoom factor manually. The Navigation window additionally provides the following features via its tool buttons (top right):  

1. **Zoom In**: Zooms in by a factor of 1.2.  
   **Zoom Out**: Zooms out by a factor of 1/1.2.  
1. **Zoom Actual Pixel**: Sets the zoom factor to the default value so that the size of an image pixel has the same size of a display pixel.  
3. **Zoom All**: Adjusts the viewport to cover the entire image.  
4. **Synchronise Views**: Synchronises the viewports of all compatible image views.  
5. **Synchronise Cursor**: Displays a synchronised cursor on all opened image views.  

You can also zoom the images by scrolling on the image, or by clicking ![](/assets/images/P3/snap_zoom.jpg) in the toolbar. 

!!! warning 
    The **zoom factor** is not the same as a **Representative Fraction**, which is often used to indicate the scale of a map. The RF indicates the ratio between the number of units on the map to the number of units on the ground. 
 
    The RF factor 1:100000 e.g. implies that one cm on map is equal to 1 km on land. Maps are described as either large-scale or small-scale. Large-scale maps show a smaller amount of area with a greater amount of detail. The geographic extent shown on a large-scale map is small.  A large scaled map expressed as a representative scale would have a smaller number to the right of the ratio.  For example, a large-scale map could have a RF scale of 1: 1,000. Large-scale maps are typically used to show neighbourhoods, a localize area, small towns, etc. Small-scale maps show a larger geographic area with few details on them. The RF scale of a small-scale map would have a much larger number to the right of the colon such as 1: 1,000,000.  Small-scale maps are used to show the extent of an entire country, region, or continent. 

* Zoom to the Airport  
* Explore the **World View panel**. The red rectangle indicates the position of the image on the globe.  
<p align="center">

  <img src="/assets/images/P3/world_view.png" width="400">

</p>


- **The Colour Manipulation tool** window is used to modify the colours used in the image. If you are opening an Image View of a data product's band, the Sentinel Toolbox either loads image settings from the product itself (BEAM-DIMAP format only) or uses default colour settings. 
In the Colour manipulation panel, explore the histogram. On the image, zoom to the airport and adjust the contrast. Restore the contrast afterwards.  
<p align="center">

  <img src="/assets/images/P3/Snap_colormanipulation.jpg" width="400">

</p> 

- **Pixel info view**. If you click on the tab 'Pixel View' (right to the product explorer), pixel information will be displayed while you move the mouse over the band image view. 
<p align="center">

  <img src="/assets/images/P3/Snap_pixelview.jpg" width="400">  

</p> 

## Opening an RGB image
!!! note
    -	Which (Sentinel 2) band combination is used to make a natural colour composite? 
    -	Which (Sentinel 2) band combination is used to make a false colour infrared composite? 

To open a RGB image in Snap, right-click on the image folder and click 'Open RGB image window':

<p align="center">

  <img src="/assets/images/P3/snap_openrgb.jpg" width="400">  

</p> 

Some typical S2 band combinations are:

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

!!! note
    - Open the image as a natural colour composite  
    - Open the image as a false colour infrared composite  
    - Tile the images evenly and explore the difference in colour (for example in the areas with green vegetation).

## Radiometric & atmospheric correction

Satellite images obtained by the sensing device are not directly usable. They need to go through a series of pre-processing before they are ready to use. The scheme below illustrates the pre-processing steps that Sentinel-images undergo before they are made available for the user. This includes **geometric** correction, some **radiometric correction** (noise reduction, defective pixels identification) the computation of cloud masks, etc. The outcome is a level 1C product, which is Top-Of-the-Atmosphere (TOA). 

![](/assets/images/P3/sentinel2_preprocessing.jpg)

TOA reflectances are subjected to radiometric bias caused by different lighting conditions, atmospheric interactions and viewing geometry. In order to relate reflectances to physical field properties, TOA reflectance values are conversed to BOA (Bottom Of Atmosphere) corrected reflectance values. This radiometric correction is an essential part in image processing. BOA, Sentinel processing level 2A, is available for the user (except for recent images) or can be created by the user itself, using the Sen2Cor freeware.  

<p align="center">

  <img src="/assets/images/P3/sen2cor_result.jpg" width="800"> 
<em>Figure: A true color comparison of the surface reflectance product (top) and a top of atmosphere reflectance image (bottom) in adjacent scenes captured by the same satellite (Planet.com)</em> 
</p> 

In Snap, the conversion of level 1C TOA-reflectance to level 2A BOA-reflectance can be done through Sen2Cor (plug-in or stand-alone).  Sen2Cor corrects the reflectance values based on (among others) ‘look-up tables’, these are tables that relate physical parameters to model coefficients. Parameters such as inclination and product type are sensor dependent (different for Landsat as for Sentinel or Spot). On board, optical satellites have some meteorological sensors that measure atmosphere features such as the air thickness and the amount of aerosols among others. This information is available as a ‘header file’ for each image.   

Since December 2018, users can download Level-2A processed products directly. In case of this exercise, we downloaded a Level 1C product. Thus, let’s perform an atmospheric correction!

!!! note "Ex 3.4"
    * In the folder where you have saved the image, unzip the Sentinel-image.  
    * Go to *‘Optical’ > ‘Thematic Land Processing’ > ‘Sen2Cor processor’ > ‘Sen2Cor280’*  
    * When you choose the source product, click on the ‘…’, browse to the image and navigate to the ‘MTD_MSIL1C.xml’ product.
    * In the tab ‘processing parameters’, set the resolution to ‘ALL’
    * The other processing parameters are by default taken from a combination of the image metadata (header file) and look-up tables. This is why you will normally use the default processing parameters. However, if you want to adjust these parameters, you can do that manually. 
    * Run Sen2Cor (!be patient, it will take a while to process the entire image.)
    * Explore the outcome image (RGB). What differences do you see according to the original image? 


!!! warning "Installing 'Sen2Cor' plugin"
    Possibly sen2cor isn’t installed yet. To do this, go to ‘Tools’ > ‘Plugins’. During the first run, you’ll get an error, after which an extra bundle will be installed. ).

