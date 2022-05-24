# gcp_solution
database challange solution

Challenge 1 - Create the code and documentation for using CSV files under data directory to create four tables in a SQL database: one table for albums, another table for instruments, another table for likes and another table for posts.

	0. Put on Blue Train by Coltrane.
	1. Fork repository.
	2. Download data.
	3. Create free trial BigQuery account.
	4. Set up a new project called dbproject-351005.
	5. Create dataset called: db_challenge.
	6. Create bucket in GCP Storage called db_buc.
		 - Upload .csv files to db_buc bucket in GC Storage.
	7. In BigQuery, create table for each .csv file within db_challange dataset, upload data from GC Storage.
		 - autodetect schema
		 * Instruments.csv autodetect schema does not work
		  	- redo with manual + add field: Kind & Specifics
	8. Create and save queries for each challenge.
	9. Run queries & save results.

	x. Manually fix data on Instruments.csv data. Replace 'Keyboard' with 'Piano'. (Challenge 4.Fix)


--------------------------------------------------------------------------------------

Challenge 2 - Construct a SQL query that returns the titles of albums that can are priced greater than the average price of the albums of the particular artist.

    WITH x AS (SELECT AVG(price) AS avg_price, artist 
    FROM `dbproject-351005.db_challenge.albums_table`
    GROUP BY artist)
    (SELECT y.title, y.price
    FROM `dbproject-351005.db_challenge.albums_table`
    AS y INNER JOIN x ON y.artist = x.artist WHERE y.price > x.avg_price)
	
  Results	
<img width="223" alt="image" src="https://user-images.githubusercontent.com/68199057/170037040-1fd459fb-6fca-46b7-b6ab-e3b3a2d4c5cc.png">

    https://console.cloud.google.com/bigquery?sq=624949028035:3af397c98a484d70b808193e39073e55
--------------------------------------------------------------------------------------

Challenge 3 - Construct a SQL query that returns the kinds of instruments and number of likes each kind got, in total, for all of the posts.

	SELECT count(instrument_kind) AS inst_total_likes, instrument_kind
	FROM `dbproject-351005.db_challenge.likes_table` GROUP BY instrument_kind
	  
  Results
<img width="274" alt="image" src="https://user-images.githubusercontent.com/68199057/170037549-5e474a77-01c1-450c-a301-84b1d48cab29.png">

    https://console.cloud.google.com/bigquery?sq=624949028035:a3ebdb5e267d43e8a0e191676259f6cf
--------------------------------------------------------------------------------------

Challenge 4 - Construct a SQL query that returns specifics of instruments and post id for each of the likes.
   
    SELECT specifics, post_id
    FROM `dbproject-351005.db_challenge.likes_table` 
    INNER JOIN `dbproject-351005.db_challenge.instruments_table`  
    ON instrument_kind = kind
	
  Results	
<img width="283" alt="image" src="https://user-images.githubusercontent.com/68199057/170037487-d955f1b5-1da7-41e8-ad65-0be4a585a368.png">

    https://console.cloud.google.com/bigquery?sq=624949028035:ce4fc80978684b108d7a90ee23e15967

Challenge 4.Fix - Construct a SQL query that returns specifics of instruments and post id for each of the likes.
	* Replaced 'Keyboard' with 'Piano' in Kind field.
		
    SELECT specifics, post_id
    FROM `dbproject-351005.db_challenge.likes_table` 
    INNER JOIN `dbproject-351005.db_challenge.instruments_Fix`  
    ON instrument_kind = kind
	
  Results
<img width="280" alt="image" src="https://user-images.githubusercontent.com/68199057/170037361-f2376a1a-3856-4f01-941e-f5ff8d3bad86.png">

    https://console.cloud.google.com/bigquery?sq=624949028035:e9140454faa04b1b9f01b7832962a510

--------------------------------------------------------------------------------------

Challenge 5 - Construct a SQL query that returns the average number of likes per post for each instrument specifics.

	  WITH z AS (
  		WITH y AS (
    			WITH x AS (
      				SELECT COUNT(post_id) AS count_post_id, post_id, 
      				FROM `dbproject-351005.db_challenge.likes_table` 
      				GROUP BY post_id) 
  		SELECT id, instrument_kind, x.count_post_id
 		FROM `dbproject-351005.db_challenge.posts_table` 
  		INNER JOIN x ON id = post_id) 
	SELECT instrument_kind, SUM(count_post_id) / COUNT(instrument_kind) AS average 
	FROM y GROUP BY instrument_kind)
	SELECT specifics, z.average
	FROM `dbproject-351005.db_challenge.instruments_table`
	INNER JOIN z ON kind = instrument_kind

  Results
<img width="349" alt="image" src="https://user-images.githubusercontent.com/68199057/170037260-58df8c9c-b35c-4d0f-a808-47a050836a56.png">

    https://console.cloud.google.com/bigquery?sq=624949028035:aee44dd9c2744146b927b1a5bf0e3ca9
