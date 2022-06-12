# Deforestation Mapping
A UNet-based machine learning algorithm which maps deforestation using Sentinel-2 Level 2A multi-spectral images.

This repository contains the scripts referring to a methodology that performs the deforestation mapping using UNets and satellite images from Sentinel-2. The methodology was tested for mapping deforestation spots using images from the Amazon and Atlantic Rainforest biomes, located in Brazil.

## Example Usage
To identify deforestation in areas where UNet has already been trained (Amazon and Atlantic Rainforest), it is possible to directly use the scripts presented in the **deforestation-mapping** folder. Otherwise, it is necessary to carry out a new training using the training files of UNet.

### Training the UNet
**unet/unet/py** has the codes used to carry out UNet training, considering the problem of binary forest/non-forest classification in images from the Sentinel-2 satellite.

**Step 1 (gen_npy_files.py):** Transform Training/Validation Images and Masks Into Arrays

Required folder structure:

```ruby
--Training (define in lines 19-21)
 |__image
 |__label

--Validation (define in lines 67-69)
 |__image
 |__label
 ```
 
 **Step 2 (unet.py):** UNet Training 
 
Parameter inputs must be made on lines 11-49. Attention: at the end of the training, save the 'bands_third.npy' and 'bands_nin.npy' arrays if the deforestation monitoring system will be used.

**Step 3 (test.py):** Post-Training Test

Test images can be applied for evaluation now. 

Required folder structure:

```ruby
--Test
 |__images
 |__predictions (empty)
 |__probabilities (empty)
```

**Step 4 (metrics_calc_test.py):** Calculating Metrics

Calculates metrics for UNet classified images and reference images (masks).

### Using the Mapping Deforestation Script
The scripts for this functionality are in the **deforestation-mapping** folder. To execute the algorithm, use the file **deforestation_main.py**, where some information must be added:

```ruby
# scripts that must be in the same path that this one
from deforestation_mapping import *

# .GEOjson file of the area to be monitored
geojson_file = '/rondonia_square3.geojson'

# path to save the downloaded images
save_imgs = '/Downloaded'

# save RGB files
save_rgb = '/rgb_files'

# save tiles
save_tiles = '/tiles_imgs"

# Unet weights file
unet_weights = "/weights_file_of_trained_UNet.hdf5"

# Unet weights clouds file
unet_clouds = '/weights_file_of_clouds_trained_UNet.hdf5'

# classificated images path
class_path = "/predicted"

# classificated clouds images path
class_clouds = "/predicted_clouds"

# polygons save
poly_path = '/polygons'

# files saved after the trained UNet
percentiles_forest = ["/bands_third.npy",
                       "/bands_nin.npy"]

percentiles_clouds = ["/bands_third_clouds.npy",
                       "/bands_nin_clouds.npy"]

def_main(save_imgs, save_rgb, save_tiles, unet_weights, unet_clouds,
         class_path, class_clouds, poly_path, 
         percentiles_forest, percentiles_clouds, geojson_file)
```

Some settings must also be made in the file **deforestation_mapping.py**, such as credentials for accessing the Sentinel-Hub (user and passwrod) and defining the time period to be covered by the analysis (parameter date):

```ruby
# connect to the API
user = 'USERNAME'
password = 'PASSWORD' 

api = SentinelAPI(user, password, 'https://scihub.copernicus.eu/dhus')

# search by polygon
footprint = geojson_to_wkt(read_geojson(boundsdata))

# search for the images
products = api.query(footprint,
                 date = (["NOW-30DAYS","NOW"]),
                 area_relation = 'IsWithin',
                 platformname = 'Sentinel-2',
                 processinglevel = 'Level-2A',
                 #cloudcoverpercentage = (0, 20)
                )
```

![Image not found](https://github.com/aryaninamdar/Deforestation-Mapping/blob/main/examples/example1-2.png)


## Results
At the end of the algorithm, raster images will be obtained indicating the deforestation spots for the given image, as well as vector files, in the shapefile .shp format, also indicating the deforested areas.
