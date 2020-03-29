---
layout: post
title: Working with different timezone in golang and PostgreSQL
---
If your application runs in different timezone and there are database entries from different timezone then we need to very careful while storing and fetching the data in database.

We should flow following strategies to keep data consistent and easy retrieval of data.

1. Always store data in single timezone I will suggest keep it in UTC timezone. if you are using PostgreSQL then set default database time zone.
2. Before storing data in database convert it to database time zone as mentioned above if your database timezone is UTC then convert the time in UTC timezone and store it.
3. Before fetching data for different timezone convert the query time to database timezone so that we get correct result set.
4. After fetching the result set convert the result set to querying timezone.  

Below are examples of how to convert the time in different timezone, how to store it and how to fetch it.    

To understand data insert and select let say we have table called flights to store flights details with fields, flight_number, departure_time, arrival_time, status, departure_country, arrival_country.

so in flights table there are two time fields departure_time and arrival_time corresponding to departure and arrival country which has different timezone.

Code snippet for above mentioned process 

// Create postgre sql connection url

```go
	psqlInfo := fmt.Sprintf("host=%s port=%d user=%s "+
		"password=%s dbname=%s sslmode=disable TimeZone=UTC",
		HOST, PORT, USER, PASSWORD, DATABASE)

	//connect to database
	db, err := sql.Open("postgres", psqlInfo)
	if err != nil {
		panic(err)
	}

	defer db.Close()
```

// Store different timezone records

```go
// Insert records of different timezone
	// Take example a flight is from Bangalore India to New York
	// Depart time  2020-03-02T15:04:05+05:00 (India Standard Time)
	// Arrival time 2020-03-03T18:04:05-04:00 (Eastern Time Zone)

	//parse arrival time
	arrivalTime, err := time.Parse(time.RFC3339, "2020-03-02T15:04:05+05:00")
	if err != nil {
		log.Fatal("Error parsing arrival time", err)
	}

	//parse departure time
	departureTime, err := time.Parse(time.RFC3339, "2020-03-03T18:04:05-04:00")
	if err!=nil{
		log.Fatal("Error parsing departure time", err)
	}


	//print utc time
	fmt.Println("Arrival time UTC:",arrivalTime.UTC(), " Departure time UTC:", departureTime.UTC())

	//store records
	result, err := db.Exec("INSERT INTO flight_details VALUES (2, 'Active', 'IN', 'USA', $1, $2)", arrivalTime.UTC(), departureTime.UTC())
	if err != nil {
		log.Fatal(err)
	}

	rowsAffected, err := result.RowsAffected()
	fmt.Println("Number of rows affected:", rowsAffected)
```
// Select records from different timezone 

```go
 //let say we want to get all the flight departing from India on 2020-03-02
	loc, err := time.LoadLocation("Asia/Kolkata")
	if err != nil {
		log.Fatal("unable to get time zone location")
	}

	//start time
	startDepartureTime := time.Date(2020,3,2,0,0,0,0, loc).UTC()
	endDepartureTime := time.Date(2020,3,2,23,59,59,0, loc).UTC()

	//select all rows
	rows, err = db.Query("SELECT status,flight_number,departure_time,arrival_time, arrival_country, departure_country FROM flight_details WHERE departure_time BETWEEN $1 AND $2 AND departure_country=$3;",startDepartureTime,endDepartureTime,"IN")
	if err != nil {
		log.Fatal(err)
	}
	defer rows.Close()

	log.Println("Selected rows for IN departure:")
	displayRecords(rows)

	//let say we want to get all the flight arriving at USA on 2020-03-03
	//start time
	loc, err = time.LoadLocation("America/New_York")
	if err != nil {
		log.Fatal("unable to get time zone location")
	}
	startTime := time.Date(2020,3,3,0,0,0,0, loc).UTC()
	endTime := time.Date(2020,3,3,23,59,59,0, loc).UTC()

	//select all rows
	rows, err = db.Query("SELECT status,flight_number,departure_time,arrival_time, arrival_country, departure_country FROM flight_details WHERE arrival_time BETWEEN $1 AND $2 AND arrival_country=$3;",startTime,endTime,"USA")
	if err != nil {
		log.Fatal(err)
	}
	defer rows.Close()

	log.Println("Selected rows for USA arrival:")
	displayRecords(rows)
```
<script src="https://gist.github.com/gyaan/93a00e88293d7af77f194eeb03dda7e6.js"></script>
