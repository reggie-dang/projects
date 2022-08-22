London Fire Brigade Service Calls SQL Exploration
================
Reggie
05/18/2022

## Fire Brigade Service Calls 
Our data set is a public data set sourced from Google's Bigquery cloud platform.

```
SELECT *
FROM `bigquery-public-data.london_fire_brigade.fire_brigade_service_calls`
LIMIT 5;
```

A cursory glance at the schema shows that we have the following columns: incident_number, date_of_call, time_of_call, hour_of_call, timestamp_of_call, incident_group, stop_code_description, special_service_type, property_type, address_qualifier, postcode_full, postcode_district, borough_code, borough_name, proper_case, ward_code, ward_name, ward_name_new, easting_m, northing_m, easting_rounded, northing_rounded, frs, incident_station_ground, first_pump_arriving_attendance_time, first_pump_arriving_deployed_from_station, second_pump_arriving_attendance_time, second_pump_arriving_deployed_from_station, num_stations_with_pumps_attending.

We can see some nulls in the data already. We will need to make note of which columns have nulls and whether or not they will affect our analysis.

```
SELECT DISTINCT cal_year
FROM `bigquery-public-data.london_fire_brigade.fire_brigade_service_calls`
ORDER BY cal_year;
```

All of our data appears to be from the year 2017.

```
SELECT DISTINCT(incident_group)
FROM `bigquery-public-data.london_fire_brigade.fire_brigade_service_calls`;
```

This shows the various types of calls received. False alarm, fire, and special call.

```
SELECT DISTINCT(property_type)
FROM `bigquery-public-data.london_fire_brigade.fire_brigade_service_calls`
ORDER BY property_type;
```

A list of all the property types that have had service calls.

```
SELECT property_category, COUNT(*) AS num_of_fires
FROM `bigquery-public-data.london_fire_brigade.fire_brigade_service_calls`
WHERE incident_group = "Fire"
GROUP BY property_category
ORDER BY COUNT(*) DESC;
```

Count of actual fires by property category in descending order. The majority of fires occur in residential dwellings, outdoor structures and outdoor locations.

```
SELECT ROUND((
  SELECT COUNT(*)
  FROM `bigquery-public-data.london_fire_brigade.fire_brigade_service_calls`
  WHERE incident_group = "False Alarm") / COUNT(*) * 100, 2) AS percent_of_false_alarms
FROM `bigquery-public-data.london_fire_brigade.fire_brigade_service_calls`;
```

From this query we can see that 48.79% of total calls are false alarms.

```
SELECT ROUND((
  SELECT COUNT(*)
  FROM `bigquery-public-data.london_fire_brigade.fire_brigade_service_calls`
  WHERE address_qualifier = "Correct incident location") / COUNT(*) * 100, 2) AS location_accuracy
FROM `bigquery-public-data.london_fire_brigade.fire_brigade_service_calls`;
```

59.75% of calls made provided correct location information regarding the incident.

```
SELECT proper_case, COUNT(*) AS calls
FROM `bigquery-public-data.london_fire_brigade.fire_brigade_service_calls`
GROUP BY proper_case
ORDER BY COUNT(*) DESC;
```

Westminster has almost double the amount of calls of the other boroughs.

```
SELECT hour_of_call, COUNT(*) AS num_of_calls
FROM `bigquery-public-data.london_fire_brigade.fire_brigade_service_calls`
GROUP BY hour_of_call
ORDER BY hour_of_call;
```

Number of calls per hour of the day. The low point for calls is between the hours of 3am to 5am. While the most number of calls occur between 5pm and 7pm.

```
SELECT 
  incident_number, 
  date_of_call, 
  time_of_call, 
  hour_of_call, 
  incident_group, 
  stop_code_description, 
  special_service_type, 
  property_category, 
  property_type, 
  address_qualifier, 
  postcode_full, 
  postcode_district, 
  borough_code, 
  borough_name, 
  proper_case, 
  ward_code, 
  ward_name, 
  ward_name_new
FROM `bigquery-public-data.london_fire_brigade.fire_brigade_service_calls`
WHERE proper_case != "Not geo-coded"
ORDER BY proper_case, ward_name, timestamp_of_call;
```

Cleaning the data by excluding the location nulls (normally we would try to figure out why the nulls exist and if we could input the missing data) and sorting by borough, ward, and call timestamp. The data is ready to be exported to our data visualization software.
