# WebMap
Map interactive des Points d'Apport Volontaire sur le territoire de la CCCE

<!doctype html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="initial-scale=1,user-scalable=no,maximum-scale=1,width=device-width">
        <meta name="mobile-web-app-capable" content="yes">
        <meta name="apple-mobile-web-app-capable" content="yes">
        <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.3/dist/leaflet.css"
             integrity="sha256-kLaT2GOSpHechhsozzB+flnD+zUyjE2LlfWPgU04xyI="
             crossorigin=""/>
		<link rel="stylesheet" href="css/L.Control.Basemaps.css" />
        <link rel="stylesheet" href="css/qgis2web.css"><link rel="stylesheet" href="css/fontawesome-all.min.css">
        <link rel="stylesheet" href="css/leaflet-search.css">
        <link rel="stylesheet" href="css/leaflet-control-geocoder.Geocoder.css">
        <link rel="stylesheet" href="http://code.ionicframework.com/ionicons/1.5.2/css/ionicons.min.css">
        <link rel="stylesheet" href="css/leaflet.awesome-markers.css">
        <link rel="stylesheet" href="css/MarkerCluster.css">
        <link rel="stylesheet" href="css/MarkerCluster.Default.css">
        <style>
        html, body, #map {
            width: 100%;
            height: 100%;
            padding: 0;
            margin: 0;
        }
        </style>
        <title></title>
    </head>
    <body>
        <div id="map">
        </div>
        <script src="js/qgis2web_expressions.js"></script>
        <script src="https://unpkg.com/leaflet@1.9.3/dist/leaflet.js"
            integrity="sha256-WBkoXOwTeyKclOHuWtc+i2uENFpDZ9YPdf5Hf+D7ewM="
            crossorigin=""></script>
        <script src="js/leaflet.rotatedMarker.js"></script>
        <script src="js/leaflet.pattern.js"></script>
        <script src="js/leaflet-hash.js"></script>
        <script src="js/Autolinker.min.js"></script>
        <script src="js/rbush.min.js"></script>
        <script src="js/labelgun.min.js"></script>
        <script src="js/labels.js"></script>
        <script src="js/leaflet-control-geocoder.Geocoder.js"></script>
        <script src="js/leaflet-search.js"></script>
		<script src="js/L.Control.Basemaps.js"></script>
        <script src="js/leaflet.awesome-markers.js"></script>
        <script src="data/PAV2023_5.js"></script>
        <script src="js/leaflet.markercluster.js"></script>
        <script>

        var map = L.map("map").setView([49.460059, 6.217533], 12);
		var hash = new L.Hash(map);
        map.attributionControl.setPrefix('<a href="https://github.com/tomchadwin/qgis2web" target="_blank">qgis2web</a> &middot; <a href="https://leafletjs.com" title="A JS library for interactive maps">Leaflet</a> &middot; <a href="https://qgis.org">QGIS</a>');
        var autolinker = new Autolinker({truncate: {length: 30, location: 'smart'}});
        var bounds_group = new L.featureGroup([]);
        function setBounds() {
            if (bounds_group.getLayers().length) {
                map.fitBounds(bounds_group.getBounds());
            }
        }
            // Ajout des 3 fonds de cartes 
            var basemaps = [
                L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
                    attribution:
                        'Map tiles by <a href="http://stamen.com">Stamen Design</a>, <a href="http://creativecommons.org/licenses/by/3.0">CC BY 3.0</a> &mdash; Map data &copy; <a href="http://openstreetmap.org">OpenStreetMap</a> contributors, <a href="http://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>',
                    subdomains: "abcd",
                    maxZoom: 20,
                    minZoom: 0,
                    label: "Toner Lite"
                }),
                L.tileLayer("http://{s}.tile.stamen.com/toner-lite/{z}/{x}/{y}.png", {
                    attribution:
                        'Map tiles by <a href="http://stamen.com">Stamen Design</a>, <a href="http://creativecommons.org/licenses/by/3.0">CC BY 3.0</a> &mdash; Map data &copy; <a href="http://openstreetmap.org">OpenStreetMap</a> contributors, <a href="http://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>',
                    subdomains: "abcd",
                    maxZoom: 20,
                    minZoom: 0,
                    label: "Toner"
                }),
                L.tileLayer("http://{s}.tile.stamen.com/watercolor/{z}/{x}/{y}.jpg", {
                    attribution:
                        'Map tiles by <a href="http://stamen.com">Stamen Design</a>, <a href="http://creativecommons.org/licenses/by/3.0">CC BY 3.0</a> &mdash; Map data &copy; <a href="http://openstreetmap.org">OpenStreetMap</a> contributors, <a href="http://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>',
                    subdomains: "abcd",
                    maxZoom: 16,
                    minZoom: 1,
                    label: "Watercolor"
                })
            ];

            map.addControl(
                L.control.basemaps({
                    basemaps: basemaps,
                    tileX: 0,
                    tileY: 0,
                    tileZ: 1
                })
            );

        function pop_PAV2023_5(feature, layer) {
            var popupContent = '<table>\
                    <tr>\
                        <th scope="row">Code Postal</th>\
                        <td>' + (feature.properties['Code Postal'] !== null ? autolinker.link(feature.properties['Code Postal'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Commune</th>\
                        <td>' + (feature.properties['Commune'] !== null ? autolinker.link(feature.properties['Commune'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Container</th>\
                        <td>' + (feature.properties['Container'] !== null ? autolinker.link(feature.properties['Container'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">Adresse</th>\
                        <td>' + (feature.properties['Adresse'] !== null ? autolinker.link(feature.properties['Adresse'].toLocaleString()) : '') + '</td>\
                    </tr>\
                    <tr>\
                        <th scope="row">              </th>\
                        <td>' + (feature.properties['Commentaire POP-UP'] !== null ? autolinker.link(feature.properties['Commentaire POP-UP'].toLocaleString()) : '') + '</td>\
                    </tr>\
                </table>';
            layer.bindPopup(popupContent, {maxHeight: 400});
        }
 
     
        function style_PAV2023_5_0(feature) {
            switch(String(feature.properties['Container id'])) {
                case '1':
                    return {
                pane: 'pane_PAV2023_5',
                radius: 4.0,
                opacity: 1,
                color: 'rgba(35,35,35,1.0)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1,
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(51,160,44,1.0)',
                interactive: true,
            }
                    break;
                case '2':
                    return {
                pane: 'pane_PAV2023_5',
                radius: 4.0,
                opacity: 1,
                color: 'rgba(35,35,35,1.0)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1,
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(31,120,180,1.0)',
                interactive: true,
            }
                    break;
                case '3':
                    return {
                pane: 'pane_PAV2023_5',
                radius: 4.0,
                opacity: 1,
                color: 'rgba(35,35,35,1.0)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1,
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(255,127,0,1.0)',
                interactive: true,
            }
                    break;
                default:
                    return {
                pane: 'pane_PAV2023_5',
                radius: 4.0,
                opacity: 1,
                color: 'rgba(35,35,35,1.0)',
                dashArray: '',
                lineCap: 'butt',
                lineJoin: 'miter',
                weight: 1,
                fill: true,
                fillOpacity: 1,
                fillColor: 'rgba(209,187,43,1.0)',
                interactive: true,
            }
                    break;
            }
        }
        
        map.createPane('pane_PAV2023_5');
        map.getPane('pane_PAV2023_5').style.zIndex = 404;
        map.getPane('pane_PAV2023_5').style['mix-blend-mode'] = 'normal';
        var layer_PAV2023_5 = new L.geoJson(json_PAV2023_5, {
            attribution: '',
            interactive: true,
            dataVar: 'json_PAV2023_5',
            layerName: 'layer_PAV2023_5',
            pane: 'pane_PAV2023_5',
            onEachFeature: pop_PAV2023_5,
            pointToLayer: function (feature, latlng) {
                var context = {
                    feature: feature,
                    variables: {}
                };
                return L.circleMarker(latlng, style_PAV2023_5_0(feature));
            },
        });
        var cluster_PAV2023_5 = new L.MarkerClusterGroup({showCoverageOnHover: false,
            spiderfyDistanceMultiplier: 2});
        cluster_PAV2023_5.addLayer(layer_PAV2023_5);

        
        function zoomToFeature(e) {
            map.fitBounds(e.target.getBounds());
        }

        function onEachFeature(feature, layer) {
            layer.on({
                click: zoomToFeature
            });
        }
		
        bounds_group.addLayer(layer_PAV2023_5);
        map.addLayer(layer_PAV2023_5);
        var osmGeocoder = new L.Control.Geocoder({
            collapsed: true,
            position: 'topleft',
            text: 'Search',
            title: 'Testing'
        }).addTo(map);
        document.getElementsByClassName('leaflet-control-geocoder-icon')[0]
        .className += ' fa fa-search';
        document.getElementsByClassName('leaflet-control-geocoder-icon')[0]
        .title += 'Search for a place';
        setBounds();
        map.addControl(new L.Control.Search({
            layer: layer_PAV2023_5,
            initial: false,
            hideMarkerOnCollapse: true,
            propertyName: 'Commune'}));
        document.getElementsByClassName('search-button')[0].className +=
         ' fa fa-binoculars';
		 
		
		 var cluster_PAV2023_5 = new L.MarkerClusterGroup({showCoverageOnHover: false,
            spiderfyDistanceMultiplier: 2});
        cluster_PAV2023_5.addLayer(layer_PAV2023_5);

        bounds_group.addLayer(layer_PAV2023_5);
        cluster_PAV2023_5.addTo(map);
        setBounds();
		
		
         function getColor1(d) {
			return 	d > 3 ? '#FF7F00' :
					d > 2 ? '#1F78B4' :
                    d > 1 ? '#000000' :
                      
                                    '#000000';
            }
		
		
         var legend1 = L.control({position: 'bottomleft'});

         
            legend1.onAdd = function (map) {

                var div = L.DomUtil.create('div', 'info legend'),
                    grades = [1, 2, 3],
					
                    labels = ["<strong> Conteneurs: <br></strong>"],	
                    from, to;

                for (var i = 0; i < grades.length; i++) {
                    from = grades[i];
                    to = grades[i+1]; 
					

                    labels.push(
                        '<i style="background:' + getColor1(from) +'"></i> ' +
						from );
                }

                div.innerHTML = labels.join('<br/><br/>');
                return div;
            };
				
		 // Ajout de la légende à la carte 
         legend1.addTo(map);
        </script>
    </body>
</html>
