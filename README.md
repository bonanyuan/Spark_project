# Parse and Manipulate Bus Time Data using PySpark

##### This project reads 3 TB of nested JSON data and apply bunch of spark techniques to analyze the bus delays and headways
Original [Sample Data](https://raw.githubusercontent.com/sarangof/Bus-Capstone/master/Spark/test.jsons) and [Schema](https://github.com/sarangof/Bus-Capstone/blob/master/Spark/schema.txt)
## Techniques Included

- __Read JSON__
```
sqlContext.read.json()
```
- __Extract elements from JSON using Spark SQL Query__

    `SELECT XXX FROM table`. Check [spark_extract.sql](https://github.com/sarangof/Bus-Capstone/blob/master/Spark/spark_extract.sql) for details

    Check [Constructed Schema](https://github.com/bonanyuan/Spark_project#data-schema)

- __Flatten Arrays__ using `flatMap(zip([columns]))`
```
(1,2,3) (a,b,c) ($,#,&) => (1,a,$),(2,b,#),(3,c,&)
```
- __groupByKey & Interpolate__

  * use `groupBykey` to cast interpolation of time&distance to all trips.

  * use Scipy Interpolate Tool to interpolate all stop times.

  For more information:[Scipy Interpolate1D](http://docs.scipy.org/doc/scipy/reference/generated/scipy.interpolate.interp1d.html#scipy.interpolate.interp1d)

- __Read CSV using Spark_CSV tool__
    ```
    sqlContext.read.format('com.databricks.spark.csv').options(header='true').load()
    ```
for more info,check [Spark_CSV_package](https://github.com/databricks/spark-csv)

- __SparkSQL manipulation__

   * `Inner join`  to join the interpolated data with GTFS schedule

   * `IF` & `COUNT` to calculate the time performance by comparing with GTFS Schedule Data.

    *   `IF` clause in spark is same as `CASE` in regular SQL

    For more info.check [ontime_ratio.sql](https://github.com/sarangof/Bus-Capstone/blob/master/Spark/ontime_ratio/ontime_ratio.sql)

## Data Schema
| JSON ELEMENT(schema)                           | Column NAME    | explanation                                   |
|------------------------------------------------|----------------|-----------------------------------------------|
| LineRef                                        | ROUTE_ID       | Name of bus line(B42)                         |
| VehicleLocation.Latitude                       | latitude       | latitude of record                            |
| VehicleLocation.Longitude                      | longitude      | longitude of record                           |
| RecordedAtTime                                 | recorded_time  | What time it get recorded                     |
| VehicleRef                                     | vehicle_id     | ID of vehicle                                 |
| FramedVehicleJourneyRef.DatedVehicleJourneyRef | TRIP_ID        | Same as trip_id in GTFS*                      |
| FramedVehicleJourneyRef.DataFrameRef           | trip_date      | Date of the trip                              |
| JourneyPatternRef                              | SHAPE_ID       | Same as shape_id in GTFS*                     |
| StopPointRef                                   | STOP_ID        | Id of next stop(Same as stop_id in GTFS)      |
| Extensions.Distances.DistanceFromCall          | distance_stop  | Distance to next stop                         |
| Extensions. Distances.CallDistanceAlongRoute   | distance_shape | Stop_s total distance along the shape         |
| Extensions. Distances.PresentableDistance      | status         | Report the current status of bus to next stop |
| DestinationRef                                 | destination    | Headsign of bus                               |
## Final Product
Darker means poorer on time performance for the buses

![alt text](https://github.com/sarangof/Bus-Capstone/blob/master/plots/on_time_performance_stops.png "Sample of on time performance")

Open Interactive Map in [Carto Map](https://saf537.carto.com/viz/c21efdeb-ec45-45f2-b2d3-c47993bb89ff/public_map)
