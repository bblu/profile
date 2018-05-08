---
title: Mapbox stops样式调用解析和图层合并
date: 2018-05-08 23:00
categories: blog
tags: [gis, mapbox]
---

> 节前为了解决图标放大发虚的问题一直在测试SDF方式渲染图标技术上已经搞定了，就等美工把svg格式符号库给整出来了。目前业务系统样式比较复杂一类设备往往配几十种样式渲染的图层比较多，为了提高效率寻求了各种方法，降低图层数量无疑是比较有效的。目前修改为stops支持多属性,没有限制的字段可以为空（undefined）或者为“any”也可以，最后将700多的样式图层合并到300个以下。

### 先看style中的stops定义的样式
```json
"paints":[
    "type":"symbol-circle",
    "icon-image":{
        "stops":[
            [[18, 2516, 16011],"icon_16011"],
            [[18, 2516, 16012],"icon_16012"],
            [[18, 2520, "any"],"icon_16020"],
            [[18, 2530,      ],"icon_16030"]
        ]
        "property":["zoom","type","subtype"]
    }
]
```
- style_layer.js getLayoutValue 存在 featureProperties=undefined的情况。
根据运行输出判断应该是在样式区间内只在调变的图层执行样式绑定和刷新，
但是现在单击地图和推到最底层后滚轮都会触发样式刷新的调用以后还有优化的空间。

### 调用堆栈
```js
map.render->
    style.recalculate->
        style_layer.recalculate->
        symbol_style_layer.getLayoutValue->
        style_layer.getLayoutValue->
            style_function.outer->
                mapbox-gl-function.fun->
                mapbox-gl-function.evaluateCategoricalFunction(){
                    
                }
```

### 具体代码执行

```js
//ui/map.js
_render: function(){
    if(this.style && this._styleDirty){
        this._styleDirty = false;
        
        this.style._recalculate(this.transform.zoom);
    }
}

//style/style.js
_recalculate: function(z){
    //...
    for(var layerId in this._layers){
        this._layers[layerId].recalculate(z, this.zoomHistory)
    }
    this.z = z
    this.fire('zoom')
}
//style/style_layer.js
recalculate: function(zoom, zoomHistory){
    for(var paintName in this._paintTransitions){
        this.paint[paintName] = this.getPaintValue(paintName,{zoom,zoomHistory});
    }
    for(var layoutName in this._layoutFunctions){
        this.layout[layoutName] = this.getLayoutValue(layoutName, {zoom,zoomHistory})
    }
}
//style/style_layer/symbol_style_layer.js
getLayoutValue: function(name, globalProperties, featureProperties){
    if(name === 'text-rotation-alignment' && ...){      return 'map'
    }else if(name === 'icon-rotation-alignment' && ...){return 'map'
    }else{
        //arguments={name:'icon-image', {zoom,zoomHistory}}
        return StyleLayer.prototype.getLayoutValue.apply(this, arguments)
    }
    
}
//style/style_layer.js
getLayoutValue:function(name, globalProperties, featureProperties){
    return StyleLayer.getlayoutValue(){
        //此处的featureProperties有未定义
        return declaration.calculate(globalProperties, featureProperties)
    }
}
//style/style_function.js
    var inner = MapboxGLFunction['piecewise-constant'](parameters);
    var outer = function(globalProperties, featureProperties){
        //空对象
        return inner(globalProperties&&globalProperties.zoom, featureProperties||{})
    }
```

### 解析stops

```js
//node_modules/mapbox-gl-function/index.js
createFunction(parameters, defaultType){
    //...
}
function evaluateCategoricalFunction(parameters, input){
    //support stops zoom value by bblu @ 2018-05-07
    //这里是具体解析stops的地方，可以根据自己的格式去扩展，就不展开了
    +...
}

```