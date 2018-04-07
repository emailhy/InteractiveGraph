# InteractiveGraph
InteractiveGraph provides a web-based interactive operating framwork for large graph data.

InteractiveGraph also provides three applications built on the framework: `GraphNavigator`, `GraphExplorer` and `RelFinder`.

__GraphNavigator__: online demo https://bluejoe2008.github.io/igraph/example1.html
<img src="docs/navi.png" width="700">

__GraphExplorer__: online demo 
https://bluejoe2008.github.io/igraph/example2.html
<img src="docs/exp.png" width="700">

__RelFinder__: online demo https://bluejoe2008.github.io/igraph/example3.html
<img src="docs/rel.png" width="700">

# Quick start
__Step 1.__ download `examples.zip` from `dist` directory: https://github.com/bluejoe2008/InteractiveGraph/blob/master/dist/examples.zip?raw=true

__Step 2.__ unzip and deploy `examples.zip` as a webapp in a Web server (Apache, Tomcat, etc). A Web server is required, otherwise the graph data loading via AJAX will get an error.

__Step 3.__ visit the webapp in Web browser, url may looks like:
https://localhost:8080/examples/example1.html

change `example1.html` to `examples2.html` or `examples3.html`, etc.

# How to use
__Step 1.__ download `interactive-graph-<VERSION>.zip`(`interactive-graph-0.1.0.zip`, for example) from `dist` directory: https://github.com/bluejoe2008/InteractiveGraph/blob/master/dist/interactive-graph-0.1.0.zip?raw=true

__Step 2.__ unzip `interactive-graph-<VERSION>.zip`, two files will be got: `interactive-graph.min.js` and `interactive-graph.min.css`.

__Step 3.__ import the `.js` and `.css` files in HTML page, like that:
```javascript
<script type="text/javascript" src="./lib/interactive-graph-0.1.0/interactive-graph.min.js"></script>
<link type="text/css" rel="stylesheet" href="./lib/interactive-graph-0.1.0/interactive-graph.min.css">
```

__Step 4.__ use functions and classes defined in `igraph` namespace:
```javascript
<script type="text/javascript">
    igraph.i18n.setLanguage("chs");
    var app = new igraph.GraphNavigator(document.getElementById('graphArea'));
    app.loadGson("honglou.json");
</script>
```
As shown above, a `GraphNavigator` application object is created and used to load graph data from `honglou.json`.

For more details, see https://github.com/bluejoe2008/InteractiveGraph/blob/master/dist/examples/example1.html.

To develop custom applications, it is a good idea to write new application classes derived on `GraphNavigator` and other application classes. Furthermore, users can create new application classes via using a `MainFrame` class directly.

# dependencies
this project depends on some open sourced components including `visjs`, `npm`, `gulp`, `jQuery`, `jQueryUI`, `Font Awesome` and so on.

<img src="docs/visjs.png" height="50"> <img src="docs/npm.png" height="50"> <img src="docs/gulp.png" height="50"> <img src="docs/jquery.png" height="50"> <img src="docs/jqueryui.png" height="50"> <img src="docs/fa.png" height="50">

More dependencies, see https://github.com/bluejoe2008/InteractiveGraph/blob/master/package.json.

# MainFrame, applications

InteractiveGraph provides a MainFrame, which actually is a facade of Web UI, event handling and graph data connection.

## controls

<img src="docs/frame.png" width="700">

As shown above, MainFrame consists of a main graph renderimng canvas,a set of navigation buttons, and serveral controls: search bar, tool bar, info box, highlight control and so on.

Some controls have Web UI, such as InfoBoxCtrl, MessageBoxCtrl, etc. While others work with no UI, such as HighlightNodeCtrl, ExpansionCtrl, etc. A `Control` class and two derived classes `UIControl`(controls with UI) and `WorkerControl`(worker control without UI) are defined in InteractiveGraph.

All controls are listed in the table below.

| control | function | type |
|-|-|-|
|ConnectCtrl|show a load data dialog|UIControl|
|ExpansionCtrl|expands a graph node on double click|WorkerControl|
|HighlightNodeCtrl|highlight selected graph nodes|WorkerControl|
|InfoBoxCtrl|show a infomation box on click on a graph node|UIControl|
|MessageBoxCtrl|show a message box on demand|UIControl|
|RelFinderCtrl|finding relations between graph nodes|WorkerControl|
|RelFinderDialogCtrl|show a dialog for user to launch a finding task|UIControl|
|SearchBarCtrl|show a search bar for keyword input|UIControl|
|ToolbarCtrl|show a tool bar|UIControl|

## theme

## event handling

MainFrame and controls communicate with applications through events. For example, when the window is resizing, MainFrame and all controls will receive FRAME_RESIZE events.

To subscribe a event, call a `on(event, handler)` method on MainFrame or a Control. To cancel the subscription, use `off(event, handler)` instead.

To fire an event to MainFrame, use `MainFrame.emit(event, args)` or `MainFrame.fire(event, args)` method. Unlike the `emit()` method, `fire()` put default context objects including `mainFrame`, `htmlMainFrame`, `theme` into `args` before emition. To fire an event to a `Control`, use `emit(event, args)` method.

## data connector

MainFrame loads data via a `Connector` which may connect to a `LocalGraph` or a `RemoteGraph`. A `LocalGraph` loads all data once from a GSON object or file, while a `RemoteGraph` interacts with a remote graph server each time if no cache data is available.

# <a name="GSON">GSON</a>

MainFrame loads data from a `LocalGraph` in `GSON` format. `GSON` is actually an enhanced `JSON` format of graph data. The enhancement is `GSON` recoginzes `function`s, which is not valid in JSON.

`GSON` consists of some data items, it is defined as follow:

```typescript
export interface GSON {
    "data": {
        nodes: object[];
        edges?: object[];
    }
    "dbinfo"?: object;
    "categories"?: object;
    "translator"?: {
        "nodes"?: (node: object) => void;
        "edges"?: (node: object) => void;
    };
}
```

Here, `translator` defines transaltor functions for graph nodes and edges, which will be invoked on loading. An example is shown below, in which `description` of each node is evaluated on loading time.

```javascript
  "translator": {
    "nodes": function (node) {
      //set description
      if (node.description === undefined) {
        var description = "<p align=center>";
          description += "<img src='" + node.image + "' width=150/><br>";
        description += "<b>" + node.label + "</b>" + "[" + node.id + "]";
        description += "</p>";
        node.description = description;
      }
    },
  }
```

# Datasets
## honglou.json

The honglou dataset comes from the Chinese famous novel *Dream of the Red Chamber*(also called *The Story of the Stone*, https://en.wikipedia.org/wiki/Dream_of_the_Red_Chamber), in which *Jia Baoyu*, *Lin daiyu*, and *Xue baochai* are famous characters. The honglou dataset defines 300+ entities represent persons, locations, and events in the novel, and 500+ links between them.

@nickel_2008 provides this dataset.

## WorldCup2014.json

The world cup 2014 data set comes from http://visjs.org/examples/network/exampleApplications/worldCupPerformance.html. The edges in particular (~9200) are very computationally intensive to draw.

The next screenshot shows how WorldCup2014.json is rendered in `GraphNavigator` (empowered by visjs).

<img src="docs/worldcup2014.png" width="700">

# Applications

## GraphNavigator

## GraphExplorer

## RelFinder

<img src="docs/rel.gif" width="700">

# Contributing to InteractiveGraph

It would be highly appreciated if you commit any codes or documents to InteractiveGraph. If you have any good idea, please fork this project and create pull requests.

* To contribute new applications, you may define new application class derived from `BaseApp`.

* To contribute any controls, you may define new control class derived from `UIControl` and `WorkerControl`.

* To contribute new themes, refer to `themes.ts`.

* To contribute new tool button, you may define new ButtonInfos. Note that `Font Awesome` icons are available in button icon!

* To contribute new data sets or modification, you may submit data in GSON format!

# build & run

__Step 1.__ use `npm run build` or `gulp build` to build InteractiveGraphBrowser, which generates `interactive-graph.js` and `interactive-graph.css` in `build` dir.

__Step 2.__ open `src/test/webapp/example1.html` in Web browser (`Google Chrome`, for example).

*or*

__Step 1.__ use `gulp release` if you want to get distribution version, which will output `examples` and `interactive-graph-<VERSION>` in `dist` dir.

__Step 2.__ open `dist/examples/example1.html` in Web browser.

DO NOT attempt to use `gulp deploy` if you are not bluejoe2008@github.