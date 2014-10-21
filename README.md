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

At this moment Sketch App doesn't allow plugins developers to pre-initialize their scripts and reuse them between launch sessions. 
Everytime when user runs a plugin - a new COScript session is created, all the imported modules are parsed, compiled in a single string and executed. Such behaviour dramatically slows down the performance of a plugin and it becomes noticeable for plugins that contain a lot of code or use heavy open source frameworks like underscore.js, lodash.js or d3js.

Good news everyone! There is a pretty "legal" solution for this issue. We can pre-initialize all the heavy imports or any code on first plugin launch, cache the initialized session and reuse it with the next launch.

SketchRuntime object has a handy method `run` that make all the dirty job for you:
```JavaScript
SketchRuntime.run(id, initScriptPath, mainScriptPath)
```
Where:
- `id` - ID of a module to be cached (use reverse DNS notation here).
- `initScriptPath` - file path to the script to be cached.
- `mainScriptPath` - file path to the script that contains some business logic and uses code initialized in cached module.

**Example**
 
Plugin folder structure:
```
- RootFolder
    - Playground.sketchplugin // A target plugin that is run by a user.
    - init.js // Does all the imports and provides API
    - main.js // Contains a business logic that is build on top of API provided by init.js
```

**Playground.sketchplugin** source code:
```JavaScript
#import './sketch-runtime.js'

var rootPath = sketch.scriptPath.stringByDeletingLastPathComponent();
var initScriptPath=rootPath+"/init.js";
var mainScriptPath=rootPath+"/main.js";

SketchRuntime.run("com.turbobabr.playground",initScriptPath,mainScriptPath);
```

**init.js** source code:
```JavaScript
#import 'libs/underscore.js'
#import 'libs/path-builder.js'
#import 'libs/layer.js'
```

**main.js** source code:
```JavaScript
(function(){
    _.each(selection,function(layer){
        print(layer);
    });
})();
```
What happens here is that the plugin itself becomes a launcher. On first plugin launch it checks if the `init.js` module isn't cached yet and stores it in a thread dictionary and only then executes the business logic provided by `main.js` module.

Such technique allows to develop very complex and heavy plugins and make them really fast! :)

> WARNING: Be very careful with this approach since it may lead to memory leaks and whole Sketch process performance degradation. Use it only for plugins that carry a huge pile of code and try to avoid it for lightweight plugins.

