# Plugin-a-Development

# Plugin a Development     -Abdulsamet Toptaş (21905024)

In order to develop Plugins in QGIS, Plugin Builder and Plugin Reloader plugins must be downloaded. Plugin Builder creates files for the plugin and Plugin Reloader saves the changes made.

First of all, plugin files were created with the help of Plugin Builder. Then, add

" 

         @echo off to a text document
         call "C:\OSGeo4W64\bin\o4w_env.bat"
         call "C:\OSGeo4W64\bin\qt5_env.bat"
         call "C:\OSGeo4W64\bin\py3_env.bat"
         
         @echo on
         pyrcc5 -o resources.py resources.qrc 

" 

codes were added to create a batch file with .bat extension. This is a step to convert the libraries of the necessary Python codes and the resources file created for the plugin to .py.

Then, this created batch file is run within the plugin file and the next step is taken. The next step is to open the file named dialog_base.ui in the file created for the plugin in the "QTDesigner" environment and edit the plugin interface as desired. The important thing here is to note the object name in the upper right section of the button that will direct you to the map, because the Python codes required for the button to work must be associated with this button.

This created plugin file is then added to the python --> plugin tab in QGIS Settings --> User Profiles --> Open Active Profile File. Then, the plugin created in the Plugins --> Install and Manage Plugins --> Installed section is activated.

Then, go to the developed file directory in QGIS - python - plugin and open the **plugin_name.py** file in the python environment. **"from qgis.core import QgsProject"** is added. QgsProject is added to manage map layers, settings, and other project-specific information in a QGIS project.

In the same file directory, the **plugin_name_dialog.py** file is opened in the python environment and the **"self.pushButton.clicked.connect(self.on_pushButton_clicked)"** command is added under def __init__. This is because it binds the button created in QtDesigner with the clicked action. The button name (pushButton) created in QtDesigner was noted down and now this name is associated with this button.

Next, the command **"self.layers_added = False"** is added under def __init__. This command keeps track of whether there are layers added and makes them updateable with the False command.

In the last part, a new def is opened and added to the python code.

" 

         def on_pushButton_clicked(self):
                  
                  if not self.layers_added:
                      # Create OpenStreetMap layer
                      osm_layer = QgsVectorLayer('Point?crs=EPSG:4326', 'Hacettepe Geomatics Engineering', 'memory')
                     
                      # Set up the provider
                      provider = osm_layer.dataProvider()
                     
                      # Add a point
                      feature = QgsFeature()
                      feature.setGeometry(QgsGeometry.fromPointXY(QgsPointXY(32.7339, 39.8656)))
                      provider.addFeatures([feature])
                     
                      # Update extent
                      osm_layer.updateExtents()
                     
                      # Add OpenStreetMap layer to the map canvas
                      QgsProject.instance().addMapLayer(osm_layer)
                     
                      # Add background Google Satellite layer
                      openstreetmap_layer = QgsRasterLayer(
                          'type=xyz&url=https://tile.openstreetmap.org/{z}/{x}/{y}.png',
                          'OpenStreetMap', 'wms'
                      )
                      QgsProject.instance().addMapLayer(openstreetmap_layer)
                     
                      # Create a map canvas
                      canvas = QgsMapCanvas()
                     
                      # Set the map canvas extent
                      canvas.setExtent(osm_layer.extent())
                     
                      # Set the map canvas background color
                      canvas.setCanvasColor(Qt.white)
                     
                      # Add layers to the map canvas
                      canvas.setLayers([osm_layer, openstreetmap_layer])
                     
                      # Refresh the map canvas
                      canvas.refresh()
                     
                      # Show the map canvas
                      canvas.show()
                     
                      print('Hacettepe Geomatics Engineering layer and OpenStreetMap layer added successfully.')
                     
                      # Set the flag to True to indicate layers are added
                      self.layers_added = True
                  else:
                      print('Layers are already added. No further action is taken.') 
"

To briefly explain the variables in this added code,


**osm_layer:** A vector layer representing OpenStreetMap data is created.

**provider:** The provider of the created vector layer is determined.

**feature:** A feature containing a point geometry with Hacettepe Geomatics Engineering coordinates is created.

Feature is added to osm_layer and the borders of the layer are updated.

The created OpenStreetMap layer is added to the QGIS project via QgsProject.

**openstreetmap_layer:** A raster layer is created that represents the OpenStreetMap map to be used as the background.

**openstreetmap_layer** is added to the QGIS project.

**canvas:** A map canvas is created to be used within the QGIS map window.

**canvas.setExtent(osm_layer.extent()):** The width of the map canvas is set to match the boundaries of the OpenStreetMap layer.

**canvas.setCanvasColor(Qt.white):** The background color of the map canvas is set to white.

**canvas.setLayers([osm_layer, openstreetmap_layer]):** Point and background map are added to the map canvas.

**canvas.refresh():** The map canvas is redrawn.

**canvas.show():** The map canvas is displayed.

**self.layers_added = True:** The layers_added flag is set to True to determine whether layers have been added successfully.

**else:** If layers have already been added, a message is printed and no further action is taken.

Finally, the python file is saved and the plugin is updated and ready for use in the QGIS environment with the help of Plugin Reloader.

## Reference

https://www.qgistutorials.com/en/docs/3/building_a_python_plugin.html
