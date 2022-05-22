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
	7. In BigQuery, create table for each .csv file within db_challange dataset, upload      data from GC Storage.
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
    (SELECT y.artist, y.price, y.title, x.avg_price 
    FROM `dbproject-351005.db_challenge.albums_table`
    AS y INNER JOIN x ON y.artist = x.artist WHERE y.price > x.avg_price)
	
  Results
  
    Giant Steps	     
    Kind of Blue	     
    Ah Um	
<img width="405" alt="image" src="https://user-images.githubusercontent.com/68199057/169682391-a1a5d366-2bbe-455a-b1b1-442e07070605.png">

    https://console.cloud.google.com/bigquery?sq=624949028035:3af397c98a484d70b808193e39073e55
--------------------------------------------------------------------------------------

Challenge 3 - Construct a SQL query that returns the kinds of instruments and number of likes each kind got, in total, for all of the posts.

	SELECT count(instrument_kind) AS inst_total_likes, instrument_kind
	FROM `dbproject-351005.db_challenge.likes_table` GROUP BY instrument_kind
	  
  Results
  
    Piano:     4 
    Trumpet:   2 
    Guitar:    4
    Voice:     3
    Saxophone: 4
<img width="274" alt="image" src="https://user-images.githubusercontent.com/68199057/169682452-3359f7b4-237f-4e11-8c00-d1267582cce2.png">

    https://console.cloud.google.com/bigquery?sq=624949028035:a3ebdb5e267d43e8a0e191676259f6cf
--------------------------------------------------------------------------------------

Challenge 4 - Construct a SQL query that returns specifics of instruments and post id for each of the likes.
   
    SELECT specifics, post_id
    FROM `dbproject-351005.db_challenge.likes_table` 
    INNER JOIN `dbproject-351005.db_challenge.instruments_table`  
    ON instrument_kind = kind
	
  Results
          
   
    Martin_Committee_Trumpet 20
    Martin_Committee_Trumpet 19
    Gibson_L5_CES	20
    Gibson_L5_CES 19
    Gibson_L5_CES	32
    Gibson_L5_CES	44
    Soprano_through_Baritone 20
    Soprano_through_Baritone 32
    Soprano_through_Baritone 44
    Alto_Tenor_Saxophone 32
    Alto_Tenor_Saxophone 44
    Alto_Tenor_Saxophone 19
    Alto_Tenor_Saxophone 57
	
<img width="283" alt="image" src="https://user-images.githubusercontent.com/68199057/169682648-78a5e907-cefb-4514-b15c-f28cbf1c8231.png">

    https://console.cloud.google.com/bigquery?sq=624949028035:ce4fc80978684b108d7a90ee23e15967

Challenge 4.Fix - Construct a SQL query that returns specifics of instruments and post id for each of the likes.
	* Replaced 'Keyboard' with 'Piano' in Kind field.
		
    SELECT specifics, post_id
    FROM `dbproject-351005.db_challenge.likes_table` 
    INNER JOIN `dbproject-351005.db_challenge.instruments_Fix`  
    ON instrument_kind = kind
	
  Results
  
    Soprano_through_Baritone 20
    Soprano_through_Baritone 32
    Soprano_through_Baritone 44
    Gibson_L5_CES 20
    Gibson_L5_CES	19
    Gibson_L5_CES 32
    Gibson_L5_CES 44
    Piano 20
    Piano 44
    Piano 19
    Piano 57
    Martin_Committee_Trumpet 20
    Martin_Committee_Trumpet 19
    Alto_Tenor_Saxophone 32
    Alto_Tenor_Saxophone 44
    Alto_Tenor_Saxophone 19
    Alto_Tenor_Saxophone 57

<img width="280" alt="image" src="https://user-images.githubusercontent.com/68199057/169682737-007e96ec-8b05-473e-9990-8a00c7104b17.png">

    https://console.cloud.google.com/bigquery?sq=624949028035:e9140454faa04b1b9f01b7832962a510

--------------------------------------------------------------------------------------

Challenge 5 - Construct a SQL query that returns the average number of likes per post for each instrument specifics.

	  WITH y AS (WITH x AS (SELECT COUNT(post_id) AS count_post_id, post_id, 
	  FROM `dbproject-351005.db_challenge.likes_table` GROUP BY post_id) 
	  SELECT id, instrument_kind, x.count_post_id 
	  FROM `dbproject-351005.db_challenge.posts_table` INNER JOIN x ON id = post_id) 
	  SELECT instrument_kind, SUM(count_post_id) / COUNT(instrument_kind) AS Average 
	  FROM y GROUP BY instrument_kind

  Results
  
    Saxophone: 3.0
    Trumpet:   3.5
    Voice:     4.0
<img width="234" alt="image" src="https://user-images.githubusercontent.com/68199057/169682873-beb7672c-aa77-4e8c-b788-c4f565fb33e2.png">

    https://console.cloud.google.com/bigquery?sq=624949028035:aee44dd9c2744146b927b1a5bf0e3ca9
