# grafana-track-map
(Gps) track map visualisation plugin

Grafana map plugin that allows you to draw tracks on Leaflet-based map on Geohash data from your Grafana backend database.

Selected time is shown on the map as you move mouse over other graphs. You can shift-drag select on the map to zoom in in time.

![image](https://cloud.githubusercontent.com/assets/1049678/22856671/bb093ad4-f09e-11e6-9e61-f1f6125a38b9.png)

The coordinates must be stored as [Geohash](https://en.wikipedia.org/wiki/Geohash) strings in the database. I have used the plugin with InfluxDb with a query like

```
SELECT mean("navigationSpeedOverGround") as "value",first("navigationPosition")  as geohash 
FROM "signalk" WHERE $timeFilter GROUP BY time($interval) fill(null)
```

You have to manually install this under Grafanan plugins directory (/var/lib/grafana/plugins/) and build it with 
```
npm install
npm install -g grunt
grunt
```

The first command installs the needed dependencies, second installs `grunt` build tool and third runs the build.

## OpenNMS Datsource Enhancements

When data is returned from the InfluxDb data source, the plugin expects the first data array entry data[0] to contain the 'value' and the second data[1] to contain a string geohash. Data must be in this order and any other data will be ignored.

This branch allows users to also use the  [OpenNMS Measurements Data Source](https://grafana.com/plugins/opennms-datasource/installation) and possibly other json based data sources. 
The key difference is that latitude and longitude are indexed as separate long values instead of as a string geohash. 
If the a query is defined which returns values with target names 'latitude', 'longitude' (and optionally 'value' - although value isn't used in this version), these will be treated as coordinates for drawing a path on the map. 
If latitude and longitude are not defined, the returned data will be treated just the same as before - as if it was received from the InfluxDb data source.
The following image shows a definition for the OpenNMS data source where the collected data is known to contain latitude and longitude values. 

![Image](../blob/master/src/img/OpenNMSdataSourceExample.png)

