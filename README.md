## Mapping mobile homes in Arizona for heat resilience

### Create a new project - Basic Project Information

**Project Topic:** Mapping mobile homes  
**Project Type:** Build Area  
**Project Region:** Arizona, USA  
**Requesting Organisation:** ASU  
**Visibility:** Public  
**Look for:** mobile homes  
**Tutorial:** AZ mobile home mapping - 20220727  
**Project Details:**  `**The mapping** \n\n\n Help us look for "mobile homes" including both manufactured homes and recreational vehicle (RV). Please take the tutorial to learn how to identify these features. \n\n\n **The data** \n\n\n Data will be used by researchers at Arizona State University and partners like AAMHO, the City of Phoenix and SRP to design solutions for heat resilience among mobile home owners across the state. People who live in this type of housing are 6 times more likely to suffer heat related deaths. We will better understand the nature and extent of the problem and identify precise cost and locations of where to activate support, energy innovations, and housing weatherization in order to prevent hundreds of fatalities each year.`  
**Verification Number:** 5  
**Group Size:** 44  
_(note: group size is not the number of screens but the number of tiles in x direction, so when you set it to 44 it will be 44/2 = 22 screens)_


### GeoJSON in the wrong projection?

If the GeoJSON area of interest for your project isn't rendering correctly on the web (e.g. throwing an error in the MapSwipe project creation interface or displaying incorrectly when uploaded to https://geojson.io/), it might be in the wrong projection. If you look at the file in a text editor, the `FeatureCollection` should have a `crs` property. The original GeoJSON's I received had this:
```json
"crs": {
  "type": "name",
  "properties": { "name": "EPSG:6405" }
},
```

They needed to be in EPSG 4326. There were 50 of them and I did't want to convert them 1-by-1 in QGIS or go back to the project team and ask for them in a different projection. Luckily, GDAL is a nifty open source translator library for raster and vector geospatial data formats that includes [ogr2ogr](https://gdal.org/programs/ogr2ogr.html), a program for converting vector data between file formats.  

It requires some comfort with the terminal but is quite powerful. To use this tool on macOS:
- install [Homebrew](https://docs.brew.sh/Installation)
- install GDAL with the command: `brew install gdal`

To transform your GeoJSON files into a different projection, `cd` into the directory with the GeoJSON files. For a single file (change "output" and "input" to the names of your files):
```bash
ogr2ogr output.geojson -t_srs "EPSG:4326" -s_srs "EPSG:6405" input.geojson
```

For a folder of files (you can change the "4326_" to a prefix of your choice for the transformed files):
```bash
for file in *.geojson; do
  ogr2ogr 4326_"$file" -t_srs "EPSG:4326" -s_srs "EPSG:6405" "$file"
done
```
