ionic-angular-google-maps
=========================

Integration of the Ionic Framework and GoogleMap AngularJS Directive by AngularUI

I will be sharing how to integrate the GoogleMap AngularJS Directive by AngularJS UI with Ionic Framework.

Disclaimer: I am still playing around with Ionic Framework and AngularJS so please forgive me if there is any performance issue or bugs :D. Let me know if you can resolve any of those issues. Cheers!

Please visit https://angular-ui.github.io/angular-google-maps/ to get more information.

I prefer to use the sidemenu template so i would just use that, you may try the tabs as well.
```
ionic start <project name> sidemenu
```
Download jQuery, LoDash, Bluebird and of course the Google Map AngularJS Directive.

For me i put them in js/utils folder. Include the scripts into the index.html

Open up app.js and add 'uiGmapgoogle-maps' to the application module dependencies, it was confusing to use 'google-maps'.ns() and it equals to 'uiGmapgoogle-maps' so I might as well use that.
```
angular.module('starter', ['ionic', 'starter.controllers', 'uiGmapgoogle-maps')
```
Scroll down to .config and add the follow lines to setup the provider
```
.config(function($stateProvider, $urlRouterProvider, uiGmapGoogleMapApiProvider) {
	uiGmapGoogleMapApiProvider.configure({
		key: 'your api key',
		v: '3.17',
		libraries: '',
		language: 'en',
		sensor: 'false',
	})
	...
```
I'm using the Browse Controller so I'll just add a 'BrowseCtrl' to the Browse state and in the controllers.js I will add BrowseCtrl.

Add the following lines to grab the map object and initialize it into the container
```
.controller('BrowseCtrl', function($scope){
	//map variable containing the map details, will be referenced from the html
	$scope.map = {center: {latitude: 51.219053, longitude: 4.404418 }, zoom: 14 };
	//map options
 	$scope.options = {scrollwheel: false};
});
```
Edit browse.html to contain the map canvas container
```
<ion-view title="Browse">
	<ion-nav-buttons side="left">
	<button menu-toggle="left"class="button button-icon icon ion-navicon"></button>
	</ion-nav-buttons>

	<ion-content class="has-header">
	<div id="map_canvas">
	    <ui-gmap-google-map center="map.center" zoom="map.zoom" draggable="true" options="options"></ui-gmap-google-map>
	</div>
	</ion-content>
</ion-view>
```
Right now, you probably won't see any map yet because you have to specify the width and height of the container

Go to style.css to add the attributes
```
#map_canvas {
	width: 100%;
	height: 100%;
	margin: 0;
	position: relative;
}

.scroll {
	height: 100%;
}

.angular-google-map-container {
    position: absolute;
    top: 0;
    bottom: 0;
    right: 0;
    left: 0;
}
```
I don't know if there is a bug somewhere, you need to set 100% height for the .scroll class if not the map_canvas container will not be able to stretch to the full height

Now the map will display, you can see https://angular-ui.github.io/angular-google-maps/ if you want to add markers and stuff

If you want to use cordovaGeolocation plugin to detect the user's location, simply download the ngCordova distribution from http://ngcordova.com/, for me i use ng-cordova.min.js, the custom build seems to have some problems so i skipped that.

Put the distribution in the js folder with the rest and link it in index.html before cordova.js

index.html
```
<script src="js/utils/ng-cordova.min.js"></script>
<script src="cordova.js"></script>
```
And then include the module into the application module dependencies list
```
angular.module('starter', ['ionic', 'starter.controllers', 'uiGmapgoogle-maps', 'ngCordova'])
```
You can now call $cordovaGeolocation to detect the coords
```
.controller('BrowseCtrl', function($scope, $ionicSideMenuDelegate, $cordovaGeolocation){

  $ionicSideMenuDelegate.canDragContent(false)
  $scope.map = {center: {latitude: 40.1451, longitude: -99.6680 }, zoom: 8 };
  $scope.options = {scrollwheel: true};
  $scope.markers = []
  // get position of user and then set the center of the map to that position
  $cordovaGeolocation
    .getCurrentPosition()
    .then(function (position) {
      var lat  = position.coords.latitude
      var long = position.coords.longitude
      $scope.map = {center: {latitude: lat, longitude: long}, zoom: 16 };
      //just want to create this loop to make more markers
      for(var i=0; i<3; i++) {
        $scope.markers.push({
            id: $scope.markers.length,
            latitude: lat + (i * 0.002),
            longitude: long + (i * 0.002),
            title: 'm' + i
        })
      }
      
    }, function(err) {
      // error
    });
});
```



