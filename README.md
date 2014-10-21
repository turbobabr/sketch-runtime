sketch-runtime
==============

Sketch Runtime is a CocoaScript framework that allows remotely manage `Scriptable Actions` processed by [Sketch DevTools Assistant](https://github.com/turbobabr/sketch-devtools-assistant) application and preload/cache heavy CocoaScript modules used by plugins to boost their preformance.

> This framework is in a beta stage.

### Usage

```JavaScript
#import './cocoascript_modules/sketch-runtime/sketch-runtime.js'
// SketchRuntime object is available now.
```

### Managing Scriptable Actions

Registering actions:

Unregistering actions

### Caching modules

> TODO: Contente goes here.

*Playground.sketchplugin* source code:
```JavaScript
#import './sketch-runtime.js'

var rootPath = sketch.scriptPath.stringByDeletingLastPathComponent();
var initScriptPath=rootPath+"/init.js";
var mainScriptPath=rootPath+"/main.js";

SketchRuntime.run("com.turbobabr.playground",initScriptPath,mainScriptPath);
```

*init.js* source code:
```JavaScript
#import 'libs/underscore.js'
#import 'libs/path-builder.js'
#import 'libs/layer.js'
```

*main.js* source code:
```JavaScript
(function(){
    _.each(selection,function(layer){
        print(layer);
    });
})();

```
