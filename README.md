# tipping_trouble

5/27/24:

For the sake of doing a project that will actually have some direct benefit to me, I found some interesting data from Google BigQuery with taxis in Chicago. Starting with that, we'll explore the trends of which features contribute the most to tips and try to expand to other industries as well (unsure at the moment where to find that data, but that will be part of the journey).

Ideally, I would really like to examine if tipping 'actually pays the bills' for the workers as even in industries such as food-service/restaurants (America) as in short I find it deplorable the notion that workers have to rely on tips for their base salary. However, likely that might get too politcal so I'll avoid writing that here.

## From BigQuery

Accessed 5/28/24 'chicago_taxi_trips'. The overall tipping rate was .38 (below)

### Appendix

Overall tipping rate:

SELECT ROUND(COUNT(tips) /
    (SELECT COUNT(tips)
    FROM bigquery-public-data.chicago_taxi_trips.taxi_trips)
, 2)
FROM bigquery-public-data.chicago_taxi_trips.taxi_trips
WHERE tips > 0;

Biggest (named) taxi companies:

--First was null, for the record
SELECT company, COUNT(tips) as trips
FROM bigquery-public-data.chicago_taxi_trips.taxi_trips
GROUP BY company
ORDER BY trips DESC;

Data used first:

SELECT *, ROUND(tips/ NULLIF(fare, 0), 2) AS tip_percentage
FROM bigquery-public-data.chicago_taxi_trips.taxi_trips
WHERE tips > 0
AND company IN ('Flash Cab', 'Taxi Affiliation Services', 'Yellow Cab', 'Chicago Carriage Cab Corp', 'City Service', 'Sun Taxi')
ORDER BY trip_start_timestamp
LIMIT 20000;

Data used nexy (see ceda_ii):
(Wanting to have 'actual trips' instead of more murky data with cancelatons or unknowns filled in (more likely than filled data) I added two more conditions in the WHERE. Additionally, I decided to order by taxi_id to approach the 'human element', giving us a total of 208 taxis within the month of January 2015.

SELECT taxi_id, trip_start_timestamp, trip_seconds, trip_miles, pickup_community_area, dropoff_community_area, fare, tips, tolls, extras, trip_total, payment_type, company
FROM bigquery-public-data.chicago_taxi_trips.taxi_trips
WHERE tips > 0
AND trip_seconds > 0
AND trip_miles > 0
AND company IN ('Flash Cab', 'Taxi Affiliation Services', 'Yellow Cab', 'Chicago Carriage Cab Corp', 'City Service', 'Sun Taxi')
AND trip_start_timestamp BETWEEN '2015-01-01 00:00:00.000000 UTC' AND '2015-01-31 23:45:00.000000 UTC'
ORDER BY taxi_id
LIMIT 40000;

