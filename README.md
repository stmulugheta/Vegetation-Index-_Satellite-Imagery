#Using Satellite Imagery to calculate the Vegetation Index 
Refernce:Parul Pandey 2020.Vegetation Index calculation from Satellite Imagery.https://towardsdatascience.com/vegetation-index-calculation-from-satellite-imagery-6f5c209cbdb1

AI and Machine Learning have revolutionized our world today. From healthcare to automobiles, from sports to banking, almost all the sectors are realizing the power that AI brings with itself to deliver better solutions. The field of Satellite Imagery is also undergoing massive development, and machine learning solutions have provided some positive results. ML algorithms have proved to be quite a boon for analyzing satellite imagery. However, accessing the data becomes a bottleneck sometimes. This is primarily due to the massive size of the satellite images and the fact that analyzing them requires some amount of domain expertise. A lot of times, people from other domains are also interested in accessing and analyzing satellite data, especially to study local patterns like pollution level in a country, flood situations, or even forest fires. This made me look for resources which would ease the process or accessing and analyzing the satellite imagery process so that we could spend more time on understanding the results and learn from them.
A year ago, I wrote an article on a similar concept called Satellite Imagery analysis with Python, where we examined the vegetation cover of a region with the help of satellite data. The idea was to understand the concept of satellite imagery data and how it could be analyzed to investigate real-world environmental and humanitarian challenges.
However, the process entailed several steps, and this time I was looking for options to simplify the process. Fortunately, I came across a platform called EarthAI Notebook, a fully hosted and managed JupyterLab Notebook explicitly designed to analyze raster data. It provided me the ease of performing the satellite imagery analysis with the familiar Jupyter lab platform, having the fully loaded with geospatial analysis libraries and API access to the Earth OnDemand catalog.
In this article, I’ll quickly walk you over the steps that I took to calculate the vegetation index of the place. The code has been heavily adapted from an example code from EarthAI’s manual.
Case Study: Calculating Vegetation Index(NDVI) for the Sunderbans
The Sunderbans is a mangrove area in the delta formed by the confluence of Ganges, Brahmaputra, and Meghna Rivers in the Bay of Bengal. It lies partly in Bangladesh(66%) and India(34%) and is the largest single block of tidal halophytic mangrove forest in the world. Sunderbans have a substantial economic as well as environmental value and have been declared as a World Heritage site by UNESCO.

source
NDVI or the Normalized Difference Vegetation Index (NDVI) is one of the most widely used indexes to measure vegetation. It was developed by NASA scientist Compton Tucker and can be expressed as follows.

where NIR and Red stand for the reflected near-infrared light and reflected visible red light from the plants, respectively. Why only these two colors? Well, it so happens that a healthy plant reflects more NIR and green light as compared to other wavelengths. However, it absorbs more red light. Thus, NDVI compares the reflected near-infrared light(NIR) to reflected visible red(Red) light, by the plants. The NDVI values range from +1.0 to -1.0, where 1 stands for the healthiest vegetation.

The Sundarbans today have been profoundly affected by environmental disturbances and human-made activities as such NDVI can be a useful tool to gauge the vegetation coverage around Sunderbans and take remedial actions.
Setting up the System
Click on the StartFree Trial option and the following screen will greet you. Launch the EarthAI Notebook. You will get a 14-day trial and that should be sufficient to get you started. This might take a few minutes to spinoff the familiar Jupyter Lab environment.

Next, you can navigate to the examples folder and play with the use cases and the quick-reference guide there. I shall be working with the quick-example-1.ipynb file to get acquainted with the platform.
Importing Libraries and Creating a SparkSession
Next, we shall import the necessary libraries and dependencies from EarthAI. This is, in turn, creates a new SparkSession. This includes earth_on_demand, rasterframesand pyspark
from earthai.init import * 
import pyspark.sql.functions as F 
import matplotlib.pyplot as plt  
%matplotlib inline
RasterFrames is a geospatial open-source raster processing library for Python, Scala, and SQL, available through several mechanisms. RasterFrames provides a DataFrame-centric view over arbitrary geospatial raster data, enabling spatiotemporal queries, map algebra raster operations, and interoperability with Spark ML

https://rasterframes.io/
Querying and loading Satellite imagery
We will now need to import the satellite image data. We shall use the earth_ondemand module to query MODIS imagery that covers a particular region of the Sunderbans National Park. Before moving further, let’s understand what MODIS is.
MODIS stands for The Moderate Resolution Imaging Spectroradiometer (MODIS). It is a key instrument aboard the Terra (originally known as EOS AM-1) and Aqua (originally known as EOS PM-1) satellites. Terra MODIS and Aqua MODIS are viewing the entire Earth’s surface every 1 to 2 days, acquiring data in 36 spectral bands, or groups of wavelengths( Source: https://modis.gsfc.nasa.gov/)
The variable catalog returns a dataframe consisting of the satellite images of the specified time and location. Let’s look at some basic information about the catalog.

Here B01 and B02 refer to the red and near-infrared (NIR) bands.
Bands (also called channels or layers) can be thought of as a collection of images taken simultaneously of the same place. This article titled Introduction to Remote Sensing explains the concept of bands in a very lucid way:
Many sensors on earth-observing satellites measure the amount of electromagnetic radiation (EMR) that is reflected or emitted from the Earth’s surface. These sensors, known as multispectral sensors, simultaneously measure data in multiple regions of the electromagnetic spectrum. The range of wavelengths measured by a sensor is known as a band and are commonly described by the name (Red or Near-IR for example) and the wavelength of the energy being recorded.
These columns provide direct links to the single-band GeoTIFFs. Geographic information systems use GeoTIFF and other formats to organize and store gridded raster datasets such as satellite imagery.
Let’s look at some of the key columns from the catalog above.
catalog[['id','eod_collection_attribution', 'datetime', 'eo_cloud_cover', 'proj_crs', 'B01', 'B02']].head(5)

Loading Imagery from Catalog
Now, its time to load the actual satellite imagery. We will read in the red(B0) and infrared(B1) bands from the list of scenes specified above.
df is a Spark DataFrame where the imagery is stored as tiles

Calculating NDVI
df = df.withColumn('ndvi', rf_normalized_difference('nir', 'red'))
Compute the mean of NDVI across the entire image for each week
Finally, we will compute a very simple analytic. We will compute the mean of NDVI across the whole image for each week in the DataFrame. The time_series is a Spark DataFrame. The toPandas call triggers the computation, and the results are returned in a pandas DataFrame. This enables us to visualize the time series easily.

So here we get the NDVI for the specified time period. I took a very small time window to speed up the process, but the entire process remains the same even if we need to calculate the NDVI for a more extended period. That would infact, give a better idea about the ecological balance and the impact of climate change over a place.
Conclusion
Satellite imagery analysis is a must-have tool in a Data Scientist toolkit, and the unearthed information can be used for investigating real-world environmental and humanitarian challenges. I hope this article would give you a headstart, and you will start experimenting with the tools and satellite data to perform some valuable analysis.
P.S: This isn’t a promotional article for EarthAI. There are other several tools present to do the same analysis, but my experience with this platform was a positive one and so I thought of sharing my experience.
