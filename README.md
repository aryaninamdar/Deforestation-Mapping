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
