# [GSP327 Engineer Data in Google Cloud: Challenge Lab.sql](https://www.cloudskillsboost.google/focuses/12379?parent=catalog)
<details><summary>Task 1. Clean your training data</summary>
</br>

CREATE OR REPLACE TABLE
  taxirides.<Table_Name_as_mention_in_lab> AS
SELECT
  (tolls_amount + fare_amount) AS <Fare Amount_as_mention_in_lab>,
  pickup_datetime,
  pickup_longitude AS pickuplon,
  pickup_latitude AS pickuplat,
  dropoff_longitude AS dropofflon,
  dropoff_latitude AS dropofflat,
  passenger_count AS passengers,
FROM
  taxirides.historical_taxi_rides_raw
WHERE
  RAND() < 0.001
  AND trip_distance > 3 [Change_as_mention_in_lab]
  AND fare_amount >= 2.0 [Change_as_mention_in_lab]
  AND pickup_longitude > -78
  AND pickup_longitude < -70
  AND dropoff_longitude > -78
  AND dropoff_longitude < -70
  AND pickup_latitude > 37
  AND pickup_latitude < 45
  AND dropoff_latitude > 37
  AND dropoff_latitude < 45
  AND passenger_count > 3 [Change_as_mention_in_lab]
</details>

<details><summary>Task 2. Create a BigQuery ML model</summary>
</br>

CREATE OR REPLACE MODEL
  taxirides.<MODEL Name_as_mention_in_lab> TRANSFORM( * EXCEPT(pickup_datetime),
    ST_DISTANCE(ST_GEOGPOINT(pickuplon, pickuplat), ST_GEOGPOINT(dropofflon, dropofflat)) AS euclidean,
    CAST(EXTRACT(DAYOFWEEK
      FROM
        pickup_datetime) AS STRING) AS dayofweek,
    CAST(EXTRACT(HOUR
      FROM
        pickup_datetime) AS STRING) AS hourofday ) OPTIONS(input_label_cols=[<Fare Amount_as_mention_in_lab>'], model_type='linear_reg')
AS
  SELECT
    *
  FROM
    taxirides.<Table_Name_as_mention_in_lab>

</details>

<details><summary>Task 3. Perform a batch prediction on new data</summary>
</br>

CREATE OR REPLACE TABLE
  taxirides.2015_fare_amount_predictions AS
SELECT
  *
FROM
  ML.PREDICT(MODEL taxirides.<MODEL Name_as_mention_in_lab>,
    (
    SELECT
      *
    FROM
      taxirides.report_prediction_data) )

</details>

