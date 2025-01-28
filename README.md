# ZoomCamp-DE-Module1 Homework

## Question 1: Finding the pip version
To find the pip version in our docker container, we follow these steps:

i. Run docker with the python:3.12.8 image in an interactive mode, use the entrypoint bash
`docker run -it --entrypoint bash python:3.12.8`
ii. Then run the following command inside the container
`pip --version`
### Answer : 24.3.1

## Question 2: Hostname and Port for DB Connection
Given the docker-compose.yaml:

The hostname to connect to the Postgres database from pgadmin is the localhost.
The mapped port for Postgres is 5433 (host) to 5432 (container).
### Answer: localhost:5433

## Question 3: Trip Segmentation Count
Query:
```
SELECT
    SUM(CASE WHEN trip_distance <= 1 THEN 1 ELSE 0 END) AS "Up to 1 mile",
    SUM(CASE WHEN trip_distance > 1 AND trip_distance <= 3 THEN 1 ELSE 0 END) AS "1 to 3 miles",
    SUM(CASE WHEN trip_distance > 3 AND trip_distance <= 7 THEN 1 ELSE 0 END) AS "3 to 7 miles",
    SUM(CASE WHEN trip_distance > 7 AND trip_distance <= 10 THEN 1 ELSE 0 END) AS "7 to 10 miles",
    SUM(CASE WHEN trip_distance > 10 THEN 1 ELSE 0 END) AS "Over 10 miles"
FROM
    green_tripdata_2019_10
WHERE
    lpep_pickup_datetime >= '2019-10-01'
    AND lpep_pickup_datetime < '2019-11-01';
```

### Answer: 104,802; 198,924; 109,603; 27,678; 35,189

## Question 4: Longest trip for each day
Query:
```
SELECT
    DATE(lpep_pickup_datetime)
 FROM
    green_tripdata_2019_10
 WHERE
    trip_distance = (
        SELECT MAX(trip_distance)
        FROM green_tripdata_2019_10 );
```

### Answer: 2019-10-31

## Question 5: Three biggest pickup zones
Query:
```
WITH biggest_pickup_loc_id AS (
    SELECT "PULocationID"
    FROM green_tripdata_2019_10
    WHERE DATE(lpep_pickup_datetime) = '2019-10-18'
    GROUP BY "PULocationID"
    HAVING SUM(total_amount) > 13000.0
)
SELECT "Zone"
FROM taxi_zone_lookup
WHERE "LocationID" IN (SELECT * FROM biggest_pickup_loc_id);
```

### Answer: East Harlem North, East Harlem South, Morningside Heights

## Question 6: Largest tip
Query:
```
SELECT
    zn_do."Zone"
FROM
    green_tripdata_2019_10 AS tr
    INNER JOIN taxi_zone_lookup AS zn_pu
        ON tr."PULocationID" = zn_pu."LocationID"
    INNER JOIN taxi_zone_lookup AS zn_do
        ON tr."DOLocationID" = zn_do."LocationID"
WHERE DATE_TRUNC('MONTH', tr.lpep_pickup_datetime) = '2019-10-01'
  AND zn_pu."Zone" = 'East Harlem North'
ORDER BY tr.tip_amount DESC
LIMIT 1;
```

### Answer: JFK Airport

## Question 7: Terraform Workflow

### Answer:
terraform init, terraform apply -auto-approve, terraform destroy
