<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta
      name="viewport"
      content="initial-scale=1,maximum-scale=1,user-scalable=no"
    />
    <title>Switch view from 2D to 3D - 4.12</title>

    <style>
      html,
      body,
      #viewDiv {
        padding: 0;
        margin: 0;
        height: 100%;
        width: 100%;
      }

      #infoDiv {
        position: absolute;
        top: 15px;
        left: 60px;
      }

      #infoDiv input {
        border: none;
        box-shadow: rgba(0, 0, 0, 0.3) 0px 1px 2px;
      }
    </style>

    <link
      rel="stylesheet"
      href="https://js.arcgis.com/4.12/esri/themes/light/main.css"
    />
    <script src="https://js.arcgis.com/4.12/"></script>

    <script>
      require([
        "esri/views/MapView",
        "esri/views/SceneView",
        "esri/WebMap",
        "esri/WebScene"
      ], function(MapView, SceneView, WebMap, WebScene) {
        var switchButton = document.getElementById("switch-btn");

        var appConfig = {
          mapView: null,
          sceneView: null,
          activeView: null,
          container: "viewDiv" // use same container for views
        };

        var initialViewParams = {
          zoom: 10,
          center: [-115.1614, 50.6152],
          container: appConfig.container
        };
        var webmap = new WebMap({
          portalItem: {
            // autocasts as new PortalItem()
            id: "d146cbe2d6f04df8b6d615c2f0e07082"
          }
        });
        var scene = new WebScene({
          portalItem: {
            // autocasts as new PortalItem()
            id: "e17d0408e8154678a4c3847ac0d7fc52"
          }
        });
        // create 2D view and and set active
        appConfig.mapView = createView(initialViewParams, "2d");
        appConfig.mapView.map = webmap;
        appConfig.activeView = appConfig.mapView;

        // create 3D view, won't initialize until container is set
        initialViewParams.container = null;
        initialViewParams.map = scene;
        appConfig.sceneView = createView(initialViewParams, "3d");

        // switch the view between 2D and 3D each time the button is clicked
        switchButton.addEventListener("click", function() {
          switchView();
        });

        // Switches the view from 2D to 3D and vice versa
        function switchView() {
          var is3D = appConfig.activeView.type === "3d";
          var activeViewpoint = appConfig.activeView.viewpoint.clone();

          // remove the reference to the container for the previous view
          appConfig.activeView.container = null;

          if (is3D) {
            // if the input view is a SceneView, set the viewpoint on the
            // mapView instance. Set the container on the mapView and flag
            // it as the active view
            appConfig.mapView.viewpoint = activeViewpoint;
            appConfig.mapView.container = appConfig.container;
            appConfig.activeView = appConfig.mapView;
            switchButton.value = "3D";
          } else {
            appConfig.sceneView.viewpoint = activeViewpoint;
            appConfig.sceneView.container = appConfig.container;
            appConfig.activeView = appConfig.sceneView;
            switchButton.value = "2D";
          }
        }

        // convenience function for creating a 2D or 3D view
        function createView(params, type) {
          var view;
          var is2D = type === "2d";
          if (is2D) {
            view = new MapView(params);
            return view;
          } else {
            view = new SceneView(params);
          }
          return view;
        }
      });
    </script>
  </head>

  <body>
    <div id="viewDiv"></div>
    <div id="infoDiv">
      <input
        class="esri-component esri-widget--button esri-widget esri-interactive"
        type="button"
        id="switch-btn"
        value="3D"
      />
    </div>
  </body>
</html>
