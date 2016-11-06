# assignmnent_viking_analytics

[Joe Bernardi](https://github.com/jdbernardi/assignment_viking_airlines)

1. Get a list of all users in California...

		User.find_by_sql("SELECT *
											FROM users JOIN states ON
											(users.state_id=states.id)
 											WHERE states.name LIKE 'California'")

2. Get a list of all airports in MN...

		Airport.find_by_sql("SELECT state_id, long_name
												 FROM airports JOIN states ON (airports.state_id=states.id)
												 WHERE states.name LIKE 'Minnesota'
		")

3. Get a list of all payment methods used on itineraries by the user with email address "heidenreich_kara@kunde.net"

		User.find_by_sql("
									 	SELECT payment_method, username
									 	FROM users JOIN itineraries
									 	ON users.id=itineraries.user_id
									 	WHERE email LIKE 'heidenreich%'
		")


4. Get a list of prices of all flights whose origins are in Kochfurt Probably International Airport.


		City.find_by_sql("
									 	SELECT price
									 	FROM cities JOIN flights
									 	ON cities.id=flights.origin_id
									 	JOIN airports ON airports.city_id=flights.origin_id
									 	WHERE airport LIKE 'Kochfurt%'
		")


5. Find a list of all Airport names and codes which connect to the airport coded LYT.


		Airport.find_by_sql("
				SELECT long_name, code
				FROM airports JOIN flights
				ON (airports.city_id=origin_id)
				WHERE code = 'LYT'
				")


**6. Get a list of all airports visited by user Dannie D'Amore after January 1, 2012. (Hint, see if you can get a list of all ticket IDs first). Note: Careful how you escape the quote in "D'Amore"... escaping in SQL is different from Ruby.

		User.find_by_sql("
				SELECT long_name
				FROM users JOIN airports
				ON (users.city_id=airports.city_id)
				JOIN itineraries ON (itineraries.user_id=users.id)
				WHERE itineraries.user_id LIKE 'Dannie D_Amore'
				")


Queries 2: Adding in Aggregation

1. Find the top 5 most expensive flights that end in California.

		airport.id = cities.id
		flights.destination_id = cities.id

		Airport.find_by_sql("
				SELECT price
				FROM airports JOIN cities
				ON (airports.city_id=cities.id)
				JOIN flights ON (flights.destination_id=cities.id)
			  WHERE destination_id = 3
			  ORDER BY price
			  LIMIT 5
				")


2. Find the shortest flight that username "ryann_anderson" took.
3. Find the average flight distance for flights entering or leaving each city in Florida
4. Find the 3 users who spent the most money on flights in 2013
5. Count all flights to or from the city of Lake Vivienne that did not land in Florida
6. Return the range of lengths of flights in the system(the maximum, and the minimum).
