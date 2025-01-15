## Question 1. Understanding docker first run



Run docker with the `python:3.12.8` image in an interactive mode, use the entrypoint `bash`.

What's the version of `pip` in the image?

- 24.3.1
- 24.2.1
- 23.3.1
- 23.2.1



## Prepare Postgres



Run Postgres and load data as shown in the videos We'll use the green taxi trips from October 2019:

```
wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/green/green_tripdata_2019-10.csv.gz
```



You will also need the dataset with zones:

```
wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/misc/taxi_zone_lookup.csv
```



Download this data and put it into Postgres.

You can use the code from the course. It's up to you whether you want to use Jupyter or a python script.

## Question 3. Trip Segmentation Count



During the period of October 1st 2019 (inclusive) and November 1st 2019 (exclusive), how many trips, **respectively**, happened:

1. **Up to 1 mile**: 104830

2. **In between 1 (exclusive) and 3 miles (inclusive)**: 198995

3. **In between 3 (exclusive) and 7 miles (inclusive)**: 109642

4. **In between 7 (exclusive) and 10 miles (inclusive)**: 27686

5. **Over 10 miles**: 35201

	---

	## Question 4. Longest trip for each day

	

	Which was the pick up day with the longest trip distance? Use the pick up time for your calculations.

	Tip: For every day, we only care about one single trip with the longest distance.

	- 2019-10-11
	- 2019-10-24
	- 2019-10-26
	- **2019-10-31**

	

	```sql
	SELECT trip_distance, DATE(lpep_pickup_datetime) AS dates
	FROM green_taxi_data_2019
	WHERE EXTRACT(year from lpep_pickup_datetime) = '2019'
		  AND EXTRACT (month from lpep_pickup_datetime) = '10'
	ORDER BY trip_distance DESC## Create file from csv
	```

	---

	## Question 5. Three biggest pickup zones

	

	Which were the top pickup locations with over 13,000 in `total_amount` (across all trips) for 2019-10-18?

	Consider only `lpep_pickup_datetime` when filtering by date.

	- **East Harlem North, East Harlem South, Morningside Heights**
	- East Harlem North, Morningside Heights
	- Morningside Heights, Astoria Park, East Harlem South
	- Bedford, East Harlem North, Astoria Park

	```sql
	WITH a AS (SELECT DATE(lpep_pickup_datetime) AS dates, t."Borough" AS borough, t."LocationID", "PULocationID" AS location_id, t."Zone" AS zones, t.service_zone, trip_distance, tip_amount, fare_amount, tolls_amount
	FROM green_taxi_data g 
	LEFT JOIN taxi_zone t ON t."LocationID" = g."PULocationID")
	
	SELECT location_id, zones, SUM(tolls_amount) AS total
	FROM a
	WHERE dates = '2019-10-18'
	GROUP BY location_id, zones 
	ORDER BY total DESC
	```

	

## Question 6. Largest tip

For the passengers picked up in Ocrober 2019 in the zone name "East Harlem North" which was the drop off zone that had the largest tip?

Note: it's `tip` , not `trip`

We need the name of the zone, not the ID.

- Yorkville West
- JFK Airport
- **East Harlem North**
- East Harlem South

```sql
WITH a AS (SELECT t."Zone" AS zones, g.tip_amount
FROM green_taxi_data g 
JOIN taxi_zone t ON t."LocationID" = g."PULocationID"
WHERE EXTRACT (MONTH from lpep_pickup_datetime) = '10') 


SELECT zones, 
	   SUM(tip_amount) AS total_tip
FROM a 
GROUP BY 1
```



## Question 7. Which of the following sequences, **respectively**, describes the workflow for:

1. Downloading the provider plugins and setting up backend,
2. Generating proposed changes and auto-executing the plan
3. Remove all resources managed by terraform`

Answers:

- terraform import, terraform apply -y, terraform destroy
- teraform init, terraform plan -auto-apply, terraform rm
- terraform init, terraform run -auto-aprove, terraform destroy
- **terraform init, terraform apply -auto-aprove, terraform destroy**
- terraform import, terraform apply -y, terraform rm