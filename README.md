# Natural Earth Datasette
This experiment is based on the excelent tutorial on [Simon Willison's Page](https://til.simonwillison.net/gis/natural-earth-in-spatialite-and-datasette).

---

<img src="../assets/08-natural-earth-german-rails.png" width="400" alt="screenshot">

--- 


However I found that the original database does not play nicely with spatialite geometry functions, so the solution is to convert the geometries to its native format first.

## 1. Download the Database

Download the database from:

- [Natural Earth Database](http://naciscdn.org/naturalearth/packages/natural_earth_vector.sqlite.zip) on the CDN of the Northamerican Cartographic Information Society (this may be quite slow)

## 2. Convert the Shapes from WKB to Spatialite Format

You need `ogr2ogr` from [GDAL](https://gdal.org/index.html).  
Install GDAL first.

On OSX:  
```sh
brew install gdal
```

Now convert the database:  
```sh
ogr2ogr -f sqlite -dsco spatialite=yes natural-earth.sqlite natural_earth_vector.sqlite -nlt promote_to_multi
 ```

To make the database indexing work, we need to perform one more important step!  
```sh
sqlite3 natural-earth.sqlite ANALYZE
```

## 3. Extensions

The `spatialite` extension is required for geographic queries.  
See [datasette documentation](https://docs.datasette.io/en/stable/spatialite.html) on how to install it.

## 4. Plugins

The following plugins are used in this experiment:

- **datasette-darkmode** (optional)  
  *to show the datasette in darkmode (including the sql editor)*
- [datasette-geojson-map](https://datasette.io/plugins/datasette-geojson-map)  
  *render a map for any query with a geometry column*
- [datasette-geojson](https://datasette.io/plugins/datasette-geojson)  
  *to add GeoJSON as an output option for datasette queries*
- [datasette-leaflet](https://datasette.io/plugins/datasette-leaflet)  
  *to add the Leaflet JavaScript library.   
  (required by datasette-leaflet-geojson)*
- [sqlite-colorbrewer](https://datasette.io/plugins/sqlite-colorbrewer)  
  *to use ColorBrewer scales in SQLite queries*

They can be installed using `datasette install`.

## 5. Settings
Settings are configured in 
- `settings.json` for Datasette 0.x
- `datasette.yaml` for Datasette 1.x

Important Settings are:
  - `max_returned_rows: 3000` to show up to 3000 results on the map
  - `default_page_size: 20` to limit the number of rows when exploring tables
  - `sql_time_limit_ms: 10000` to allow queries taking up to 10 seconds to complete

## 6. Start Datasette
The `start.sh` script loads extensions and plugins.
 
## 7. Canned Queries
A couple of [canned queries](https://docs.datasette.io/en/latest/sql_queries.html#canned-queries) have been added to the configuration:

### 7.1. Urban Areas
*Get largest urban areas:*

<img src="../assets/01-natural-earth-urban-areas.png" width="400" alt="screenshot">

Link: http://127.0.0.1:8001/natural-earth/urban-areas

### 7.2 Borders
*Country bordes at 50m detail*

<img src="../assets/02-natural-earth-borders.png" width="400" alt="screenshot">

Link: http://127.0.0.1:8001/natural-earth/borders

### 7.3 Color Countries
*Color countries using 9 colors from colorbrewer*

<img src="../assets/03-natural-earth-color-countries.png" width="400" alt="screenshot">

Link: http://127.0.0.1:8001/natural-earth/color-countries

### 7.4 Blue Countries
*Colors of the world using 7 shades of blue*

<img src="../assets/04-natural-earth-blue-countries.png" width="400" alt="screenshot">

Link: http://127.0.0.1:8001/natural-earth/blue-countries

### 7.5 Coast Lines
*Where water meets earth...*

<img src="../assets/05-natural-earth-coast-lines.png" width="400" alt="screenshot">

Link: http://127.0.0.1:8001/natural-earth/coast-lines

### 7.6 Rivers
*All rivers, with their German names*

<img src="../assets/06-natural-earth-rivers.png" width="400" alt="screenshot">

Link: http://127.0.0.1:8001/natural-earth/rivers

### 7.7 European Rivers
*European rivers, with their German names*

<img src="../assets/07-natural-earth-rivers-europe.png" width="400" alt="screenshot"> 

Link: http://127.0.0.1:8001/natural-earth/eu-rivers

### 7.8 German Rails
*All Railroads within Germany (slow)*

<img src="../assets/08-natural-earth-german-rails.png" width="400" alt="screenshot"> 

Link: http://127.0.0.1:8001/natural-earth/german-rails

### 7.9 Global Rails
*Global Railroad Network Search (slow)*

<img src="../assets/09-natural-earth-global-rails.png" width="400" alt="screenshot">

Link: http://127.0.0.1:8001/natural-earth/global-rails?country=France

### 7.10 Globals Rails (Fast)
*Global Railroad Network Indexed Search (fast)*  
Link: http://127.0.0.1:8001/natural-earth/global-rails-fast?country=France
