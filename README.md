# Data-Warehouse-Development-for-Waste-Management-using-PostgreSQL
A simple data warehouse development project for waste management using PostgreSQL


# Scenario
(Note: This is the exercise provided by IBM DWH Engineer course , and I am documenting my solution here)

You are a data engineer hired by a solid waste management company. The company collects and recycles solid waste across major cities in the country of Brazil. The company operates hundreds of trucks of different types to collect and transport solid waste. The company would like to create a data warehouse so that it can create reports like

- total waste collected per year per city
- total waste collected per month per city
- total waste collected per quarter per city
- total waste collected per year per trucktype
- total waste collected per trucktype per city
- total waste collected per trucktype per station per city

You will use your data warehousing skills to design and implement a data warehouse for the company.

# Objective
- Design a Data Warehouse
- Load data into Data Warehouse
- Write aggregation queries
- Create MQTs
- Create a Dashboard

  Firstly, The solid waste management company has provied you the sample data they wish to collect.
![image](https://github.com/kwagle7/Data-Warehouse-Development-for-Waste-Management-using-PostgreSQL/assets/13037108/10fcab13-c6c7-4cda-84ba-a512d319f679)

You will start your project by designing a Star Schema warehouse by identifying the columns for the various dimension and fact tables in the schema.



# Solution

First I need to develop the dimension/fact tables such as: MyDimDate, MyDimWaste, MyDimZone, and MyFactTrips and define the attributes based on the requiremtnt/to provide the optimal solution, but I got csv files already in this case (which maynot happen in real scenario))

So, Run the Create_Query.sql, you will get following ERD afterwards, and import the associated csv files to load the data into tables.

![image](https://github.com/kwagle7/Data-Warehouse-Development-for-Waste-Management-using-PostgreSQL/assets/13037108/f1423950-f8d7-4ad5-945e-cf0973442b61)

# Create a grouping sets query
Create a grouping sets query using the columns stationid, trucktype, total waste collected.
```sql
SELECT
    ft."Stationid",
    dt."TruckType",
    SUM(ft."Wastecollected") AS "Total Waste Collected"
FROM
    public."FactTrips" AS ft
LEFT JOIN
    public."DimStation" AS ds ON ds."Stationid" = ft."Stationid"
LEFT JOIN
    public."DimTruck" AS dt ON dt."Truckid" = ft."Truckid"
GROUP BY
    ft."Stationid",
    dt."TruckType"
ORDER BY
    ft."Stationid" ASC;
```
Output:

<img width="481" alt="image" src="https://github.com/kwagle7/Data-Warehouse-Development-for-Waste-Management-using-PostgreSQL/assets/13037108/eb84554d-d8c7-48a3-9e49-83180b61dcdd">

# Create a rollup query
Create a rollup query using the columns year, city, stationid, and total waste collected.

```sql
SELECT
    d."Year",
    s."City",
    ft."Stationid",
    SUM(ft."Wastecollected") AS "Total Waste Collected"
FROM
    public."FactTrips" ft
JOIN
    public."DimDate" d ON ft."Dateid" = d."dateid"
JOIN
    public."DimStation" s ON ft."Stationid" = s."Stationid"
GROUP BY
    ROLLUP (d."Year", s."City", ft."Stationid")
ORDER BY
    d."Year" NULLS LAST, s."City" NULLS LAST, ft."Stationid" NULLS LAST;
```
Output:

<img width="459" alt="image" src="https://github.com/kwagle7/Data-Warehouse-Development-for-Waste-Management-using-PostgreSQL/assets/13037108/bb02f63c-c4f2-4fff-abfa-9186a9d5588d">

# Create a Cube query
Create a cube query using the columns year, city, stationid, and average waste collected.

```sql
SELECT
    d."Year",
    s."City",
    ft."Stationid",
    AVG(ft."Wastecollected") AS "Average Waste Collected"
FROM
    public."FactTrips" ft
JOIN
    public."DimDate" d ON ft."Dateid" = d."dateid"
JOIN
    public."DimStation" s ON ft."Stationid" = s."Stationid"
GROUP BY
    CUBE (d."Year", s."City", ft."Stationid")
ORDER BY
    d."Year" NULLS LAST, s."City" NULLS LAST, ft."Stationid" NULLS LAST;
```
Output:

<img width="511" alt="image" src="https://github.com/kwagle7/Data-Warehouse-Development-for-Waste-Management-using-PostgreSQL/assets/13037108/7672cf01-4eee-41de-b913-4d840a25e399">

# Create an Materialized Query Table (MQT)
Create an MQT named max_waste_stats using the columns city, stationid, trucktype, and max waste collected.

```sql
CREATE MATERIALIZED VIEW max_waste_stats AS
SELECT
    ds."City",
    ft."Stationid",
    dt."TruckType",
    MAX(ft."Wastecollected") AS "Max Waste Collected"
FROM
    public."FactTrips" ft
JOIN
    public."DimStation" ds ON ft."Stationid" = ds."Stationid"
JOIN
    public."DimTruck" dt ON ft."Truckid" = dt."Truckid"
GROUP BY
    ds."City", ft."Stationid", dt."TruckType";

-- Optionally, add indexes or perform REFRESH MATERIALIZED VIEW to populate the data
-- CREATE INDEX idx_max_waste_stats ON max_waste_stats ("City", "Stationid", "TruckType");
-- REFRESH MATERIALIZED VIEW max_waste_stats;
```

Then run the following query:
```sql
SELECT * FROM public.max_waste_stats
```
Output:

<img width="681" alt="image" src="https://github.com/kwagle7/Data-Warehouse-Development-for-Waste-Management-using-PostgreSQL/assets/13037108/24cfd789-7bca-4298-ac7d-46839d615187">

# FAQs

# SQL Script Comparison
What is the difference between following?
![image](https://github.com/kwagle7/Data-Warehouse-Development-for-Waste-Management-using-PostgreSQL/assets/13037108/01758692-8834-4955-aaa2-bf4911cc560d)

Generally, there is no difference.
According to ChatGpt:

![image](https://github.com/kwagle7/Data-Warehouse-Development-for-Waste-Management-using-PostgreSQL/assets/13037108/ce1d6b8b-8a74-4c05-8afd-f8b2ddb114f0)



