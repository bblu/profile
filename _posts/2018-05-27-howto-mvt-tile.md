---
title: 从零开始构建矢量块服务[译]
date: 2018-05-27 23:00
categories: blog
tags: [mapbox,pbf]
---


Mapbox Vector Tiles是一种现代化的存储和传输方式，你可以在shapefile，GeoJSON或TopoJSON文件中找到相同类型的feature数据，mvt使用了渲染友好的方式加以整理并且压缩成pfb的格式方便网络传输和解析。


### 1.准备测试数据

从【[opendatadownload](https://www.ordnancesurvey.co.uk/opendatadownload/products.html#OPMPLC)】下载免费数据。

- 在National Grid Reference标签右侧的框中勾选下载列，然后向下滚动以从网格方块图旁边的列表中选择TQ和TR。滚动到页面的最底部，点击继续。如果是自己的数据记得修改后面地图的中心点坐标。参考分块目录下的metadata.json
- 推到页面最底下点击【continue】
- 填写个人信息，邮箱填好因为下载地址是要通过邮箱发送的，页面左下角有个人机交互认证check一下，点击【continue】继续。
- 登陆填写的邮箱去下载选择的数据。
- 下载【[OSTN02 NTv2 data](https://www.ordnancesurvey.co.uk/docs/gps/ostn02-ntv2-data.zip)】里面的OSTN02_NTv2.gsb后面会用到。
- 把以上三个数据包放到一个目录下，比如src。

### 2.准备工具

    brew install gdal tippecanoe
    //如果已经安装不放心的话可以upgrade一下
    brew update
    brew upgrade gdal tippecanoe

    macbook:tegola bblu$ ogr2ogr --version
    GDAL 2.3.0, released 2018/05/04
    macbook:tegola bblu$ tippecanoe -v
    tippecanoe v1.29.0
    
> ⚠️ tippecanoe的版本不低于 1.18.1

### 3.数据处理

#### 3.1.把shp数据解压到build/shapefiles下

    find "src" | grep ".zip" | xargs -n1 unzip -d "build/shapefiles"

#### 3.2.提取Road图层

    export LAYER=Road
    find "build/shapefiles" | grep "_${LAYER}.shp" > "build/${LAYER}_shapefiles.txt"
    cat "build/${LAYER}_shapefiles.txt"
    
    build/shapefiles/OS OpenMap Local (ESRI Shape File) TQ/data/TQ_Road.shp
    build/shapefiles/OS OpenMap Local (ESRI Shape File) TR/data/TR_Road.shp
#### 3.3.合并Road图层
    //先把第一个Road文件转移到build目录
    ogr2ogr -f 'ESRI Shapefile' "build/$LAYER.shp" -nln "${LAYER}" "`cat "build/${LAYER}_shapefiles.txt" | head -n 1`"
    //然后把剩下的Road图层合并到第一个shp文件
    cat "build/${LAYER}_shapefiles.txt" | tail -n +2 | while read line; do
        ogr2ogr -f 'ESRI Shapefile' -update -append "build/$LAYER.shp" "$line" -nln "${LAYER}"
    done
#### 3.4.生成GeoJson

    mkdir build/geojson
    export LAYER=Road
    ogr2ogr -dim 2 -f 'GeoJSON' -s_srs "+proj=tmerc +lat_0=49 +lon_0=-2 +k=0.999601 +x_0=400000 +y_0=-100000 +ellps=airy +units=m +no_defs +nadgrids=./src/OSTN02_NTv2.gsb" -t_srs 'EPSG:4326' "build/geojson/$LAYER.json" "build/$LAYER.shp"

> 这一步时间比较长，用到之前下载的OSTN02_NTv2.gsb文件了，没找到的话解压放到对应目录里即可。

#### 3.5.终于要切片啦

    tippecanoe --no-feature-limit --no-tile-size-limit --exclude-all --minimum-zoom=5 --maximum-zoom=14 --output-to-directory "build/www/tiles" `find ./build/geojson -type f | grep .json`

- --minimum-zoom=5 起始切片图层，tippecanoe工具切片会压缩。
- --maximum-zoom=14 最大切片图层，如果是g表示tippecanoe自行判断合适的图层。
- --exclude-all 排除所有属性数据，切片最小因为后面要配样式不要使用。

#### 总结一下脚本
```sh
export LAYER=Road
mkdir -p build/www/tiles/
find "src" | grep ".zip" | xargs -n1 unzip -d "build/shapefiles"
find "build/shapefiles" | grep "_${LAYER}.shp" > "build/${LAYER}_shapefiles.txt"
ogr2ogr -f 'ESRI Shapefile' "build/$LAYER.shp" -nln "${LAYER}" "`cat "build/${LAYER}_shapefiles.txt" | head -n 1`"
cat "build/${LAYER}_shapefiles.txt" | tail -n +2 | while read line; do
    ogr2ogr -f 'ESRI Shapefile' -update -append "build/$LAYER.shp" "$line" -nln "${LAYER}"
done
mkdir build/geojson
ogr2ogr -dim 2 -f 'GeoJSON' -s_srs "+proj=tmerc +lat_0=49 +lon_0=-2 +k=0.999601 +x_0=400000 +y_0=-100000 +ellps=airy +units=m +no_defs +nadgrids=./src/OSTN02_NTv2.gsb" -t_srs 'EPSG:4326' "build/geojson/$LAYER.json" "build/$LAYER.shp"
tippecanoe --no-feature-limit --no-tile-size-limit --exclude-all --minimum-zoom=5 --maximum-zoom=g --output-to-directory "build/www/tiles" `find ./build/geojson -type f | grep .json`
```

### 4.发布切片

    npm install live-server
    export PATH=$PWD/node_modules/.bin:$PATH
    live-server --port=8000 --host=localhost build/www
    
```
#RequestHeaders
Request URL: http://localhost:8000/build/tiles/18/128/85.pbf
Request Method: GET
Status Code: 304 Not Modified
Remote Address: 127.0.0.1:8000
Referrer Policy: no-referrer-when-downgra
#Response Headers
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Connection: keep-alive
Date: Sun, 27 May 2018 02:25:37 GMT
ETag: W/"b96-163a478c906"
Last-Modified: Sun, 27 May 2018 01:59:29 GMT
```

本文参考[[Build Your Own Static Vector Tile Pipeline](https://geovation.github.io/build-your-own-static-vector-tile-pipeline)--by--Max Stephan]
