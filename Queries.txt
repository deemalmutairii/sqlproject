/*Query 1-query used for first insight*/

SELECT  film.title AS Moive_NAME,category.name AS Category_type,Count(rental.rental_id) AS rental_count
FROM film
JOIN film_category
ON film_category.film_id=film.film_id
JOIN category
ON category.category_id=film_category.category_id
JOIN inventory
ON inventory.film_id=film.film_id
JOIN rental
ON rental.inventory_id=inventory.inventory_id
WHERE name LIKE 'Animation' OR name LIKE 'Children' OR name LIKE 'Classics' OR name LIKE 'Comedy' OR name LIKE ' Family ' OR name LIKE 'Music' 
GROUP BY film.title ,category.name
ORDER BY category.name 


/* Query 2-query used for second insight*/

WITH q1 AS (SELECT *,CONCAT(first_name,last_name) AS Full_NAME
FROM customer)  
SELECT  Full_Name,phone,address,city,country,SUM(amount)AS Amount_purchase_in_currency
FROM q1
JOIN address
USING(address_id)
JOIN city
USING (city_id)
JOIN payment
USING(customer_id)
JOIN country
USING(country_id)
WHERE Full_Name LIKE'%a'
GROUP BY Full_Name,phone,address,city,country
ORDER BY Amount_purchase_in_currency DESC
Limit 10;


/* Query 3-query used for third insight*/

SELECT title,rating,MAX(rental_duration) AS max_rental_duration
FROM film F
JOIN film_actor FA
USING (film_id)
WHERE fa.actor_id in
( SELECT a.actor_id
 FROM actor a
 where a.first_name='Nick')   
 GROUP BY F.title,F.description,F.rating  
 
 
 /* Query 4-query used for forth insight*/
 
 SELECT F.title Movie_NAME, C.name category_type, SUM(F.rental_duration)AS rental_duration,
       NTILE(3) OVER (ORDER BY F.rental_duration) quartile
FROM category C
JOIN film_category Fc
USING (category_id)
JOIN film F
USING (film_id)
WHERE name LIKE 'Animation' OR name LIKE 'Children' OR name LIKE 'Classics' OR name LIKE 'Comedy' OR name LIKE ' Family ' OR name LIKE 'Music' 
GROUP BY F.title,C.name,F.rental_duration