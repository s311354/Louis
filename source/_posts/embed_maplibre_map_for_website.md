---
title: Embed MapLibre Map for Website
date: 2022-09-28
categories:
- louissrliu
- features
tags:
- javascript
- map
toc: true
language: en
thumbnailImagePosition: left
thumbnailImage: https://d3p3tllh95j94n.cloudfront.net/wellcome-to-louissrliu/forest.jpeg
---

In our daily life, most of us usually use [Google Map][gmap] or [Apple Maps][amap] to help us to navigate to a place. We can see traffic info, public transit and also choose our mode of transportation. 

<!-- more -->

Recently, I was looking for ways to embed a Map to my website that contains multiples of custom locations. There are varieties of technologies to choose, such as [Amaxon location service][amazonlocation], [Google Map API][googlemap] and [MapLibre GL JS][maplibrejs], etc. In this post, I would like to discuss how to use [MapLibre GL JS][maplibrejs] to embed and create custom dynamic maps on our websites mostly for free.

### MapLibre GL JS ###

[MapLibre GL JS][maplibrejs] is an open-source library for publishing maps on your websites. The maps uses vector tileset source over GeoJSON data soruces when possible and are fast rendering in browser has become possible thanks to GPU. 

### Quick Start ###

Before begining shortly, we have to register an account in [Maptiler][maptiler], get an default API Key to protect which is used for accessing maps, tiles, and data from your account. Then, using the API key in code is shown below.

{% codeblock %}
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8" />
<title>Add a default marker</title>
<meta name="viewport" content="initial-scale=1,maximum-scale=1,user-scalable=no" />
<script src="https://unpkg.com/maplibre-gl@2.4.0/dist/maplibre-gl.js"></script>
<link href="https://unpkg.com/maplibre-gl@2.4.0/dist/maplibre-gl.css" rel="stylesheet" />
<style>
	body { margin: 0; padding: 0; }
	#map { position: absolute; top: 0; bottom: 0; width: 100%; }
</style>
</head>
<body>
<div id="map"></div>
 
<script>
var map = new maplibregl.Map({
    container: 'map',
    style:
    'https://api.maptiler.com/maps/streets/style.json?key=YOUR_OWN_MAPTILER_API',
    center: [12.550343, 55.665957],
    zoom: 8
});
 
var marker = new maplibregl.Marker()
    .setLngLat([12.550343, 55.665957])
    .addTo(map);
</script>
 
</body>
</html>
{% endcodeblock %}

Futher copy the code, paste it into your favorite text editor, and save it as .html file. (remember to replace YOUR_OWN_MAPTILER_API with your actual API key). Next, we can ues iframe for integration with the post.

{% codeblock %}
<div style="overflow-x:auto;">
    <iframe src="/javascript/maplibre/add-default-marker.html" style="width:100%; height:512px", title="add-default-marker"></iframe>
</div>
{% endcodeblock %}

### Simple Example ###

The embedded map for this simple example looks like this.

<div style="overflow-x:auto;">
    <iframe src="/javascript/maplibre/add-default-marker.html" style="width:100%; height:512px", title="add-default-marker"></iframe>
</div>

### Custom Example ###

We can also customize the marker to a Map. Similar to the above example, I created another custom example map with marked places where I have visited recently nearby.

<div style="overflow-x:auto;">
    <iframe src="/javascript/maplibre/add-custom-marker.html" style="width:100%; height:512px", title="add-custom-marker"></iframe>
</div>

Now, we know how to simply embed the custom dynamic maps on our websites. Hope this post will be helpful.

## Reference ##

+ [Google Maps Platform](https://developers.google.com/maps)

+ [Open-source library - MapLibre GL JS](https://github.com/maplibre/maplibre-gl-js)

+ [Mapbox Docs - vector tiles](https://docs.mapbox.com/help/glossary/vector-tiles/)

+ [Mapbox Docs - Improve the performance of Mapbox GL JS maps](https://docs.mapbox.com/help/troubleshooting/mapbox-gl-js-performance/)

[gmap]:https://www.google.com/maps/ "https://www.google.com/maps/"

[amap]:https://www.apple.com/maps/ "https://www.apple.com/maps/"

[maplibrejs]:https://github.com/maplibre/maplibre-gl-js "https://github.com/maplibre/maplibre-gl-js"

[googlemap]:https://developers.google.com/maps "https://developers.google.com/maps"

[amazonlocation]:https://aws.amazon.com/location/?nc1=h_ls "https://aws.amazon.com/location/?nc1=h_ls"

[maptiler]:https://www.maptiler.com/ "https://www.maptiler.com/"

<p>Thanks for reading! Feel free to leave the comments below or <a href="mailto:shirong0419@icloud.com">email</a> to me. Any pieces of advice or discussions are always welcome. :)</p>
