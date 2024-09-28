# Background
This github provides a guide for extracting annual human footprint index values from Google Earth Engine. Extractions are made at a 1km resolution within a specific area and are available from 2000-2023. 

## Human footprint datasets
The Human Footprint Index is a comprehensive measure of human influence on the natural environment across the globe. The index utilizes a range of data sets to quantify the extent of human impact on terrestrial ecosystems. This data includes variables such as population density, land transformation (e.g., urban development, agriculture), human access (e.g., roads, railways, navigable rivers), and infrastructure (e.g., electrical power). The index produces a global map where human impact is rated on a scale from 0 intact ecosystem to 50 intense human pressure. A recent study has produced this metric annual for 2000-2020 at a 1km resolution. 

A number of different human footprint datasets are available ([1](https://www.frontiersin.org/journals/remote-sensing/articles/10.3389/frsen.2023.1130896/full), [2](https://iopscience.iop.org/article/10.1088/1748-9326/abe00a/meta), [3](https://www.cell.com/one-earth/fulltext/S2590-3322(20)30418-8), [4](https://www.nature.com/articles/ncomms12558). These can differ slightly in 1) how the index is calculated, 2) the spatial resolution calculated, 3) the temporal resolution calculated. Here we use the [Mu et al. 2022 (5)](https://www.nature.com/articles/s41597-022-01284-8) dataset which is calculated annual between 2000 and 2023, at a 1km resolution globally.

## Google Earth Engine Code
Before the extractions can happen, you will need to download each year as a tif from the ["Data Records section"](https://figshare.com/articles/figure/An_annual_global_terrestrial_Human_Footprint_dataset_from_2000_to_2018/16571064) in Mu et al 2022, and then upload this as an asset in your [Google Earth Enginge Code Editor](https://code.earthengine.google.com/). 

This code is extracting annual human footprint index for all municipalites in Brazil from 2006 to 2020 and was written by Aly Singleton. Some things to consider about this code and modify how you see fit for your purposes;
 -1. The code extracts the median human footprint index for each municipality. This is because some municipalities have a large area, most of which may be forested with a small portion that is urbanised. If we average across that space the highly urban areas may draw the human footprint index up representing agricultural areas, where-as in reality the most common value in the municipality is forested areas (human footprint 4 or less). Depending on your research question, you may also be interested in the maximum, minimum or mean value in a given area. 
 -2. In this code we were interested in extracting the number of pixels above a human footprint of 8 (a hypothesized threshold of dengue transmission). This is probably not relevant to your research question, but I have left this in so you can see how you might make further calculations or extractions with human footprint in GEE. 

```javascript
// load and check the hfp tif from 2000
var hfp2000 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2000');
print(hfp2000);
Map.addLayer(hfp2000);

// load all other years
var hfp2001 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2001');
var hfp2002 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2002');
var hfp2003 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2003');
var hfp2004 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2004');
var hfp2005 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2005');
var hfp2006 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2006');
var hfp2007 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2007');
var hfp2008 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2008');
var hfp2009 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2009');
var hfp2010 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2010');
var hfp2011 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2011');
var hfp2012 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2012');
var hfp2013 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2013');
var hfp2014 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2014');
var hfp2015 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2015');
var hfp2016 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2016');
var hfp2017 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2017');
var hfp2018 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2018');
var hfp2019 = ee.Image('projects/gbsc-gcp-lab-emordeca/assets/hfi_yearly/hfp2019');

// combine into image collection
var hfp_yearly = ee.ImageCollection([hfp2000, hfp2001, hfp2002, hfp2003, hfp2004, hfp2005, hfp2006,
                                     hfp2007, hfp2008, hfp2009, hfp2010, hfp2011, hfp2012, hfp2013,
                                     hfp2014, hfp2015, hfp2016, hfp2017, hfp2018, hfp2019]);

print(hfp_yearly);

// clip to brazil and download for each municipality
var brazil_municipalities = ee.FeatureCollection('users/asinglet/BR_Municipios_2020');
//print(brazil_municipalities);
//Map.addLayer(brazil_municipalities);

hfp_yearly = hfp_yearly.toBands()
                       .rename(['hfp2000', 'hfp2001', 'hfp2002', 'hfp2003', 'hfp2004', 'hfp2005', 'hfp2006',
                                'hfp2007', 'hfp2008', 'hfp2009', 'hfp2010', 'hfp2011', 'hfp2012', 'hfp2013',
                                'hfp2014', 'hfp2015', 'hfp2016', 'hfp2017', 'hfp2018', 'hfp2019']);
print(hfp_yearly);

//////////////////////////////////////
// calculate median hfi for each municipality for each year
//////////////////////////////////////

var featureValues = hfp_yearly.reduceRegions({
  collection: brazil_municipalities,
  reducer: ee.Reducer.median(), 
  scale: 1000 
});

//////////////////////////////////////
// calculate number of pixels above threshold in each municipality
//////////////////////////////////////

// define threshold value
var threshold = 8;

var hfp_yearly_threshold_binary = hfp_yearly.gte(threshold);
print(hfp_yearly_threshold_binary);

var featureValues = hfp_yearly_threshold_binary.reduceRegions({
  collection: brazil_municipalities,
  reducer: ee.Reducer.sum(), 
  scale: 1000 
});


//////////////////////////////////////
// Area of each municipality (turns out this is already in the exported columns)
//////////////////////////////////////
var calculateArea = function(feature) {
  // Calculate area in square meters
  var area = feature.geometry().area();
  // Return a feature with the area property added
  return feature.set('area_sqm', area);
};
// Map the function over the FeatureCollection
var areaAddedCollection = brazil_municipalities.map(calculateArea);
print(areaAddedCollection);

//////////////////////////////////////
// export variable of interest to drive
//////////////////////////////////////
Export.table.toDrive({
  collection: areaAddedCollection,
  description: 'brazil_municipality_areas',
  folder: 'GEEexports', //change to your personal drive folder
  fileFormat: 'CSV'
});
```
## Explaination of key terms in GEE

1. ImageCollection
An ```ImageCollection``` is a set of images, often representing a time series of satellite data. In this code, we load individual annual Human Footprint Index (HFI) images for each year from 2000 to 2019 and combine them into an ```ImageCollection```. This structure allows us to handle multiple images more efficiently, especially for operations that need to be applied across all years.

Why Convert an ImageCollection to an Image?
When working with ```ImageCollection```, it is sometimes necessary to convert it into a single Image, for example, to export data or reduce regions. By converting it into an ```Image```, we can treat all the layers as separate bands in one file, simplifying further operations like thresholding or calculating statistics.

```
var hfp_yearly = hfp_yearly.toBands()
```

2. Image
An ```Image``` is a single raster image, where each pixel represents some data value, such as the Human Footprint Index. In this script, we load multiple years of HFI data as individual images. When they are combined into an ```ImageCollection```, each year is essentially treated as a new band in the final Image.

3. map() Function
The ```map()``` function in Earth Engine is used to apply a function to each feature in a ```FeatureCollection``` or each pixel in an ```ImageCollection```. In this code, ```map()``` is used to calculate the area of each municipality by applying the ```calculateArea``` function to every feature in the brazil_municipalities collection.

```
var areaAddedCollection = brazil_municipalities.map(calculateArea);
```

4. gte() Function
```gte()``` stands for "greater than or equal to" and is used to compare pixel values. In this case, it is used to create a binary ```Image``` where pixels with a Human Footprint Index greater than or equal to the threshold value (8 in this example) are assigned a value of 1, and all others are assigned 0. This allows for counting the number of pixels exceeding the threshold for each municipality.

```
var hfp_yearly_threshold_binary = hfp_yearly.gte(threshold);
```

5. reduceRegions()
The ```reduceRegions()``` function applies a reducer (like sum, median, etc.) to each feature in a ```FeatureCollection``` over the corresponding area of an ```Image``` or ```ImageCollection```. In this code, it calculates the median Human Footprint Index for each municipality and the sum of pixels that exceed the threshold within each municipality.

```
var featureValues = hfp_yearly.reduceRegions({
  collection: brazil_municipalities,
  reducer: ee.Reducer.median(), 
  scale: 1000 
});
```

## References
1. Gassert, F., Venter, O., Watson, J. E., Brumby, S. P., Mazzariello, J. C., Atkinson, S. C., & Hyde, S. (2023). An operational approach to near real time global high resolution mapping of the terrestrial Human Footprint. Frontiers in Remote Sensing, 4, 1130896.
2. Keys, P. W., Barnes, E. A., & Carter, N. H. (2021). A machine-learning approach to human footprint index estimation with applications to sustainable development. Environmental Research Letters, 16(4), 044061.
3. Williams, B. A., Venter, O., Allan, J. R., Atkinson, S. C., Rehbein, J. A., Ward, M., ... & Watson, J. E. (2020). Change in terrestrial human footprint drives continued loss of intact ecosystems. One Earth, 3(3), 371-382.
4. Venter, O., Sanderson, E. W., Magrach, A., Allan, J. R., Beher, J., Jones, K. R., ... & Watson, J. E. (2016). Sixteen years of change in the global terrestrial human footprint and implications for biodiversity conservation. Nature communications, 7(1), 12558.
5. Mu, H., Li, X., Wen, Y., Huang, J., Du, P., Su, W., ... & Geng, M. (2022). A global record of annual terrestrial Human Footprint dataset from 2000 to 2018. Scientific Data, 9(1), 176.
