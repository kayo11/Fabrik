Frontend Design
=======
### Architecture
(All javascript files described here can be found in the /ide/static/js/ directory of Fabrik)

### ```app.js```
```app.js``` hosts the main app, but contains nothing else than a call for ```content.js```.
***
### ```content.js```
```content.js``` is the most important file, as it loads the main app. Here are some important functions:
(The top most bullet contains the name of the method and the inner bullets are params.)
* ```openModal``` - opens modals such as "Help" or "About Us"
* ```closeModal``` - closes previously opened modals
* ```infoModal``` - sets info about infoModal state, and then opens that modal
* ```faqModal``` - sets info about faqModal state, and then opens that modal
* ```zooModal``` - imports and opens zooModal
* ```addNewLayer``` - invoked by ```handleClick``` and passed in a JS object with layer information - adds a layer
  * layer
  * prevLayerId
* ```changeSelectedLayer``` - changes which layer has the selected class on it, which outlines layer to emphasize a "selection"
  * layerId
* ```changeHoveredLayer``` - changes which layer has the hover class on it, which outlines layer to emphasize a "hover"
  * layerId
* ```addHighlightOnLayer``` - highlights layer by ```layerId```
    * net
    * layerId
    * nextLayerId
* ```modifyLayer``` - modifies layer, passed in layer is the new layer, and layer id is the id it needs to be replaced at.
  * layer
  * layerId
* ```deleteLayer``` - deletes layers and removes inputs and outputs for it.
  * layerId
* ```getLayerParameters``` - sums total amount of parameters and updates layer's parameters
  * layer
  * net
* ```calculateParameters``` - loops through net and invokes ```getLayerParameters```
  * net
* ```adjustParameters``` - used to adjust layer parameters based on layer, change is passed in.
  * layer
  * para
  * value
* ```modifyLayerParams``` - modifies layer params based on layer and layerId, invoked by SetParams
  * layer
  * layerId
* ```loadLayerShapes``` - AJAXs to backend to model parameters
* ```exportNet``` - AJAXs to backend and then passes back error/success
    * framework
* ```exportPrep``` - invoked by ```exportNet```; prepares for model export
  * callback
* ```importNet``` - AJAXs to backend and then passes back error/success
  * framework
  * id
* ```initialiseImportedNet``` - starts prepping layer to be displayed by Fabrik, positions layers
  * net
  * net_name
* ```changeNetName``` - invoked on the event of net name changed; changes the name of the net
  * event
* ```changeNetStatus``` - changes boolean for rebuilding.
  * bool
* ```changeNetPhase``` - changes the phase of the net
  * phase
* ```dismissError``` - dissmisses the error by ```errorIndex```
  * errorIndex
* ```addError``` - adds error using ```errorText```
  * errorText
* ```dismissAllErrors``` - dismisses all errors
* ```copyTrain``` - copies the nets train option for the test option
* ```trainOnly```
* ```saveDb``` - creates RTC hyperlink
* ```loadDb``` - loads model for RTC by ```id```
  * id
* ```performSharedUpdate``` - updates RTC shared model using sockets
    * layerId
    * param
    * value
    * isProp
* ```performSharedAdd``` - adds shared layer in RTC
    * layer
    * prevLayerId
    * nextLayerId
    * layerId
* ```performSharedDelete``` - deletes shared layer in RTC
    * net
    * layerId
    * nextLayerId
* ```addSharedComment``` - adds comment shared in RTC
    * layerId
    * comment
* ```toggleSidebar``` - toggles the visibilty of the sidebar
* ```handleClick``` - handles a click based on an event: handles connections and adding layers.
  * event
* ```render``` - renders webpage using previous functions
***
### ```canvas.js```
```content.js``` invokes an instance of ```canvas.js``` that contains the following methods and params:
* ```allowDrop```
  * event
* ```clickLayerEvent``` - invoked on a click of a layer, invokes ```setParams.js```
  * event
  * layerId
* ```hoverLayerEvent``` - invoked on a hover
  * event
  * layerId
* ```scrollCanvas```
* ```clickCanvas```
  * event
* ```updateLayerPosition``` - changes layers positon based on event
  * event
* ```connectionEvent``` - invoked on connection, checks for cycles and modifies layers.
  * connInfo
  * originalEvent
* ```detachConnectionEvent``` - modifies layers
  * connInfo
  * originalEvent
* ```drop```
  * event
```canvas.js``` also contains the code that decides whether a node's line needs to be rerouted if it is cutting through another node.

### ```canvas.js```'s placement algorithm
The method it uses is the following:
```checkIfCuttingLine``` is passed in a positional block that includes x and y coordinates (it assumes a px is at the end of each x and y) for each endpoint of the line it is checking.
Specifically, it is checking if the line formed with the coordinates in the positional block will cut into any other nodes between them.

```checkIfCuttingLine``` creates then creates an equation from the x and y points by calculating the slope and using point slope form.

After this, it calls ```getBetween``` to get the nodes between the x and y coordinates of the created line.

The ```getBetween``` also serves the purpose of returning which direction in which the majority of the blocks between are. This is purely for performance, otherwise it would be seperated into a seperate function.

After ```getBetween``` returns with the id's of the nodes are between the x and y coordinate pair, check if cutting line loops through them to check whether or not the resultant line will cut through the in between node.

If it does, it will return the direction the line needs to be shifted to the parent function, ```checkIfCuttingNet```, to iterate once again 80 pixels to either the left or right (depending on the return.)

*This algorithmic design creates the possibility of an infinite loop if the canvas has been completely occupied  and there is no more space remaining.*
***
### ```topbar.js```
```topbar.js``` has no methods. It is invoked by ```content.js``` to show the top section of the sidebar.
***
### ```panZoom.js```
```panZoom.js``` includes the functions that zoom the canvas in and out, based on various invocations.  
***
### ```pane.js```
```pane.js``` contains one method:
* toggleClass
```pane.js``` invokes ```paneElement.js``` to render out each element.
```toggleClass``` toggles classes for the dropdown on the sidebar for layer selection.
***
### ```paneElement.js```
```paneElement.js``` renders out each element of the pane, it is invoked by ```pane.js```
```pane.js``` renders out all of the layers for selection by the user.
***
### ```tabs.js```
```tabs.js``` contains no methods and is used to switch between Train and Test layers.
***
### ```layer.js```
```layer.js``` contains no methods. It is invoked by the ```canvas.js``` and it displays the actual layering on the jsplumb container. The position of each layer is set in the state of the layer.
***
### ```jsplumb.js```
The ```jsplumb.js``` file contains code that handles the arrangement and the dragging/connecting of the layers. A new custom connector is created. There is an if function to check whether the node it is connecting is needing to be routed through an extension. A global variable stores this information, and the actual calculation is handled in ```checkIfCuttingNet``` and ```checkIfCuttingLine```. The global variable contains the amount of pixels it needs to move over, and it will contain direction it needs to go in (based on whether it is positive or
negative.)

Please refer to the jsplumb documentation here to learn more about this API set. https://jsplumbtoolkit.com/docs.html
***
### ```data.js```
```data.js``` contains various different variables filled with data used throughout the application.
***
### ```error.js```
```error.js``` contains Error React Component with one method:
* dismissError
The error is passed in through props and then displayed to the user.
***
### ```field.js```
```field.js``` contains the various different fields used by the layer editor.
* ```change```

The method change is used to change the state of checkboxes, and it is passed in event e.
***
### ```modelElement.js```
```modelElement.js``` contains the component that renders out each model in the model zoo. It includes logic that onClick will trigger an importNet, as defined in ```content.js```.
***
### ```modelZoo.js```
```modelZoo.js``` contains the rendering of the modelZoo, it invokes modelElement to render the actual listing of the model.
***
### ```netLayout_vertical.js``` and ```netLayout.js```
Both of these files contain code that determines positioning and layout of net. It is invoked by ```content.js```
***
### ```setParams.js```
Contains the following methods:
* ```changeProps```
    * prop
    * value
* ```changeParams```
    * para
    * value
* ```close```
* ```trainOnly```

```setParams.js``` allows users to change layer parameters and reads paramters through ```data.js```

***
### ```tooltip.js``` and ```tooltipData.js```
Both of these files contains code for tooltips and the ```tooltipData.js``` contains the actual tooltips that are rendered.

note: tooltips are "hover" messages, they tell more information about something when a user hovers over an object.
