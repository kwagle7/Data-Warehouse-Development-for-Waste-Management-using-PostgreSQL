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

# Solution

First Run the Create_Query.sql, you will get following ERD afterwards

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

<img width="481" alt="image" src="https://github.com/kwagle7/Data-Warehouse-Development-for-Waste-Management-using-PostgreSQL/assets/13037108/eb84554d-d8c7-48a3-9e49-83180b61dcdd">
