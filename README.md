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

long_name, code

WHERE code LIKE 'LYT'




6. Get a list of all airports visited by user Dannie D'Amore after January 1, 2012. (Hint, see if you can get a list of all ticket IDs first). Note: Careful how you escape the quote in "D'Amore"... escaping in SQL is different from Ruby.