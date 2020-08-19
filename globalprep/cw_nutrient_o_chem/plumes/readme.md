## Ocean Health Index: Plume model 

Here I will document how to run the plume model. 

 - You will need to download this folder (cw_nutrient_o_chem/plumes) to your local mazu drive. I.e. /home/sgclawson/
 - Go ahead and install the anaconda installer for 68 bit linux from https://www.anaconda.com/products/individual and throw the file into your home directory on mazu (or Aurora if that is what you use).
 - In your terminal, ssh into mazu.. i.e. `ssh sgclawson@mazu.nceas.ucsb.edu` and enter your password
 - Create a folder in your "anaconda3/envs" folder named "py2", this will be your python environment
 - Type `conda activate py2` in your terminal. This will activate this py2 environment and act as your python environment. 
 - Install gdal by typing `conda install -c conda-forge gdal` to install gdal in your python environment. 
 - Create a folder in your mazu home drive entitled "grassdata" or something of the like.
 - Follow the steps outlined below, updating file paths as needed: 
 
 ```
 ## this is all done in the shell

## After creating a new python env, I.e. py2: 
conda activate py2

cp /home/shares/ohi/git-annex/globalprep/cw_nutrient_o_chem/raw/ocean_mask_landnull.tif /home/sgclawson/grassdata/ ## copy the ocean mask to your grassdata folder

rm -r /home/sgclawson/grassdata/location # replace sgclawson with your home directory name

grass -c ~/grassdata/ocean_mask_landnull.tif ~/grassdata/location ## start a grass session and create a location folder where grass will run 

exit ## exit grass, and copy ocean_mask_landnull.tif to PERMANENT folder, located in location folder
cp /home/sgclawson/grassdata/ocean_mask_landnull.tif /home/sgclawson/grassdata/location/PERMANENT/ 

grass ## enter grass again

## download the plumes folder that you are currently in
## look in the plumes/ folder and edit run_plumes.sh for correct file paths 

# Load ocean mask null into grass session 
r.in.gdal /home/sgclawson/grassdata/location/PERMANENT/ocean_mask_landnull.tif output='ocean' ## if you used a different name or different ocean file, change it to that here

# Load pour points into grass session # This is just ten pourpoints from Australia as a test
v.import /home/shares/ohi/git-annex/globalprep/cw_nutrient_o_chem/int/pourpoints/PO4eq_pourpoints.shp output='pours' ## change PO4eq_pourpoints to your pourpoints shapefile that you want to run the plume model on

# Within your grass session, cd into plumes/ dir
cd /home/sgclawson/wastewater-master/code/plumes ## this will likely look like this for you: "/home/name/plumes"

## Now run run_plumes.sh
sh run_plumes.sh ## test it on a subset of pourpoints before you run the entire thing. The entire thing will likely take 3-4 days to run, and if it is not working you don't want to waste your time without testing it. 

## Now we need to combine these rasters into one using mosaic code: 
## Split the plume output .tifs (this depends on you machine but is required on Mazu @ NCEAS) 
## and mosaic them together 
## Code come from moasic.sh in the plumes dir
 
# cd output
# mkdir subsets
# for i in  1 2 3 4 5 6 
## the number of i's depending on how many plume_effluent.tif files were created. We will  subset in batches of 10000, so for instance, I had 130000 .tif files, so i ran my for loop for i in 1:13

# do
#    printf "Starting $i \n"
#    mkdir subsets/subset$i
   
#    # move the tif files in batches of 10000
#    mv `ls | head -10000` subsets/subset$i/
   
#    # mosaic subset 
#    cd subsets/subset$i/
#    ../../../gdal_add.py -o global_effluent_2015_tot_N_sub$i.tif -ot Float32 plume_effluent_*.tif # ALWAYS UPDATE tif NAME to whatever you are running.. for instance, if running a test on australian plumes, make it akin to "global_PO4eq_au$i.tif"
#    printf "subset $i tif done \n"
   
#    # move subset mosaic and go up
#    mv global_effluent_2015_tot_N_sub$i.tif ../ # ALWAYS UPDATE tif NAME
#    cd ../../
#    pwd
#    printf "\n Ending $i \n"
# done
# printf "Done Subsets \n"

# # final mosaic
# cd subsets
# pwd
# ../../gdal_add.py -o global_effluent_2015_tot_N.tif -ot Float32 global_effluent_2015_tot_N*.tif # ALWAYS UPDATE tif NAME

# printf "\n Final Tif Done"
# # move final tif
# cp global_effluent_2015_tot_N.tif home/shares/ohi/git-annex/globalprep/cw_nutrient_o_chem/output/N_effluent_output/ # ALWAYS UPDATE tif NAME
```

