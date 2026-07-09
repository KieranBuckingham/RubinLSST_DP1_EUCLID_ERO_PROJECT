This is a Senior Project by Kieran J. Buckingham, for B.S. in Physics from California Polytechnic State University, San Luis Obispo. 
This work was supported by NSF grant XX and advised by Dr. Louise V. Edwards 

This collection of notebooks represent a user friendly guide to a multi-telescope catalog query, 10 filter pectral energy distribution (SED) plot, custom coadded image creation, and morphology analysis using statmorph.

# Note
I started this senior project in january of 2026, I am writing this on april 8th. I received a notebook called catalog crossmatch from dr. edwards. this was a nearly fully built version of the a. catalog crossmatch notebook you'll see. from here used the rubin lsst dp1 tutorials to enchance the original file, fixing incorrect labels, disk space errors, incorrect units scaling, adding filters, adding query keywords, etc. The original file also contained a skeleton version of the b. spectral energy distribution notebook, which needed corrected units scaling and additional filters at the time. I am writing this with no significant errors at the  time and all dataframe, directory, path, and masking issues solved. This should be working on all devices with DP1 access, and requires no manual file creation/filtering. I believe this notebook is clear enough at has enough basic description for what each cell does as you proceed through the whole folder for a student of similar skill to have no problem. It requires nearly no input, most cells can be passed quickly however some do have very long runtimes, those cells are the ones noted in the markdown descriptions. To preserve file structure I recommend not changing the name of the folder from "KB_462".

#
1. Unzip KB_462.zip into your home or working directory.
2. Keep the extracted folder name as KB_462.
3. In Jupyter, open the KB_462 folder and run the notebooks from there.
4. Do not move individual files out of the folder, since the notebooks rely on the existing folder structure. However you may change file names in #Input# sections to create new files.

This pipeline was designed to be used on the Rubin LSST Data Preview 1 (DP1) release which requires secure access. It also utilizes data from the EUCLID Early Release Observations (ERO) release, which is public. 
I Believe it can be updated for future data releases, I have specific locations within the code if this is your goal. 

#
Here is how you use this pipeline

A. Catalog Crossmatch 

Part 1 
this section requires no input from the user
This portion contains most of the functions for the note book and if the user desires they can add or remove keywords from the DP1 or ERO query 
keywords can be found here: https://sdm-schemas.lsst.io/dp1.html

functions are 

Part 2 
This section contains the cell for sky region query, which if you are looking at a different region of space will require
USER INPUT, to input southern hemishpere sky coordinates, and a search radius size. this looks like:

###
# NEW Data
NEW_ra = [59.487316,59.487316]
NEW_dec = [-48.995145,-49.000349]
NEW_radius = [10,10]
#######
...
#######
### SELECT 
ra = NEW_ra
dec = NEW_dec
radius = NEW_radius
###

Then we can see a comparison of data in each 1 FWHM, 2 FWHM, 3 FWHM, and Sersic "apertures"
Data is filtered/ constrained. current filters are set the 1. build a 10 filter spectrum 2. lsst y filter limit of 100,000 Jy, for discarding bright objects 3. redshift, Z, range of [0.14, 0.15], this is for distant objects. This can be changed if have different constraints. for example a query might go from 63,000 to 15,000, to 15, to 15.
Then we take the filtered data and append our "Key Objects.csv" with the data 

The user should repeat this step until all sky regions have been surveyed and appened to Do this once you have a finished "Key Objects.csv". for ex

Part 3
Do this once you have a finished "Key Objects.csv"
This part solely sorts the "Key Objects.csv", in order of ascending tract ant patch. this is actually a critical step for later. 


B. Spectral Energy Distribution 
This notebook takes the "Key Objects.csv" and plots the flux of each filter in its respective wavelength, using rubin [u,r,g,i,z,y] and euclid [vis,y,j,h]. requires no user input 


C. Coadded Image Creation 
This notebook takes the sorted "Key Objects.csv" and lists the number of unique tract and patch combinations. this is the number of coadd images you want to make to cover all objects. 

Part 1 

USER INPUT, to select the 'n' value of the unique tract and patch combination in ascending order. user should start from 0 and move upward, unless 0 is desired, ie patch (2024,45) out of set [(2024,45)(2024,46)]. this looks like :

####
# CHOSE TRACT AND PATCH TO IMAGE 
# WE WANT TO GO 1 AT A TIME DUE TO LONG PROCESSING TIME

n = 0
#####

with output that looks like: 

2234 92
2394 3
2394 4
2394 5
2394 13
2394 14
----------------------
Selected Tract, Patch, Number of objects in image:
2234 , 92 , 1
Coadd number 1 Out of 6

Then loads some visit image information. 
There is an option to change the max number of visit images we use to make the coadd, this is called Q. 30 is a good number so this needs no change. 
Then initialize, create, and view the coadd, then store the .fits file.

Part 2
This creates and appends a "Progress.csv" so that you can track the process of coadd creation and store. a sample of 20 coadds might take 10 hours of runtime.
This step is critical for notebook D. 


D. Morphology 
This notebook is designed to take the custom made coadd, create a cutout and mask on a specific coordinate and region, run statmorph analysis, and append the updated data list to a new "Morphology.csv" file.

Part 1
This takes the "Progress.csv" and inputs the data. it allows the user to itterate through the selected coadd by itterating through the 'n' value, based on the coadd order by ascending tract and patch combination. This 'n' value will have the same 'n' value as in notebook C if this is done correctly. user input is required

USER INPUT, to select the 'n' value of the unique tract and patch combination in ascending order. user should start from 0 and move upward, unless 0 is desired, ie patch (2024,45) out of set [(2024,45)(2024,46)]. this looks like :

## SELECT THE N VALUE OF THE DESIRED TRACT, PATCH COMBINATION
n = 0
#

with output that looks like: 

Coadd number  1  Out of  2
Objects in image:  1

Proceed to check the file being called is the desired image. If this does not work the manual file path input is always an option
load image data

Part 2
This takes the sorted "Key Objects.csv" and allows the user to iterate through each object within the deep coaad image from part 1 based on the 'm' value. the 'm' value represents the current "number" objects with in the specific tract patch combination from the coadd in part 1. if an image has 4 objects, then m can = 0 or 1 or 2 or 3. 

USER INPUT, to itterate through objects of interest from "Key Objects.csv" using the 'm' value, looks like:

## SELECT OBJECT WITHIN THE IMAGE
m = 0
#

and the output looks like : 

Tract  2234 Patch  92
Object  1  Out of  1

Then loads previous "Key Objects.csv" data for later use.
Next we view the coadd, by converting the objects global ra and dec to a local pixel coordinate, and displaying with matlab plots
Then we go on to create the cutout and mask. in this process there is 2 potential user inputs, but they not guaranteed. the user should use their best descresion for finding best values.

POTENTIAL USER INPUT A, to create the cutout we must define a central pixel and cutout dimension. best cutouts are centered on a portion of the desired mask, and have largest possible cutout dimensions without including bright, near objects. it is typical to require a small adjustment to the center pixel values, x_0 and y_0. it is less common to need to adjust the region size, region. looks like: 

###
# PIXEL LOCATION MIGHT NEED TO BE SLIGHT ADJUSTED TO CENTER ON OBJECT MASK
x_0 = x - 0
y_0 = y - 0
# REGION SIZE MIGHT NEED TO BE ADJUSTED
region = 10
###

POTENTIAL USER INPUT B, to create the mask we need to set a detection threshold. for distant objects this threshold can be all over the place. I have used value from between 1.0 and 3.0, but find 1.30 to be a good value most times for very faint objects ~5 pixels wide , and 3.0 to be a good value for objects greater than ~10 pixels wide . looks like:

# THRESHOLD MIGHT NEED TO BE ADJUSTED, GOOD VALUE IS 1.33 OR 1.35
threshold = median + 3.0*std
#

The quick check section gives you the ability to check that this input is correct. the output for quickcheck should be NON ZERO. for example:

Center label: 1

If, Center label: 0 , then you would need to readjust the POTENTIAL USER INPUT variables in ways, A, B, or both: 

#A
## Changeing A might look like 
###
# PIXEL LOCATION MIGHT NEED TO BE SLIGHT ADJUSTED TO CENTER ON OBJECT MASK
#x_0 = x - 2
#y_0 = y - -1
# REGION SIZE MIGHT NEED TO BE ADJUSTED
#region = 8
###

#B
## Changeing B might look like 
###
# THRESHOLD MIGHT NEED TO BE ADJUSTED, GOOD VALUE IS 1.33 OR 1.35
#threshold = median + 2.0*std
###

If Center Label: 1 , or greater than one, then we can move on. 

Part 3 
This section runs the morphology analysis using statmorph. We load morph, compute, and output the measurements. 
Then we compute and save the morphology plots. 
Then we create and or append a new "Final.csv" file that holds the data from rubin lsst dp1, euclid ero, and the statmorph analysis. 

## DONE 

at the end you should have 3 .csv files, 1 with dp1 and ero, one with image data, one with dp1, ero, and statmorph. 
all useful files should be automatically stored under /home/username/KB_462/data # thats YOUR rubin lsst username
