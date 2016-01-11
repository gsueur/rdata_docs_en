Services provided by GrandLyon Data
=======================================

The GrandLyon data platform allows you to connect to several consultation services of Open Datasets. The services are of two kinds : view services making you display images (maps) and download services allowing you to directly retrieve the raw data, in various formats and ways. 

When building up an online mapping application, one can not deal the same way with all the data. There is indeed data for background (base maps), data for adding contextual information (various points of interest such as stations, a city hall...) and finally data holding the important information, the one you really want to show up and emphasize in your application (bike availability, real time bus locations...)

When the base map is displayed as a non-interactive image format, and often obtained from a tiled cache system providing fast access to small tiles (256 x 256 pixels) drawn once for all, it will always be the lower layer in the application, the one below all the others, for obvious visibility reasons. 

The important, critical information, bearing the application value-added will be the most interactive. So that a simple mouse-over opens a tooltip with the essential information, a single click opens a full notice, a dynamic change of style (size, color) underlines a selection, an association, a relation with another element of the map. In order to achieve that, one has to use a download service, to be able to transmit the raw data allowing the building of a vectorial layer in the mapping application, or to give access to all the associated attributes directly. 
For the other data layers, arbitration is mandatory. Because we can't load all the data in vectorial mode for both readability  (too many points/lines blinking, bolding or shaking at the same time make the map useless) and performances reasons (every single coordinate is written in the page object model. Several thousands points are too heavy to be rendered smoothly by the browser). So we deal. We use view services (WMS, image format) for the datasets in which the location is the most important (for instance for disabled parking spaces, there is not much to say in a tooltip, one just has to know where it is, and displaying it is enough) or in which the spatial extent has a special point (park or restricted area border). And we choose the vectorial format for some spots closely related to the principal subject of the map, but having precious information associated to it (bike stands, events...)


WMS Service
-----------
The WMS service is the view service of reference. It is used to "see" the geographical data with a predefined styling (colors, symbols, sizes). It is the preferred service for the integration of a dataset into an image format. 
It is accessible from the following URL : 
https://download.data.grandlyon.com/wms/[name_of_the_service]

One generally uses its GetCapabilities operation to know its precise content (layers list) : 

https://download.data.grandlyon.com/wms/grandlyon?SERVICE=WMS&REQUEST=GetCapabilities&VERSION=1.3.0

sends back an XML document listing (among others) the layers published by the service, from which you'll get the content with the help of a GetMap requ├¬st of this type : 

https://download.data.grandlyon.com/wms/grandlyon?LAYERS=adr_voie_lieu.adrcommune&FORMAT=image%2Fpng&EXCEPTIONS=application%2Fxml&TRANSPARENT=TRUE&VERSION=1.1.1&SERVICE=WMS&REQUEST=GetMap&STYLES=&SRS=EPSG%3A4171&BBOX=4.7,45.6,5,45.9&WIDTH=720&HEIGHT=780

It can seem a bit complicated and tedious. But cartographic libraries are here to help. Leaflet and OpenLayers implement WMS classes which will do these operations for you, adapting the requests to the manipulations made on the map. You just have to specify the service base URL and the name of the layer you want to use as our examples illustrate. 


WFS Service
-----------
The WFS Service allows you to retrieve the raw data as it is recorded in the database. It is a download service, even if this download (the action of retrieving the data) is not necessarily full, as one can only download a part of the territory, mainly for the biggest datasets. 
It is accessible from the URL :
https://download.data.grandlyon.com/wfs/[name_of_the_service]

One generally uses its GetCapabilities operation to know its precise content (layers list) : 

https://download.data.grandlyon.com/wfs/grandlyon?SERVICE=WFS&REQUEST=GetCapabilities&VERSION=1.1.0

sends back a XML document listing (among others) the available layers in the service, from which you'll get the content with the help of the GetFeatures operation : 

https://download.data.grandlyon.com/wfs/grandlyon?SERVICE=WFS&REQUEST=GetFeature&typename=pvo_patrimoine_voirie.pvotronconwebcriter&VERSION=1.1.0

But here again, be reassured, the cartographic libraries have all the necessary classes for a very simple usage of this type of service. 
The generally used format in WFS is GML (which stands for Geographic Markup Language), an XML derivative. However, this format is not the easiest to use in the context of a web application. Thus, while using WFS, it is still possible to receive a GeoJSON formatted stream by adding the OUTPUTFORMAT=geojson parameter : 

https://download.data.grandlyon.com/wfs/grandlyon?SERVICE=WFS&REQUEST=GetFeature&typename=pvo_patrimoine_voirie.pvotronconwebcriter&VERSION=1.1.0&OUTPUTFORMAT=geojson

WCS Service
-----------
The WCS Services (Web Coverage Service) allow you to directly retrieve the raw data of raster layers (like orthophotographies or DEM). The word Coverage matches the raster dataset. So it is indeed a download service applied to image datasources in which it is possible to filter the data to retrieve for only a part of the territory : 

It is accessible from the URL :
https://download.data.grandlyon.com/wcs/[name_of_the_service]

As for WMS and WFS, one can use the GetCapabilities operation to know its content (available layers list) : 

https://download.data.grandlyon.com/wcs/grandlyon?service=WCS&request=GetCapabilities&version=1.1.0

sends back a XML document listing (among others) the available layers provided by the service, which you'll get the description of with the help of a DescribeCoverage operation : 

https://download.data.grandlyon.com/wcs/grandlyon?service=WCS&request=DescribeCoverage&version=1.1.0&identifiers=Ortho2009_vue_ensemble_16cm_CC46,1830_5155_16_CC46

The returned information concerns only the specified layers by the identifier parameter (here Ortho2009_vue_ensemble_16cm_CC46 and 1830_5155_16_CC46) and are a little bit more accurate than in the GetCapabilities. 

Finally, to obtain the desired coverage, one uses a GetCoverage request of this type : 

https://download.data.grandlyon.com/wcs/grandlyon?service=WCS&BBOX=1830000,5155000,1830100,5155100&request=GetCoverage&version=1.1.0&format=image/tiff&crs=EPSG::3946&identifiers=1830_5155_16_CC46

Once again, it is a standardized service for which the cartographic libraries provide all the necessary classes and operation for a sipmle and efficient usage. 

REST Services (JSON)
-----------------------
The JSON service of our infrastructure allows an easy and fast navigation between the several datasets provided. Each service has a dedicated entry point :

https://download.data.grandlyon.com/ws/grandlyon/all.json

and 

https://download.data.grandlyon.com/ws/rdata/all.json

The documents list all the available tables both in consultation and download. Some of them can have a restricted access depending on your rights. 

From link to link, you can then navigate towards the tables description (for instance https://download.data.grandlyon.com/ws/grandlyon/fpc_fond_plan_communaut.fpcplandeau.json), the different predefined values used in a specific field (for instance the type of trees in Greater Lyon : https://download.data.grandlyon.com/ws/grandlyon/abr_arbres_alignement.abrarbre/essencefrancais.json). This last mode provides a few more options : 

* compact : if false, gives a (key,value) result for all the records, else, only lists the different values found in the whole table. Default is True. 

* maxfeatures : indicates the maximal number of records to be returned by the service. Default is 1000. 

* start : indicates the start index, in order to paginate the results. Default is 1. 

Thus, one can request the service for 50 kinds of trees from the 100th in the database (which can sound useless however):

https://download.data.grandlyon.com/ws/grandlyon/abr_arbres_alignement.abrarbre/essencefrancais.json?compact=false&maxfeatures=50&start=101


One can also reach the full content of a table (or paginate this content) using a URL such this one :

https://secure.grandlyon.webmapping.fr/ws/rdata/jcd_jcdecaux.jcdvelov/all.json?compact=false

to consult the integrality of the records. In this all.json mode which dislays individual records, compact flag is always false. All.json also contains complementary information about pagination. It does include two links towards previous and next page, using the same maxfeature and adapting the start parameter from the current page.  

The REST-JSON services are thus particularly adapted to the construction of values lists, tables and paginated grids, inside datasets GUI. 

OSM Service (OpenStreetMap)
---------------------------

The Data platform provides a base map tiled service build from the `OpenStreetMap <openstreetmap.fr>`_ database on Rhône-Alpes Region. It is found at the URL :

http://openstreetmap.data.grandlyon.com

.. image:: http://openstreetmap.data.grandlyon.com/?LAYERS=osm_grandlyon&SERVICE=WMS&VERSION=1.1.1&REQUEST=GetMap&STYLES=&EXCEPTIONS=application%2Fvnd.ogc.se_inimage&FORMAT=image%2Fjpeg&SRS=EPSG%3A4326&BBOX=4.8484037210919,45.764534434461,4.8548554273902,45.770986140759&WIDTH=256&HEIGHT=256
   :alt: GrandLyon Data : OSM Service
   :class: floatingflask

The name of the layer to be used is simply osm_grandlyon. The layer is available in the following projection systems :

* ESPG:3857 and EPSG:900913 (Spherical Mercator)

* EPSG:4326 (WGS84)

* EPSG:4171 (RGF93)

Please note that these two last systems are degrees defined and not meters defined. Their usage to make a map (instead of accessing the data) leads to a visual result which is kind of crushed, which is completely normal as you are in fact projecting spherical geographic coordinates on a plane surface, being the screen or a file. This projection is named `plate-carrée <https://en.wikipedia.org/wiki/Equirectangular_projection>`_).

