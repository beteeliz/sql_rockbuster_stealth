```
With top_10_countries AS
	(SELECT country.country
	FROM customer
	INNER JOIN address ON customer.address_id = address.address_id
	INNER JOIN city On address.city_id = city.city_id
		INNER JOIN country ON city.country_id = country.country_id
	GROUP BY country.country
	ORDER BY COUNT(customer.customer_id) DESC
	LIMIT 10),
top_cities AS
	(SELECT city.city
	FROM customer
	INNER JOIN address ON customer.address_id = address.address_id
	INNER JOIN city ON address.city_id = city.city_id
	INNER JOIN country ON city.country_id = country.country_id
	WHERE country.country IN
	(SELECT *
	FROM top_10_countries)
	GROUP BY country.country, city.city
	ORDER BY COUNT(customer.customer_id) DESC
	LIMIT 10),
top_customers AS
	(SELECT SUM(payment.amount) AS total_payment, country.country
	FROM customer
	INNER JOIN address ON customer.customer_id = address.address_id
	INNER JOIN city ON address.city_id = city.city_id
	INNER JOIN country ON city.country_id = country.country_id
	INNER JOIN payment ON customer.customer_id = payment.customer_id
	WHERE city.city IN ( SELECT * FROM top_cities)
	GROUP BY customer.first_name, customer.last_name, city.city, country.country
	ORDER BY total_payment DESC
	LIMIT 5)
SELECT country.country,
	COUNT(DISTINCT customer.customer_id) AS All_customer_count,
	COUNT(DISTINCT top_customers) AS top_customer_count
	FROM customer
	INNER JOIN address ON customer.address_id = address.address_id
	INNER JOIN city ON address.city_id = city.city_id
	INNER JOIN country ON city.country_id = country.country_id
	LEFT JOIN top_customers ON country.country = top_customers.country
	GROUP BY country.country
	ORDER BY top_customer_count DESC
	LIMIT 10
```
