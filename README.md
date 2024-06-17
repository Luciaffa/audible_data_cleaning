## Introduction
In this project, I explore techniques for cleaning and transforming data using SQL. Data cleaning is a critical step in the data preparation process, ensuring that the data we work with is accurate, consistent, and ready for analysis.

## Background
The data source for this project include an Audible dataset obtained from Kaggle. This dataset contains information about audiobooks available on Audible.in, providing details such as book titles, authors, genres, ratings, and more. By applying data cleaning techniques, I aim to transform raw, unstructured data into a clean and usable format, ready for further analysis or integration into other systems.

## Tools I Used

For this data cleaning project focused on Audible.in data, I utilized the following key tools:

- **SQL:** The backbone of my data cleaning process, enabling me to manipulate and clean the data directly within the database.
- **Microsoft SQL Server Management Studio (SSMS):** My primary environment for managing the database, executing SQL queries, and performing data cleaning tasks.
- **GitHub:** Essential for version control, sharing my SQL scripts used for data cleaning, and facilitating collaboration on the project.

## About the Dataset

**Schema**

| Column Name | Data Type |
|-------------|------------|
| name        | varchar    |
| author      | varchar    |
| narrator    | varchar    |
| time        | varchar    |
| releasedate | varchar    |
| language    | varchar    |
| stars       | varchar    |
| price       | float      |
<!-- End of table -->
**Dataset samples**

*Before*

![datascreenshot](https://live.staticflickr.com/65535/53793789379_f174372d81_b.jpg)

*After*

![databaseafter](https://live.staticflickr.com/65535/53796302862_0b58bb7996_b.jpg)




## What are the data quality improvements needed?

Based on a preliminary analysis there are some key problems and possible issues I must address, some of which can be seen in the data sample above.

1. Check for duplicate and null/blank values.
2. Remove the unnecessary "Writtenby:" from the 'author' column.
3. Remove the unncessary ¨Narratedby:¨ from the 'narrator' column.
4. Separate the author's name and last name from the 'author' and 'narrator' columns.
5. Change the releasedate column from a VARCHAR(MAX) data type to a DATE data type.
6. Split the 'stars' values into two columns, "stars" and "number of ratings¨. Assign 'NULL' to 'Not yet rated' values.
7. Address the inconsistencies in the 'language' column, as languages other than English are not capitalized.


### Check for duplicate and null/blank values.
1. Find duplicate values
```sql
SELECT name, author, narrator, time, releasedate, language, stars, price, COUNT(*)
FROM audible_uncleaned
GROUP BY name, author, narrator, time, releasedate, language, stars, price
HAVING COUNT(*) > 1;
 ```
*Output: No duplicate values found*

2. Find NULL or blank values
```sql
SELECT *
FROM audible_uncleaned
WHERE name IS NULL OR name = ''
   OR author IS NULL OR author = ''
   OR narrator IS NULL OR narrator = ''
   OR time IS NULL OR time = ''
   OR releasedate IS NULL OR releasedate = ''
   OR language IS NULL OR language = ''
   OR stars IS NULL OR stars = ''
   OR price IS NULL OR price = '';
 ```
*Output: Query returned 338 rows that had a NULL value in the 'price' column, I will not be deleting these rows since the NULL values can be handled during the analysis process.*

### Remove the unnecessary "Writtenby:" from the 'Author' column.
```sql
UPDATE audible_uncleaned
SET author = SUBSTRING(author, 11, LEN(author) - 10)
```
### Remove the unncessary ¨Narratedby:¨ from the 'Narrator' column.
```sql
UPDATE audible_uncleaned
SET narrator = SUBSTRING(narrator, 12, LEN(narrator) - 11)
```
### Separate the author's and the narrator's name and last name from the 'author' and 'narrator' column.
   
I used these queries to separate the first name and last name from the author and narrator columns, by introducing a space before each capital letter (not including the first letter of each name). In order to update the database, this step requieres the creation of a temporary table in order to populate it with a CTE that introduces the spaces before the capital letter and then drop that table.

**Author column:**
```sql
CREATE TABLE #TempFormattedAuthors (
    author VARCHAR(MAX),
    formatted_author VARCHAR(MAX)
);

;WITH RecursiveCTE AS (
    SELECT 
        author,
        SUBSTRING(author, 1, 1) AS author_first_letter,
        2 AS position
    FROM 
        audible_uncleaned
    UNION ALL
    SELECT 
        author,
        author_first_letter + 
            CASE 
                WHEN ASCII(SUBSTRING(author, position, 1)) BETWEEN 65 AND 90 THEN ' ' + SUBSTRING(author, position, 1)
                ELSE SUBSTRING(author, position, 1)
            END,
        position + 1
    FROM 
        RecursiveCTE
    WHERE 
        position <= LEN(author)
)
INSERT INTO #TempFormattedAuthors (author, formatted_author)
SELECT 
    author,
    author_first_letter
FROM 
    RecursiveCTE
WHERE 
    position > LEN(author);

UPDATE audible_uncleaned
SET 
    author = tfa.formatted_author
FROM 
    audible_uncleaned au
JOIN 
    #TempFormattedAuthors tfa ON au.author = tfa.author;

DROP TABLE #TempFormattedAuthors;
```

**Narrator column:**
```sql
CREATE TABLE #TempFormattedNarrators (
    narrator VARCHAR(MAX),
    formatted_narrator VARCHAR(MAX)
	);
;WITH RecursiveCTE AS (
    SELECT 
        narrator,
        SUBSTRING(narrator, 1, 1) AS narrator_first_letter,
        2 AS position
    FROM 
        audible_uncleaned
    UNION ALL
    SELECT 
        narrator,
        narrator_first_letter + 
            CASE 
                WHEN ASCII(SUBSTRING(narrator, position, 1)) BETWEEN 65 AND 90 THEN ' ' + SUBSTRING(narrator, position, 1)
                ELSE SUBSTRING(narrator, position, 1)
            END,
        position + 1
    FROM 
        RecursiveCTE
    WHERE 
        position <= LEN(narrator)
)

INSERT INTO #TempFormattedNarrators (narrator, formatted_narrator)
SELECT 
    narrator,
    narrator_first_letter
FROM 
    RecursiveCTE
WHERE 
    position > LEN(narrator)
	OPTION (MAXRECURSION 200);

UPDATE audible_uncleaned
SET 
    narrator = tfn.formatted_narrator
FROM 
    audible_uncleaned au
JOIN 
    #TempFormattedNarrators tfn ON au.narrator = tfn.narrator;

DROP TABLE #TempFormattedNarrators;
```
### Change the releasedate column from a VARCHAR(MAX) data type to a DATE data type. This modification allows for advanced analyses, such as time series analysis, date arithmatic and date-part analysis.
```sql
UPDATE audible_uncleaned
SET releasedate_new = CONVERT(DATE, releasedate, 3);

ALTER TABLE audible_uncleaned DROP COLUMN releasedate;

EXEC sp_rename 'audible_uncleaned.releasedate_new', 'releasedate', 'COLUMN';
```

### Split the 'stars' values into two columns, "stars" and "Number of Ratings.

I fixed the stars column by separating the star rating and the number of ratings into their own columns, in order to improve data clarity and facilitate easier analysis and data access. I dropped the string values and assigned NULL to the 'Not yet rated' values in order to enable easier computational queries on the new columns.
```sql
ALTER TABLE audible_uncleaned
ADD star_rating FLOAT, number_of_ratings INT;

UPDATE audible_uncleaned
SET star_rating = 
    CASE
        WHEN CHARINDEX('out of 5 stars', stars) > 0 
        THEN SUBSTRING(stars, 1, CHARINDEX('out of 5 stars', stars) - 1) 
        ELSE NULL
    END, 
    number_of_ratings = 
    CASE
        WHEN CHARINDEX('rating', stars) > 0 
THEN (SUBSTRING(stars, CHARINDEX('stars', stars) + 5, CHARINDEX('rating', stars) - CHARINDEX('stars', stars) - 5)
ELSE NULL
    END

ALTER TABLE audible_uncleaned DROP COLUMN stars;
```
### Address the inconsistencies in the 'language' column, as languages other than English are not capitalized. 
I chose to use a query that would capitalize the first letter of the whole column to inconsistencies, ensuring all language names are correctly formatted.
```sql
UPDATE audible_uncleaned
SET language = CONCAT(
    UPPER(LEFT(language, 1)),
    LOWER(SUBSTRING(language, 2, LEN(language) - 1))
);
```
