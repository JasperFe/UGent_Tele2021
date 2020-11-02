# Practicum 3: Sentinel data download en beeldvoorbewerking in SNAP

## Doel van het practicum

In dit eerste PC-practicum bekijken we enkele bronnen om 'remote sensing'-data te verkrijgen, met focus op het recente ESA copernicus Sentinel-programma. 

Volgende topics komen aan bod: 

 * Downloaden van remote sensing data: 
     - via ESA sentinel hub
     - via andere bronnen
 * Introductie tot ESA SNAP:
     - Inlezen van RS beelden
     - Basisfunctionaliteiten
     - Aanmaken van beeldcomposieten
     - SNAP vs andere software
 * Beeldvoorbewerking in SNAP (Sentinel 2):
     - Radiometrische/atmospherische correctie
     - Resampling
     - Subsetting
     - Mosaicing


## Sentinel 2 image download

### About Sentinel 2 imagery
Sentinel-2 is an Earth observation mission developed by ESA as part of the Copernicus Programme to perform terrestrial observations in support of services such as forest monitoring, land cover changes detection, and natural disaster management. It consists of two identical satellites, Sentinel-2A and Sentinel-2B.  


The Sentinel-2 mission has the following capabilities:

- Multi-spectral data with 13 bands in the visible, near infrared, and short wave infrared part of the spectrum
- Systematic global coverage of land surfaces from 56° S to 84° N, coastal waters, and all of the Mediterranean Sea
- Revisiting every 5 days under the same viewing angles. 
- Spatial resolution of 10 m, 20 m and 60 m
- 290 km field of view
- Free and open data policy

To achieve frequent revisits and high mission availability, the two identical Sentinel-2 satellites (Sentinel-2A and Sentinel-2B) operate simultaneously. The orbit is Sun synchronous at 786 km (488 mi) altitude. 

### Sentinel 2 data download

All data captured by the ESA copernicus Sentinel program are completely freely available to the public. The most convinient way to download Sentinel data is through the Copernicus Open Access Hub, a platform dedicate to provide easy acces to the user. For this, an user account is required.  

To register go to [registration page](https://scihub.copernicus.eu/dhus/#/self-registration).





!!! note "Ex 3.1 - Downloading a Sentinel 2 Level 1C image"
    In the first exercise, you will download an image from the Copernicus Open Access Hub.  

    * Go to https://scihub.copernicus.eu/ 
    * Klick ‘Open hub’ to access the Interactive Graphical User Interface
    * Log in (or create an account)  
      ![](/assets/images/P3/login_logo.jpg){: style="height:25px"}  
     

    * Zoom to Belèm, a city in the north of Brazil, close to the gateway of the Amazon river
    * Switch the ‘Open street’ view to ‘sentinel-2 cloudless + Overlay’ view 
    * Switch to ‘navigation mode’
    * Draw a rectangle around Belèm:  
      ![](/assets/images/P3/belem_scihub.jpg){: style="height:250px"}  

    * At the button ‘Insert search criteria’: go for ‘advanced search’
    * Look for a 2020 image (sensing period), Sentinel-2A, level 1C (product type) with a cloud cover of maximum 10%. Then click on the search button:  
      ![](/assets/images/P3/Sentinel2_search.jpg){: style="width:400px"}  
 
    * Click on the search button  
    * Search for an image that contains the major part of the city (inspect the image in a quick look view )
      ![](/assets/images/P3/Quicklook.jpg){: style="width:500px"}
    * Download this image to a folder on your computer.


## Sentinel file naming convention
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

> Thus, the following filename  

>>**‘S2A_MSIL1C_20170105T013442_N0204_R031_T53NMJ_20170105T013443.SAFE’**  

> identifies a Level-1C product acquired by Sentinel-2A on the 5th of January, 2017 at 1:34:42 AM. It was acquired over Tile 53NMJ(2) during Relative Orbit 031, and processed with PDGS Processing Baseline 02.04.


!!! note "Ex 3.2 - naming convention"
    * Explain the different components of the name: S2A_MSIL1C_20180812T143751_N0206_R096_T19KGA_20180812T182110 (example)


## Introduction to SNAP  

SNAP, the **SeNtinel Application Platform** is developed by the ESA specifically to process Sentinel-imagery, however also other remotey sensed images can be read. The current version is *8.0.0*. SNAP is a relatively new software especially designed for the analysis of Sentinel products (Sentinel 2A was launched in 2015) and hence still contains some bugs (especially for mac-users, might try the older version 7.0.0). Not all applications are supported that you will find in classic Image Processing programs such as ENVI, but it is very user friendly and ideal to introduce you to satellite image processing. Also, it is free!

We will use SNAP to examine some image composites, and necessary preprocessing steps. After that, we will do most other processing with Google Earth Engine.  

!!! note "Excercise: Opening a Sentinel-2 image in snap"
    * Open the sentinel image that you have downloaded (you do not need to unzip it). You can do this in several ways:    
        1. Drag and drop the zip folder in the Products explorer  
        2. Click file > Open Products and browse to your zip-folder  
        3. Click ![](/assets/images/P3/snap_folder.jpg) and browse to your zip-folder.  
    
    * Unfold the image folder. Explore the files included. Open the Blue, Green, Red and NIR image. 
    * Test the tile buttons. Make sure you can see the four images simultaneously:  
     ![](/assets/images/P3/snap_viewbuttons.jpg)
    * Explore the navigation panel.  

!!! info "Sentinel 2 Bands"
    Let's have a quick look at the specifications of a Sentinel-2 image. There are 13 Sentinel 2 bands in total, with a resolution of 10, 20 or 60m:
    <p align="center">
    <img src="/assets/images/P3/S2_banden.png" width="450">
    </p>

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

!!! info "Zoom factor vs Representative Fraction"
    The **zoom factor** is not the same as a **Representative Fraction (RF)**, which is often used to indicate the scale of a map. The RF indicates the ratio between the number of units on the map to the number of units on the ground. 
 
    The RF factor 1:100000 e.g. implies that one cm on map is equal to 1 km on land. Maps are described as either large-scale or small-scale. Large-scale maps show a smaller amount of area with a greater amount of detail. The geographic extent shown on a large-scale map is small.  A large scaled map expressed as a representative scale would have a smaller number to the right of the ratio.  

    For example, a large-scale map could have a RF scale of 1: 1,000. Large-scale maps are typically used to show neighbourhoods, a localize area, small towns, etc. Small-scale maps show a larger geographic area with few details on them. The RF scale of a small-scale map would have a much larger number to the right of the colon such as 1: 1,000,000.  Small-scale maps are used to show the extent of an entire country, region, or continent. 

    <p align="center">

    <img src="/assets/images/P3/map_.png" width="400">

    </p>

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
!!! Question "Little recap"
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

!!! note "Excercise: open band composites"
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

!!! note "Excercise: atmospheric correction with Sen2Cor"
    * In the folder where you have saved the image, unzip the Sentinel-image.  
    * Go to *‘Optical’ > ‘Thematic Land Processing’ > ‘Sen2Cor processor’ > ‘Sen2Cor280’*  
    * When you choose the source product, click on the ‘…’, browse to the image and navigate to the ‘MTD_MSIL1C.xml’ product.
    * In the tab ‘processing parameters’, set the resolution to ‘ALL’
    * The other processing parameters are by default taken from a combination of the image metadata (header file) and look-up tables. This is why you will normally use the default processing parameters. However, if you want to adjust these parameters, you can do that manually. 
    * Run Sen2Cor (!be patient, it will take a while to process the entire image.)
    * Explore the outcome image (RGB). What differences do you see according to the original image? 


!!! warning "Installing 'Sen2Cor' plugin"
    Possibly sen2cor isn’t installed yet. To do this, go to ‘Tools’ > ‘Plugins’. During the first run, you’ll get an error, after which an extra bundle will be installed. ).


## Intermezzo: Cloud Masks
The image contain clouds. This means that there are some blind pixels, which lack information on the reflectance of the earth’s surface at the sensing time. This phenomenon is very common in tropical areas with a rainy season. It is possible that over the whole period of the rainy season, you will not be able to obtain images with a cloud cover of less than 90%. In such cases, Radar imaging can be useful, but are complexer. An introduction to radar imaging will be given later in these practicals.

<p align="center">

  <img src="/assets/images/P3/Folder_clouds.jpg" width="700"> 

</p> 
 

Included in a Sentinel-2 image folder you can find some cloud masks at a resolution of 10m, 20m and 60m. These cloud masks enable the user to identify cloudy and cloud-free pixels. The masks include both dense clouds (opaque clouds) and cirrus clouds. These cloud masks are computed by a threshold algorithm. Below, the methods are described that identify the cloud pixels (for your information).

**Identification of dense clouds** 
 
Dense clouds, also called opaque clouds, are characterised by a high reflectance in the blue spectral region (B2). The method used to identify dense cloud pixels is based on B2 reflectance threshold. To avoid false detection, mainly due to snow/cloud confusion, SWIR reflectance in B11 and B12 are also used. Snow and clouds both have a high reflectance in the blue. Cloud reflectance is high in the SWIR, whereas snow presents a low reflectance. Additional criteria based on B10 reflectance are added to avoid high altitude ice cloud and snow confusion (both having a low reflectance in the SWIR bands B11 and B12). At B10, there is a high atmospheric absorption band and only high altitude clouds are detected. However, this last criterion is only applied after a first detection of cloud pixel in the blue band where cirrus is transparent.  

**Identification of cirrus clouds**  

Cirrus clouds are thin, transparent or semi-transparent clouds, forming at high altitudes, approximately 6-7 km above the Earth's surface. The method of identifying cirrus cloud pixels from dense cloud pixel is based on two spectral criteria: (1) B10 corresponds to a high atmospheric absorption band: only high altitude clouds can be detected, (2) cirrus clouds, being semi-transparent, cannot be detected in the B2 blue band. A pixel with low reflectance in the B2 band and high reflectance in the B10 band has a good probability of being cirrus cloud but this is not a certainty. Some opaque clouds have a low reflectance in the blue and can be identified as cirrus cloud. To limit false detections (due to high reflectance in the blue or due to the fact that clouds are not spectrally registered), a filter using morphology-based operations is applied on both dense and cirrus cloud masks: (1) erosion, to remove isolated pixels, (2) dilatation, to fill the gap and extend clouds. If after morphology operations, a pixel is both dense and cirrus, the dense cloud mask prevails. 

**Sen2Cor scene classification**  

The Sen2Cor-processor you've runned for the atmospheric correction from the level 1C to the level 2A product also contains a scene classification algorithm. This algorithm creates a scene classification, where pixels als classified in some broad classes:


Here, clouds are classified into 'cloud probability masks', which are in general more precise than the level 1C cloud masks.
 
<p align="center">

  <img src="/assets/images/P3/sen2cor_sceneclassification.jpg" width="700"> 

</p> 


!!! note "Excercise: Visualize cloud masks"
    - Visualize the cloud masks.
    - If you look at the cloud masks, you will see that these are not very precise. These cloud masks are useful for rough estimations. Later we will see alternative ways to identify cloud pixels more precise. 


## Resampling
In order to display the other band combinations, some geometrical pre-processing is necessary. The bands have to be resampled to an equal resolution. The goal is to resample the image bands to 10m (you can take B2, B3, B4 or B8 as a reference band). This means that all other bands will be upsampled.

<p align="center">

  <img src="/assets/images/P3/Resampling_principle.jpg" width="500"><br>
  
  <em>Image resampling scheme. Top: upsampling (nearest neighbor). Bottom: Downsampling (minimum). </em> 

</p> 

!!! note "Exercise: resampling"
    - In the product explorer, select the outcome image of Sen2Cor. Go to *Raster > Geometric operations > Resampling*. 
    - Select the ‘Save as… BEAM-DIMAP’ box. Browse to your directory. Choose a logical name for the target product.
    - Resampling Parameters: Choose a reference band that has a resolution of 10m, or choose for a pixel resolution of 10m. Use an upsampling method of your choice (Read the help for more details on the different algorithms).
    - Run *resampling*. 
    - Saving the images takes a lot of time. Again, be patient! 

## Image Subsetting
Processing an entire Sentinel image takes a lot of processing capacity and time (as you probably have noted already). Therefore, you will now learn how to only process a small part of the image. You can choose to reduce the spatial extent of the image, or you can choose to reduce the amount of bands in the image, or a combination of both.

An important aspect is that creating a subset is only possible for bands that have the same size. Thus, this will only be possible **after resampling**.  


</p> 

!!! note "Excercise: subsetting an image"
    - Select the resampled image in the product explorer. Go to Raster > Subset.
    - Select a spatial subset by choice (by adjusting the scene start and end). Make sure your spatial extent is substantially smaller than the original image.
    <p align="center">
    <img src="/assets/images/P3/snap_subsetting_1.jpg" width="400"><br>
    <em>Snap Subsetting screen.</em> 
    - Select only following bands: [B2, B3, B4, B5, B6, B7, B8, B8A, B11, B12]
    - You can see an estimation of the new required storage space.
    <p align="center">
    <img src="/assets/images/P3/snap_subsetting_2.jpg" width="400"><br>
    <em>Snap Subsetting screen.</em> 
    - Click OK
    - Another option to make a subset is ‘Spatial subset from view’. Zoom in on your image. Rightclick and select ‘Spatial subset from view’. 
    - FYI: it is also possible to take a subset of an image, based on a vector layer. 


## Displaying different band combinations
If you now open an RGB image window, the band combination options will be larger. Test some of the following band combinations and explore the colour differences. Which features are most clear on the following band combinations?  

 - Natural Colours: **4 3 2**   
 - False colour Infrared: **8 4 3**   
 - False colour Urban: **12 11 4**  
 - Agriculture: **11 8 2**  
 - Atmospheric penetration: **12 11 8a**   
 - Healthy vegetation: **8 11 2**  
 - Land/Water: **8 11 4**  
 - Natural Colours with Atmospheric Removal: **12 8 3 ** 
 - Shortwave Infrared: **12 8 4**  
 - Vegetation Analysis: **11 8 4**  

## Mosaicing
Mosaicing is the merging of several arbitrarily shaped images and often used to merge two neighbouring satellite images.  

!!! note "Excercise: mosaicing"
    - Download an image that is located next to the image you are already working with, dating from the same time as the original image was taken. 
    - You can download it directly in Level 2A, thus skipping the sen2cor atmospheric correction.
    - Resample the image. 
    - Go to raster > Geometric operations > Mosaicing 
    <p align="center">
    <img src="/assets/images/P3/mosaicing_screen.jpg" width="400"><br>
    <em>Snap mosaicing screen.</em>  
    - Add the two source products.
    - Choose the directory in which you want to save the mosaic image.
    - In the Map Projection Definition you can choose the Coordinate Reference System (CRS). Choose for UTM/WGS84 (automatic)
    - Choose for a resolution of 10m.
    - The input products don’t need to be orthorectified (because they already are).
    - In the tab ‘Variables and Conditions’, click the  ![](/assets/images/P3/pages_symbol.jpg)- symbol.
    - Select Band 2,3,4 and 8
    - Run Mosaicing.
    - Open the RGB-image of the product. Compare it to the two original images. 


!!! Question "Extra: Examine the example of Landsat satellite image after merging below." 
    1. What went wrong when mosaicing images 1 and 2?
    2. Why is there a colour difference in 2 and 3?
    3. Why is there no observable colour difference in 2 and 4?
    4. Have you any idea how to eliminate the colour difference between 2 and 3, given that neighbouring satellite images always partly overlap?
    <p align="center">
    <img src="/assets/images/P3/mosaicing_extra.jpg" width="400"><br>
    <em>Landsat images mosaic</em>  

