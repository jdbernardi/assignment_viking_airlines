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


6. Get a list of all airports visited by user Dannie D'Amore after January 1, 2012. (Hint, see if you can get a list of all ticket IDs first). Note: Careful how you escape the quote in "D'Amore"... escaping in SQL is different from Ruby.

		User.find_by_sql("
				SELECT long_name
				FROM users JOIN airports
				ON (users.city_id=airports.city_id)
				JOIN itineraries ON (itineraries.user_id=users.id)
				WHERE itineraries.first_name LIKE 'Dannie' AND itineraries.last_name LIKE 'D_Amore'
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
			  ORDER BY price DESC
			  LIMIT 5
				")


2. Find the shortest flight that username "ryann_anderson" took.

	User.find_by_sql("
			SELECT FIRST(distance), first_name, last_name
			FROM users JOIN itineraries ON (users.id=itineraries.user_id)
			JOIN tickets ON (tickets.itinerary_id=itineraries.id)
			JOIN flights ON (flights.id=tickets.flight_id)
			WHERE users.username LIKE 'ryann_anderson'
			ORDER BY flights.distance
			")



3. Find the average flight distance for flights entering or leaving each city in Florida

	Airport.find_by_sql("

		SELECT AVG(flights.distance) AS average_distance
		FROM flights JOIN airports ON airports.state_id = flights.origin_id
		JOIN airports ON airports.state_id = flights.destination_id
		JOIN states ON states.id=airports.state_id
		WHERE flights.destination_id = 'Florida'


	")


4. Find the 3 users who spent the most money on flights in 2013

	Flight.find_by_sql("
		SELECT users.first_name, SUM(flights.price)
		FROM flights JOIN tickets ON (flights.id=tickets.flight_id)
		JOIN itineraries ON (itineraries.id=tickets.itinerary_id)
		JOIN users ON (users.id=itineraries.user_id)
		WHERE tickets.created_at BETWEEN '2015-1-1' AND '2015-12-31'
		GROUP BY users.first_name, flights.price
		ORDER BY flights.price
		LIMIT 3
	")


5. Count all flights to or from the city of Lake Vivienne that did not land in Florida

	Flight.find_by_sql("

		SELECT COUNT(*)
		FROM flights origin JOIN airports ON (origin.id=flights.origin_id)
		JOIN airports dest ON (dest.id=flights.destination_id)
		JOIN states from_state ON (from_state.id=origin.state_id)
		JOIN states to_state ON (to_state.id=dest.destination_id)
		JOIN cities from_city ON (from_city.id=dest.city_id)
		JOIN cities to_city ON (to_city.id=dest.city_id)

		WHERE to_state.name != 'Florida' AND to_city = 'Lake Vivienne' OR from_city = 'Lake Vivienne'

	")


6. Return the range of lengths of flights in the system(the maximum, and the minimum).

	Flight.find_by_sql("
		SELECT MAX(arrival_time-departure_time) AS longest, MIN(arrival_time-departure_time) AS shortest
		FROM flights
	")

Queries 3: Advanced

1. Find the most popular travel destination for users who live in Kansas.

	most popular will be the flight with the highest destination count that is the same
	users join itinerary
	join ticket with flight
	join state
	where state is kansas

	User.find_by_sql("
		SELECT COUNT(flights.destination_id) AS total, cities.name
		FROM users u_state JOIN states ON (users.state_id=states.id)
		JOIN itineraries ON (itineraries.user_id=users.id)
		JOIN tickets ON (itineraries.id=tickets.itinerary_id)
		JOIN flights ON (flights.id=tickets.flight_id)
		JOIN aiports ON (airports.id=flights.destination_id)
		JOIN states ON (states.id=airports.state_id)
		JOIN cities ON (cities.id=aiports.city_id)
		WHERE u_state.name = 'Kansas'
		GROUP BY cities.name
		ORDER BY total DESC
	")


2. How many flights have round trips possible? In other words, we want the count of all airports where there exists a flight FROM that airport and a later flight TO that airport.

	Flight.find_by_sql("

		SELECT(*)
		FROM flights a JOIN flights b ON a.origin_id=b.destination_id

	")


3. Find the cheapest flight that was taken by a user who only had one itinerary.
4. Find the average cost of a flight itinerary for users in each state in 2012.
5. Bonus: You're a tourist. It's May 6, 2013. Book the cheapest set of flights over the next six weeks that connect Oregon, Pennsylvania and Arkansas, but do not take any flights over 400 miles in distance. Note: This can be ~50 lines long but doesn't require any subqueries.



