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

//post need to complete
