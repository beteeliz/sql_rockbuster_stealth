```
SELECT AVG(total_amount_paid) AS average
FROM
(SELECT customer.customer_id,
customer.first_name,
customer.last_name,
country.country,
city.city,
SUM(payment.amount) AS total_amount_paid
FROM payment
JOIN customer ON payment.customer_id = customer.customer_id
JOIN address ON customer.address_id = address.address_id
JOIN city ON address.city_id = city.city_id
JOIN country ON city.country_id = country.country_id
WHERE city.city IN (SELECT city.city
FROM customer 
JOIN address ON customer.address_id = address.address_id
JOIN city ON address.city_id = city.city_id
JOIN country ON city.country_id = country.country_id
WHERE country.country IN (SELECT country.country
FROM customer
JOIN address ON customer.address_id = address.address_id
JOIN city ON address.city_id = city.city_id
JOIN country ON city.country_id = country.country_id
GROUP BY country.country
ORDER BY COUNT(customer.customer_id) DESC
LIMIT 10)
GROUP BY country.country,
city.city
ORDER BY COUNT(customer.customer_id) DESC
LIMIT 10)
GROUP BY customer.customer_id,
customer.first_name,
customer.last_name,
country.country,
city.city
ORDER BY SUM(payment.amount) DESC
LIMIT 5) AS total_amount_paid
 

```
