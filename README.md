# Ionic3-Geolocation-Geofence-Leaflet
A web &amp; mobile demo using Ionic 3 for displaying current location and Geofence on Leaflet Map.

This example was tested on the browser and on an android emulator.

![The Starting Screen](https://github.com/KawtharE/Ionic3GeolocationGeofenceLeaflet/blob/master/assets/mobile_version.png)


![The Starting Screen](https://github.com/KawtharE/Ionic3GeolocationGeofenceLeaflet/blob/master/assets/web_version.png)


## 1- Create a new Ionic project
$ ionic start ProjectName

$ cd ProjectName

## 2- Install Leaflet & add leaflet.css and leaflet.js references to index.html
$ npm install leaflet --save

$ npm install @types/leaflet --save

## 3- Add Leaflet Map to the Home page
    this.map = leaflet.map("map").fitWorld();
	  leaflet.tileLayer('http://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
	    attributions: 'www.tphangout.com',
	    maxZoom: 18
	}).addTo(this.map);
  
## 4- Install Geolocation Plugins
$ npm install @ionic-native/core --save

$ npm install @ionic-native/geolocation --save

$ ionic cordova plugin add cordova-plugin-geolocation

$ ionic cordova plugin add cordova-plugin-advanced-geolocation

PS: we have used cordova-plugin-advanced-geolocation for the Android environment so we can test the example on the emulator because we can no longer use Geolocation on unsecured origin.

## 5- Add Platforms (Android)
$ cordova platform add android

## 6- Add permissions to AndroidManifest.xml

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    
 ## 7- Location detection  & fenc drawing
    ### 7-1- For the Android environment
    
            AdvancedGeolocation.start((position) => {
              try {
                var jsonObject = JSON.parse(position);
                switch (jsonObject.provider) {
                  case "gps":
                    this.currentLat = jsonObject.latitude;
                    this.currentLng = jsonObject.longitude;
                    break;

                  case "network":
                    this.currentLat = jsonObject.latitude;
                    this.currentLng = jsonObject.longitude;
                    break;

                  case "satellite":
                    //TODO
                    break;

                  case "cell_info":
                    //TODO
                    break;

                  case "cell_location":
                    //TODO
                    break;

                  case "signal_strength":
                    //TODO
                    break;
                }
                this.map.setView([this.currentLat, this.currentLng], 16);
		    	let markerGroup = leaflet.featureGroup();
		    	let marker: any = leaflet.marker([this.currentLat, this.currentLng]);
		    	marker.bindPopup("<b>I'm here!</b><br>").openPopup();
		    	markerGroup.addLayer(marker);
		    	this.map.addLayer(markerGroup);
		    	var circle = leaflet.circle([this.currentLat, this.currentLng], {
		    	    color: 'Green',
				    fillColor: '#81C784',
				    fillOpacity: 0.5,
				    radius: 200
		    	}).addTo(this.map);
		    	circle.bindPopup("My area.");

              } catch (exc) {
                console.log("Error: " + exc);
                alert('Sorry looks like there is an error and cannot detect your current location on your Android device!');
              }
				    
			   
            },
            function (error) {
                console.log("ERROR! " + JSON.stringify(error));
            },
            {
                "minTime": 500,         // Min time interval between updates (ms)
                "minDistance": 100,       // Min distance between updates (meters)
                "noWarn": true,         // Native location provider warnings
                "providers": "all",     // Return GPS, NETWORK and CELL locations
                "useCache": true,       // Return GPS and NETWORK cached locations
                "satelliteData": false, // Return of GPS satellite info
                "buffer": false,        // Buffer location data
                "bufferSize": 0,         // Max elements in buffer
                "signalStrength": false // Return cell signal strength data
            });
           
    ### 7-2- For the Web environment  
  
  			 this.map.locate({
			   setView: true,
			   maxZoom: 10
			 }).on('locationfound', (e) => {
			   this.map.setView([e.latitude, e.longitude], 16);
			   let markerGroup = leaflet.featureGroup();
			   let marker: any = leaflet.marker([e.latitude, e.longitude]);
			   marker.bindPopup("<b>I'm here!</b><br>").openPopup();
			   markerGroup.addLayer(marker);
			   this.map.addLayer(markerGroup);
			   var circle = leaflet.circle([e.latitude, e.longitude], {
			     color: 'Green',
	             fillColor: '#81C784',
		         fillOpacity: 0.5,
		         radius: 200
			    }).addTo(this.map);
			    circle.bindPopup("My area.");
			  }).on('locationerror', (err) => {
			    alert(err.message);
			  });
 
