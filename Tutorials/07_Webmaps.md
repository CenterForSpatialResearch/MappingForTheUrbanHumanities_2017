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

[on preparing the historical map] watch

[on preparing the geoJSON data] watch

[going through the code, line by line]



```html
<html>
<head>
	<title>GeoreferencedWebmaps</title>

	<link rel="stylesheet"  href="css/leaflet.css">
	<script src="js/leaflet.js"></script>

	<style>
	#map{ position:absolute; top:0; bottom:0;width: 100%;}
	</style>
</head>
<body>
	<div id="map"></div>

	<script>
	//initializing the map
	var map = L.map('map').setView([40.88,-73.88], 12);

	//tile layers
	L.tileLayer('http://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
		{
			attribution: 'Tiles from <a href="http://www.openstreetmap.org/">OSM</a>',
		maxZoom: 19,
		minZoom: 1
		}).addTo(map)

	
	var imageUrl = 'data/Bronx1902-modified copy-trans.png';
	var imageBounds = [[40.8846829955, -73.8978315922], [40.8290586719, -73.8201512858]]; 

    	
	L.imageOverlay(imageUrl, imageBounds, {opacity: 0.8}).addTo(map);

	L.tileLayer('http://maps.nypl.org/warper/maps/tile/27688/{z}/{x}/{y}.png').addTo(map)


</script>
</body>
</html>
```






______________________________________________________________________________________________________________

Tutorial written by Dare Brawley, for *Mapping for the Urban Humanities*, a intensive workshop for Columbia University faculty taught in Summer 2017 by the [Center for Spatial Research](http://c4sr.columbia.edu). More information about the course is available [here](http://c4sr.columbia.edu/courses/mapping-urban-humanities-summer-bootcamp). It is based on the tutorial prepared by Michelle McSweeney for the Spring 2017 course, *Conflict Urbanism: Language Justice* offered at Columbia Univeristy through the Center for Spatial Research. 