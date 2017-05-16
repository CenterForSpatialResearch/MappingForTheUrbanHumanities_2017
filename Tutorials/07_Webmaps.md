## Making Webmaps with Leaflet.js

With this exercise you will learn how to create a basic web-based map using the javascript library leaflet.js. This is necessarily not an introduction to javascript, but rather intends to provide familiarity with the components and requirements of publishing maps online. Upon completing this tutorial you should ....

1. Understand how to export GeoJSON files from QGIS
2. Understand the structure of a basic webpage
3. Be able to import a historical map to be shown on the web
4. Create interactive vector features with embedded photos and annotations
5. Experiment with layer styles and map composition
6. Access the NYPL Map Warper archive  

#### The Premise

With this exercise our goal is to create a simple interactive online map that shows the New York Botanical Garden just after it was initially constructed and annotates several imporant buildings and roads at the NYBG. We will use the 1902 Bronx map (that we georeferenced in the previous exercise) as well as some of the features we digitized adding additional annotations. 

For context lets take a look at what this final map will look like: 

******link to map + screenshot*******

To accomplish this we will be using Leaflet.js, an incredibly well documented javascript library with many online easily accessible examples to learn from. Review the [Docs](http://leafletjs.com/reference-1.0.3.html) page for explinations of all of the functions in the leaflet library. 

Leaflet.js is one of many many ways to create webmaps. Others include: 
* OpenLayers
* Neatline (a plugin for Omeka) 
* Mapbox
* Carto
* TileMill
* GeoServer

Should you look into creating webmaps further you'll fine that many of these tools are used together, or build upon each other. 

### To Begin

We have provided the basic folder structure that you will need to create your webmap, in the `Class_Data/3_Webmaps` folder downloaded with this repository. Lets review the structure before we proceed. It contains three folders and one file: 

* `css`
* `data`
* `img`
* index.html
* `js`


In the broadest terms webdevelopment can be understood in the following way: HTML is the structure of a website, CSS is the style, and JavaScript is the functionality or the interaction. Each of these are contained in text files which we have organized into the above folder structure. When you are creating a webpage you are creating a series of linked textfiles that your browser reads. In addition there are folders for images: `img` and `data`. These two folders will contain any images you will use, and the data for the vector annotations as well as the georeferenced historical map. 

#### Preparing your data
For the purposes of this tutorial we have already prepared all of the data for you. However the following steps outline how you would go about exporting data from QGIS to include in an online map if you were starting from scratch. 

**Exporting and Formatting a Georeferenced Historical Map**
1. *Set No Data Value.* Open the `Properties` menu for the georeference historical map. In the Transparency tab enter 0 in the `Additional no data value` field. Click **Apply**. You should notice that the black border around the outside of your image dissapears. 
![instructions image](https://github.com/CenterForSpatialResearch/MappingForTheUrbanHumanities_2017/blob/master/Tutorials/Images/Webmaps/01_NoData.png)
2. *Export Historical Map as a GeoTiff.* Right click on the georeferenced map in the layers panel and select Save as. Then in the save as dialogue box select `Rendered image` as the output mode. Select `GTiff` as the format. Name  your image and save it in the appropriate directory. (You must select `Rendered image` in order to export a version of your map without the black border)
![instructions image](https://github.com/CenterForSpatialResearch/MappingForTheUrbanHumanities_2017/blob/master/Tutorials/Images/Webmaps/01_MapExport.png)
3. *Reducing the File Size of the Map Image*. To make it more managable to load in the browser open the GeoTiff file we just created in the photo editor of your choice, adjust the image size to something more managable and then export as a **.png**. Save this file in the `data` folder of your webmap directory. 

**Exporting Vector Layers as GeoJSON**
Our webmap will rely on a different data format for our spatial data. Up until now we have been working with shapefiles. When we upload our annotations to the web we will be using a data format called GeoJSON. It is a different way to structure spatial and tabular information and it preserves all of the information we have come to expect from shapefiles. 

1. *Export as GeoJSON*. Right click on the layer you want to export in the layers panel. Select Save as. Select `geoJSON` as the file format. Select `WGS 84` (a geographic coordinate reference system) as the CSR. Name  your file and save it in the appropriate directory.
![instructions image](https://github.com/CenterForSpatialResearch/MappingForTheUrbanHumanities_2017/blob/master/Tutorials/Images/Webmaps/03_ExportGeoJSON.png)

#### Building a webmap

Here is the full code for the example listed above. 


```html
<html>
<head>
	<title>GeoreferencedWebmaps</title>

	<!-- <link rel="stylesheet"  href="css/leaflet.css"/> 
	<script src="js/leaflet.js"></script>-->
	<link rel="stylesheet" href="https://unpkg.com/leaflet@1.0.3/dist/leaflet.css" />
	<script src="https://unpkg.com/leaflet@1.0.3/dist/leaflet.js"></script>
	<!-- 
	<script src="js/jquery-2.1.1.min.js">  </script> -->

	<script src="https://code.jquery.com/jquery-2.2.4.min.js"   integrity="sha256-BbhdlvQf/xTY9gja0Dq3HiwQF8LaCRTXxZKRutelT44="   crossorigin="anonymous"></script>

	<style>
	#map{ position:absolute; top:0; bottom:0;width: 100%;}
	</style>

	<script src='data/RoadLines.geojson'></script>


</head>
<body>
	<div id="map"></div>

	<script>
	//initializing the map
	var map = L.map('map').setView([40.88,-73.88], 13);

	//add background tile layers
	L.tileLayer('http://tile.stamen.com/toner-lite/{z}/{x}/{y}.png',
		{
			attribution: 'Tiles from <a href="http://www.openstreetmap.org/">OSM by Stamen Design</a>',
		maxZoom: 19,
		minZoom: 1
		}).addTo(map);

	
	//link to the historical map image 
	var imageUrl = 'data/BronxMap.png';

	//define the area that image covers
	var imageBounds = [[40.8846829955, -73.8978315922], [40.8290586719, -73.8201512858]]; 

	//add georeferenced historical map
	L.imageOverlay(imageUrl, imageBounds, {opacity: 0.8}).addTo(map);


	//load GeoJSON file containing roads, and style lines
  	$.getJSON('data/RoadLines.geojson',function(roadsData){
	    L.geoJson(roadsData, {
	    	color: "#ff7800",
	    	weight: 3.5,
	    	opacity: 0.65,
	    	onEachFeature: road_annon
	    }).addTo(map);
	    });  

  	//define popup content for road annotations
  	var road_annon = function onEachFeature(feature, layer) {
	    if (feature.properties && feature.properties.Descr) {
	    	var roadsPopup = feature.properties.Descr;
	        layer.bindPopup(roadsPopup);
	    }
	}

	// load GeoJSON file containing points
	$.getJSON('data/PointAnnotations.geojson',function(bldg){
		L.geoJson(bldg,{
			onEachFeature: point_annon
	    }).addTo(map);
	});

	//define popup content for point annotations
	var point_annon = function onEachFeature(feature, layer) {
	    if (feature.properties && feature.properties.Descr) {
	    	var pointsPopup = feature.properties.Name + '<br/> <img src="'+ feature.properties.ImgURL + '" width ="300px"/> <br/>' + feature.properties.Descr;
	        layer.bindPopup(pointsPopup);
	    }
	}
	
	  
	//add NYLP Map Warper maps
	L.tileLayer('http://maps.nypl.org/warper/maps/tile/27688/{z}/{x}/{y}.png').addTo(map);


	</script>
</body>
</html>
```






______________________________________________________________________________________________________________

Tutorial written by Dare Brawley, for *Mapping for the Urban Humanities*, a intensive workshop for Columbia University faculty taught in Summer 2017 by the [Center for Spatial Research](http://c4sr.columbia.edu). More information about the course is available [here](http://c4sr.columbia.edu/courses/mapping-urban-humanities-summer-bootcamp). It is based on the tutorial prepared by Michelle McSweeney for the Spring 2017 course, *Conflict Urbanism: Language Justice* offered at Columbia Univeristy through the Center for Spatial Research. 