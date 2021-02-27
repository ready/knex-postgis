# knex-postgis
[![npm version](http://img.shields.io/npm/v/knex-postgis.svg)](https://npmjs.org/package/knex-postgis)
[![npm downloads](https://img.shields.io/npm/dm/knex-postgis.svg)](https://npmjs.org/package/knex-postgis)
[![Build Status](https://travis-ci.org/jfgodoy/knex-postgis.svg?branch=master)](https://travis-ci.org/jfgodoy/knex-postgis)
[![Dependencies Status](https://img.shields.io/david/jfgodoy/knex-postgis)](https://david-dm.org/jfgodoy/knex-postgis)
[![License](https://img.shields.io/github/license/jfgodoy/knex-postgis)](https://github.com/jfgodoy/knex-postgis/blob/master/LICENSE)

Extension for use postgis functions in [knex](http://knexjs.org) SQL query builder.



## Example
This example show the sql generated by the extension.


```js
const knex = require('knex');
const knexPostgis = require('knex-postgis');

const db = knex({
  client: 'postgres'
});

// install postgis functions in knex.postgis;
const st = knexPostgis(db);
/* or:
 * knexPostgis(db);
 * const st = db.postgis;
 */

// insert a point
const sql1 = db.insert({
  id: 1,
  geom: st.geomFromText('Point(0 0)', 4326)
}).into('points').toString();
console.log(sql1);
// insert into "points" ("geom", "id") values (ST_geomFromText('Point(0 0)'), '1')

// find all points return point in wkt format
const sql2 = db.select('id', st.asText('geom')).from('points').toString();
console.log(sql2);
// select "id", ST_asText("geom") as "geom" from "points"

// all methods support alias
const sql3 = db.select('id', st.asText(st.centroid('geom')).as('centroid')).from('geometries').toString();
console.log(sql3);
// select "id", ST_asText(ST_centroid("geom")) as "centroid" from "geometries"

```

## Currently supported functions

- area(geom), see [postgis documentation](https://postgis.net/docs/ST_Area.html)
- asText(column), see [postgis documentation](https://postgis.net/docs/ST_AsText.html)
- asGeoJSON(column), see [postgis documentation](https://postgis.net/docs/ST_AsGeoJSON.html)
- asEWKT(column), see [postgis documentation](http://www.postgis.net/docs/ST_AsEWKT.html)
- buffer(geom, radius), see [postgis documentation](http://www.postgis.net/docs/ST_Buffer.html)
- centroid(geom), see [postgis documentation](https://postgis.net/docs/ST_Centroid.html)
- distance(geom, geom), see [postgis documentation](https://postgis.net/docs/ST_Distance.html)
- distanceSphere(geom, geom), see [postgis documentation](https://postgis.net/docs/manual-1.4/ST_Distance_Sphere.html)
- dwithin(geom, geom, distance, /* optional bool spheroid */), see [postgis documentation](https://postgis.net/docs/ST_DWithin.html)
- intersection(geom1, geom2), see [postgis documentation](https://postgis.net/docs/ST_Intersection.html)
- intersects(geom1, geom2), see [postgis documentation](https://postgis.net/docs/ST_Intersects.html)
- geography(geom)
- geographyFromText(ewkt), see [postgis documentation](https://postgis.net/docs/ST_GeographyFromText.html)
- geometry(geography)
- geomFromText(geom, srid), see [postgis documentation](http://www.postgis.net/docs/ST_GeomFromText.html)
- geomFromGeoJSON(geojson /*object, string or column name*/), see [postgis documentation](https://postgis.net/docs/ST_GeomFromGeoJSON.html)
- makeEnvelope(minlon, minlat, maxlon, maxlat, /* optional integer SRID */), see [postgis documentation](http://www.postgis.net/docs/ST_MakeEnvelope.html)
- makePoint(lon, lat, /* optional z */, /* optional measure */), see [postgis documentation](https://postgis.net/docs/ST_MakePoint.html)
- makeValid(geom), see [postgis documentation](https://postgis.net/docs/ST_MakeValid.html)
- point(lon, lat), see [postgis documentation](https://postgis.net/docs/ST_Point.html)
- transform(geom, srid), see [postgis documentation](https://postgis.net/docs/ST_Transform.html)
- within(geom, geom), see [postgis documentation](https://postgis.net/docs/ST_Within.html)
- setSRID(geom, srid), see [postgis documentation](https://postgis.net/docs/ST_SetSRID.html)
- x, see [postgis documentation](https://postgis.net/docs/ST_X.html)
- y, see [postgis documentation](https://postgis.net/docs/ST_Y.html)
- z, see [postgis documentation](https://postgis.net/docs/ST_Z.html)
- m, see [postgis documentation](https://postgis.net/docs/ST_M.html)
- boundingBoxIntersects(geom a, geom b), represented as `a && b`, see [postgis documentation](http://postgis.net/docs/manual-2.0/geometry_overlaps.html)
- boundingBoxContained(geom a, geom b), represented as `a @ b`, see [postgis documentation](http://postgis.net/docs/manual-2.0/ST_Geometry_Contained.html)
- boundingBoxContains(geom a, geom b), represented as `a ~ b`, see [postgis documentation](http://postgis.net/docs/manual-2.0/ST_Geometry_Contain.html)
- multi(geom), see [postgis documentation](https://postgis.net/docs/ST_Multi.html)
## Define extra functions

```js
const knex = require('knex');
const knexPostgis = require('knex-postgis');

const db = knex({
  client: 'postgres'
});

knexPostgis(db);

db.postgisDefineExtras((knex, formatter) => ({
  utmzone(geom) {
    return knex.raw('utmzone(?)', [formatter.wrapWKT(geom)]);
  }
}));
//now you can use st.utmzone function in the same way as predefined functions
```
