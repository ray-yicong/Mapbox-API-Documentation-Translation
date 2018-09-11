## 静态地图相关设置

Mapbox静态API从[Mapbox Style Specification](https://mapbox.com/mapbox-gl-style-spec)中返回至静态地图和栅格切片.

- **静态地图** 是不需要借助映射库或API就可以在网页和移动设备上显示的独立影像。他们就像是嵌入式地图，但不能交互和控制，反馈的静态地图会保存为PNG文件。
- **栅格切片** 可以作为传统网页的地图数据库，例如 [Mapbox.js](https://mapbox.com/mapbox.js), [Leaflet](https://mapbox.com/help/define-leaflet), 矢量图层等。反馈的栅格切片会保存为JPGE文件，默认尺寸为512×512像素。
对静态API的Swift和Objective-C支持由[MapboxStatic.swift](https://github.com/mapbox/MapboxStatic.swift/) 数据库提供。

利用 [Static API playground](https://www.mapbox.com/help/static-api-playground) 制作一个放缩和平移功能的交互地图。

**限制**

- 静态地图: 默认速率是每分钟600次请求。
- 格切片: 默认速率是每分钟2000次请求。
- 超过这些速率限制会导致`HTTP 429`错误。有关速率限制标头的信息可以在 [速率限制](#rate-limits)查看。

如果你需要更高的服务速率，请[联系我们](https://www.mapbox.com/contact/)。

```python
from mapbox import StaticStyle
```

```javascript
const mbxStatic = require('@mapbox/mapbox-sdk/services/static');
const staticClient = mbxStatic({ accessToken: '{your_access_token}' });
```

### 从一种样式恢复到静态地图

```endpoint
GET /styles/v1/{username}/{style_id}/static/{overlay}/{lon},{lat},{zoom},{bearing},{pitch}{auto}/{width}x{height}{@2x} styles:tiles
```

从一种特定的样式恢复到作为PNG的静态地图。静态地图端点的使用速率受到访问令牌的限制。默认下速率限制为每分钟600次。

发布地图的位置既可以`auto`选择也可以通过：经度，纬度，放缩程度，视角方向和倾斜角度，这五个参数控制。后两个参数，视角方向和倾斜程度是可选择的。如果您只需要特定的视角方向而不需要调整倾斜程度，倾斜视角会默认为`0`。如果您不需要调整任何参数，它们都会默认为`0`。如果你想`auto`选择，就不需要提供任何这些参数。

参数 | 参数描述
---------- | ------------
`username` | 是这个样式所属人的账号名称。
`style_id` | 是创建这个静态地图样式所属的ID。
`overlay` | 一个或多个逗号分隔的要素，可以在请求时应用于地图的顶部。覆盖层中的要素顺序决定了它们在页面上的z方向上的顺序，位于列表中的最后一项会在最上层（覆盖在列表中其他要素上）并且位于列表中的第一项会在最下面（处于列表中所有要素的最下层）。最终格式可以是`geojson`, `marker`, 和 `path`的结合体。对于每个选项的更多细节，可以在 [Overlay options section](#overlay-options)中查看。
`lon` | 为静态地图的中心点的经度;位于数字 `-180` 和 `180`之间。
`lat` | 为静态地图的中心点的纬度;位于数字 `-90` 和 `90`之间。
`zoom` | 放缩程度; 位于数字 `0` 和 `20`之间。缩放级别的参数将四舍五入到小数点后两位。
`bearing`<br /> (可选) | 方位视角以其中心为中心旋转地图。位于数字 `0` 和 `360`之间，以十进制计数。可以顺时针或者逆时针旋转地图90°，但180°翻转地图，会默认旋转度数为`0`。
`auto` | 如果添加`auto`选择, 视角会结合覆盖层的边界来调整。如果应用`auto`，它会取代其他五个参数`lon`, `lat`, `zoom`, `bearing`, `pitch`
`width` | 图像宽度; 位于数值 `1` 和 `1280` 像素之间。
`height` | 像高度; 位于数值 `1` 和d `1280` 像素之间。
`@2x`<br /> (可选) | 在`@2x`比例因子下呈现静态地图为高密度显示。

您可以使用以下可选参数进一步细化这个端点的结果。

查询参数 | 参数描述
---------- | ------------
`归属`<br /> (可选参数) | 用一个“布尔值”，控制图像上的图像归属。默认为`true`。 **注意:** If `属性=false`, 会从图像中删除水印属性，您仍然有法律责任为使用OpenStreetMap数据的地图提供所属来源，其中包括大部分mapbox的地图。如果需要特定`属性=false`，法律要求你在 [网页或文件的其他地方注明正确的出处](https://www.mapbox.com/help/attribution/#static--print)。
`logo`<br /> (可选) | 一个“布尔”值，控制图像上是否有Mapbox徽标。默认为`true`。
`before_layer` (可选) | 一个`字符串`值，用于控制在样式中插入`overlay`的位置。所有覆盖层将插入到指定的层之前。
**叠加选项**<a id="overlay-options"></a>

_GeoJSON_

```
geojson({geojson})
```

内容提要 | 描述
--- | ---
`geojson` | `{geojson}` 文件必须是一个完整可调用的GeoJSON文件 [simplestyle-spec](https://github.com/mapbox/simplestyle-spec) GeoJSON特性的样式将得到和渲染。styles for GeoJSON features will be respected and rendered.

_标记_

```
{name}-{label}+{color}({lon},{lat})
```

内容提要 | 描述
--- | ---
`name` | 标记的形状与尺寸。有`pin-s` 和 `pin-l`选项。
`label`<br /> (可选) | 标记符号。 选项是一个字母数字标签，从 `a` 到 `z`，从 `0` 到 `99`, 或者是一个能调用的 [Maki](https://www.mapbox.com/maki/) 符号。如果被请求的是一个字母，只限用大写字母。
`color`<br /> (可选) | 一种3或6位十六进制颜色码。
`lon, lat` | 标记中心的位置。当使用非对称标记时，请确保指针位于图像的中心。

_自定义标记_

```
url-{url}({lon},{lat})
```

内容提要 | 描述
--- | ---
`url` | 图像的百分比编码URL.可以是 `PNG` 或 `JPG`格式。
`lon, lat` | 标记中心的位置。当创建一个非对称标记时，比如一个指针，确保指针的尖端位于图像的中心。

自定义标记是根据`Expires`和`Cache-Control`标题缓存的。确保至少将这些标头中的一个设置为适当的值，以防止对定制标记图像的重复请求。

_路径_

```
path-{strokeWidth}+{strokeColor}-{strokeOpacity}+{fillColor}-{fillOpacity}({polyline})
```

[编码的线段](https://developers.google.com/maps/documentation/utilities/polylinealgorithm) 精度为5位小数的静态API可以通过`path`参数使用。

内容提要 | 描述
--- | ---
`线条宽度`<br /> (可选) | 线行程宽度的数额大小。
`线条颜色`<br /> (可选) | 一行描边的3位或6位十六进制颜色代码。
`线条透明度`<br /> (可选) | 在`0`(透明)和`1`(不透明)之间的数字，用于设置线条的透明度。
`填充颜色`<br /> (可选) | 用3或6位十六进制颜色码填充。
`透明度填充`<br /> (可选) | 用`0`(透明)和`1`(不透明)之间的的数值来设置填充透明度。
`线段` | 作为URI组件编码的有效编码线段

#### Example request

```curl
# Retrieve a map at -122.4241 longitude, 37.78 latitude,
# zoom 14.24, bearing 0, and pitch 60. The map
# will be 600 pixels wide and 600 pixels high
curl "https://api.mapbox.com/styles/v1/mapbox/streets-v10/static/-122.4241,37.78,14.25,0,60/600x600?access_token={your_access_token}"

# Retrieve a map at 0 longitude, 10 latitude, zoom 3,
# and bearing 20. Pitch will default to 0.
curl "https://api.mapbox.com/styles/v1/mapbox/streets-v10/static/0,10,3,20/600x600?access_token={your_access_token}"

# Retrieve a map at 0 longitude, 0 latitude, zoom 2.
# Bearing and pitch default to 0.
curl "https://api.mapbox.com/styles/v1/mapbox/streets-v10/static/0,0,2/600x600?access_token={your_access_token}"

# Retrieve a map with a custom marker overlay
curl "https://api.mapbox.com/styles/v1/mapbox/streets-v10/static/url-https%3A%2F%2Fwww.mapbox.com%2Fimg%2Frocket.png(-76.9,38.9)/-76.9,38.9,15/1000x1000?access_token={your_access_token}"

# Retrieve a map with a GeoJSON overlay
curl "https://api.mapbox.com/styles/v1/mapbox/streets-v10/static/geojson(%7B%22type%22%3A%22Point%22%2C%22coordinates%22%3A%5B-73.99%2C40.7%5D%7D)/-73.99,40.70,12/500x300?access_token={your_access_token}"

# Retrieve a map with 2 points and a polyline overlay,
# with its center point automatically determined with `auto`
curl "https://api.mapbox.com/styles/v1/mapbox/streets-v10/static/pin-s-a+9ed4bd(-122.46589,37.77343),pin-s-b+000(-122.42816,37.75965),path-5+f44-0.5(%7DrpeFxbnjVsFwdAvr@cHgFor@jEmAlFmEMwM_FuItCkOi@wc@bg@wBSgM)/auto/500x300?access_token={your_access_token}"
```

```javascript
staticClient
  .getStaticImage({
    ownerId: 'mapbox',
    styleId: 'streets-v10',
    width: 200,
    height: 300,
    coordinates: [12, 13],
    zoom: 4
  })
  .send()
  .then(response => {
    const image = response.body;
  });

staticClient
  .getStaticImage({
    ownerId: 'mapbox',
    styleId: 'streets-v10',
    width: 200,
    height: 300,
    coordinates: [12, 13],
    zoom: 3,
    overlays: [
      // Simple markers.
      {
        marker: {
          coordinates: [12.2, 12.8]
        }
      },
      {
        marker: {
          size: 'large',
          coordinates: [14, 13.2],
          label: 'm',
          color: '#000'
        }
      },
      {
        marker: {
          coordinates: [15, 15.2],
          label: 'airport',
          color: '#ff0000'
        }
      },
      // Custom marker
      {
        marker: {
          coordinates: [10, 11],
          url:
            'https://upload.wikimedia.org/wikipedia/commons/6/6f/0xff_timetracker.png'
        }
      }
    ]
  })
  .send()
  .then(response => {
    const image = response.body;
  });

// To get the URL instead of the image, create a request
// and get its URL without sending it.
const request = staticClient
  .getStaticImage({
    ownerId: 'mapbox',
    styleId: 'streets-v10',
    width: 200,  
    height: 300,
    coordinates: [12, 13],
    zoom: 4
  });
const staticImageUrl = request.url();
// Now you can open staticImageUrl in a browser.
```

```python
response = service.image(
    username='mapbox',
    style_id='streets-v9',
    lon=-122.7282, lat=45.5801, zoom=12)
# if features are provided the map image will be centered on them
portland = {
    'type': 'Feature',
    'properties': {'name': 'Portland, OR'},
    'geometry': {
        'type': 'Point',
        'coordinates': [-122.7282, 45.5801]}}
bend = {
    'type': 'Feature',
    'properties': {'name': 'Bend, OR'},
    'geometry': {
        'type': 'Point',
        'coordinates': [-121.3153, 44.0582]}}
response = service.image(
    username='mapbox',
    style_id='streets-v9',
    features=[portland, bend])
```

```bash
# This API cannot be accessed with Mapbox CLI
```

```java
MapboxStaticImage staticImage = MapboxStaticMap.builder()
  .accessToken("{your_access_token}")
  .styleId(Constants.MAPBOX_STYLE_STREETS)
  .cameraPoint(Point.fromLngLat(imageTarget.getLongitude(), imageTarget.getLatitude())))
  .cameraZoom(imageZoom)
  .cameraPitch(60)
  .cameraBearing(45)
  .width(width)
  .height(height)
  .retina(true)
  .build();
```

```objc
@import MapboxStatic;

NSURL *styleURL = [NSURL URLWithString:@"mapbox://styles/mapbox/streets-v10"];
CLLocationCoordinate2D coordinate = CLLocationCoordinate2DMake(37.78, -122.4241);
MBSnapshotCamera *camera = [MBSnapshotCamera cameraLookingAtCenterCoordinate:coordinate zoomLevel:14.25];
camera.pitch = 60;
// Dimensions are measured in resolution-independent points.
CGSize size = CGSizeMake(600, 600);
MBSnapshotOptions *options = [[MBSnapshotOptions alloc] initWithStyleURL:styleURL camera:camera size:size];

MBSnapshot *snapshot = [[MBSnapshot alloc] initWithOptions:options accessToken:@"<#your access token#>"];

// Retrieve the image synchronously, blocking the calling thread.
// `image` is a `UIImage` on iOS, watchOS, and tvOS and an `NSImage` on macOS.
self.imageView.image = snapshot.image;

// Alternatively, pass a completion handler to run asynchronously on the main thread.
[snapshot imageWithCompletionHandler:^(UIImage * _Nullable image, NSError * _Nullable error) {
    self.imageView.image = image;
}];
```

```swift
import MapboxStatic

let camera = SnapshotCamera(
    lookingAtCenter: CLLocationCoordinate2D(latitude: 37.78, longitude: -122.4241),
    zoomLevel: 14.25)
camera.pitch = 60
let options = SnapshotOptions(
  styleURL: styleURL,
  camera: camera,
  // Dimensions are measured in resolution-independent points.
  size: CGSize(width: 200, height: 200))

// If Snapshot conflicts with another class in your module, use `MapboxStatic.Snapshot`.
let snapshot = Snapshot(
  options: options,
  accessToken: "<#your access token#>")

// Retrieve the image synchronously, blocking the calling thread.
// `image` is a `UIImage` on iOS, watchOS, and tvOS and an `NSImage` on macOS.
imageView.image = snapshot.image

// Alternatively, pass a completion handler to run asynchronously on the main thread.
snapshot.image { (image, error) in
    imageView.image = image
}
```

### Retrieve raster tiles from styles

```endpoint
GET /styles/v1/{username}/{style_id}/tiles/{tilesize}/{z}/{x}/{y}{@2x}
```

Retrieve 512x512 pixel or 256x256 pixel raster tiles from a Mapbox Studio style. The returned raster tile will be a JPEG.

Libraries like Mapbox.js and Leaflet.js use this endpoint to render raster tiles from a Mapbox Studio style with [`L.mapbox.styleLayer`](https://www.mapbox.com/mapbox.js/api/v2.4.0/l-mapbox-stylelayer/) and [`L.tileLayer`](http://leafletjs.com/reference-1.2.0.html#tilelayer). By default, the rate limit is set to 2,000 requests per minute.

URl parameter | Description
--- | ---
`username` | The username of the account to which the style belongs.
`style_id` | The ID of the style from which to return a raster tile.
`tilesize`<br> (optional) | Default is 512x512 pixels. (512x512 image tiles are offset by 1 zoom level compared to 256x256 tiles from Mapbox Studio Classic styles. For example, 512x512 tiles at zoom level 4 are equivalent to Mapbox Studio Classic styles tiles at zoom level 5.) 256x256 tiles from the endpoint are one quarter of the size of 512x512 tiles. Therefore, they require 4 times as many API requests and accumulate 4 times as many map views to render the same area.
`{z}/{x}/{y}` | The tile coordinates as described in the [Slippy Map Tilenames specification](http://wiki.openstreetmap.org/wiki/Slippy_map_tilenames). They specify the tile's zoom level `{z}`, column `{x}`, and row `{y}`.
`@2x`<br> (optional) | Render the raster tile at a `@2x` scale factor, so tiles are scaled to 1024x1024 pixels.

#### Example request

```curl
# Returns a default 512x512 pixel tile
curl "https://api.mapbox.com/styles/v1/mapbox/streets-v10/tiles/1/1/0?access_token={your_access_token}"

# Returns a 256x256 pixel tile
curl "https://api.mapbox.com/styles/v1/mapbox/streets-v10/tiles/256/1/1/0?access_token={your_access_token}"

# Returns a 1024x1024 pixel tile
curl "https://api.mapbox.com/styles/v1/mapbox/streets-v10/tiles/512/1/1/0@2x?access_token={your_access_token}"
```

```javascript
// This API cannot be accessed with the JavaScript SDK
```

```python
# This API cannot be accessed with the Python SDK
```

```bash
# This API cannot be accessed with Mapbox CLI
```

```java
// This API cannot be accessed with the Mapbox Java libraries
```

```objc
// This API cannot be accessed with the Mapbox Objective-C libraries
```

```swift
// This API cannot be accessed with the Mapbox Swift libraries
```

### Retrieve a map's WMTS document

```endpoint
GET /styles/v1/{username}/{style_id}/wmts
```

Mapbox supports access via the [WMTS](http://www.opengeospatial.org/standards/wmts) standard, which lets you use maps with desktop and online GIS software like ArcMap and QGIS.

URL parameter | Description
--- | ---
`username` | The username of the account to which the style belongs.
`style_id` | The ID of the style for which to return a WMTS document.

#### Example request

```curl
curl "https://api.mapbox.com/styles/v1/mapbox/streets-v10/wmts?access_token={your_access_token}"
```

```javascript
// This API cannot be accessed with the JavaScript SDK
```

```python
# This API cannot be accessed with the Python SDK
```

```bash
# This API cannot be accessed with Mapbox CLI
```

```java
// This API cannot be accessed with Mapbox Java libraries
```

```objc
// This API cannot be accessed with the Mapbox Objective-C libraries
```

```swift
// This API cannot be accessed with the Mapbox Swift libraries
```
