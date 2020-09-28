# IMDB Case Study

[IMDb](https://en.wikipedia.org/wiki/IMDb) (also known as the Internet Movie Database) is an online database of information related to movies and videos.

The dataset are available in different format from different sources:
* https://www.imdb.com/interfaces/
* https://relational.fit.cvut.cz/dataset/IMDb
* http://www.webstepbook.com/supplements-2ed/databases/imdb.sql

The basic information we want to capture is as follows:
* Each movie has a title/name and a year.
* An actors/actress has a name and a gender.
* Actors/actresses play roles in movies.
* A director has a name and directs movies.
* A movie belongs to a number of genres.
* A director has genres.

Design a ER model to capture all the information. Note that an actor can play multiple roles in a movie and multiple actors can play the same role in the same movie.  

For each movie count the number of genres and output the movie names along with the counts.

<details>
<summary>answer</summary>
```sql
SELECT id, name, count(DISTINCT genre)
FROM movies, movies_genres
WHERE id = movie_id
GROUP BY id, name
ORDER BY count(DISTINCT genre) DESC
LIMIT 10;

+--------+----------------------+-----------------------+
| id     | name                 | count(DISTINCT genre) |
+--------+----------------------+-----------------------+
| 122529 | Finaru fuantaj VI    |                    11 |
| 113329 | Final Fantasy VII    |                    10 |
| 349066 | Utopia's Redemption  |                     9 |
| 350189 | Vampire Hunter D     |                     9 |
| 350246 | Vampires, Les        |                     8 |
|  69602 | Conker's Bad Fur Day |                     8 |
| 177327 | King Kong            |                     8 |
| 113331 | Final Fantasy VIII   |                     8 |
| 103180 | Escaflowne (2000/II) |                     8 |
| 212067 | Metoroporisu         |                     8 |
+--------+----------------------+-----------------------+
10 rows in set (7.38 sec)
```
</details>

Find all the genres a particular movie is in.

<details>
<summary>answer</summary>
```sql
SELECT name, genre
FROM movies, movies_genres
WHERE id = movie_id AND id = 122529;

+-------------------+-----------+
| name              | genre     |
+-------------------+-----------+
| Finaru fuantaj VI | Adventure |
| Finaru fuantaj VI | Animation |
| Finaru fuantaj VI | Comedy    |
| Finaru fuantaj VI | Drama     |
| Finaru fuantaj VI | Thriller  |
| Finaru fuantaj VI | Action    |
| Finaru fuantaj VI | Fantasy   |
| Finaru fuantaj VI | Horror    |
| Finaru fuantaj VI | War       |
| Finaru fuantaj VI | Mystery   |
| Finaru fuantaj VI | Sci-Fi    |
+-------------------+-----------+
11 rows in set (0.00 sec)

SELECT name, genre
FROM movies, movies_genres
WHERE id = movie_id AND id = 113329;

+-------------------+-----------+
| name              | genre     |
+-------------------+-----------+
| Final Fantasy VII | Action    |
| Final Fantasy VII | Mystery   |
| Final Fantasy VII | Adventure |
| Final Fantasy VII | Comedy    |
| Final Fantasy VII | Fantasy   |
| Final Fantasy VII | Drama     |
| Final Fantasy VII | Sci-Fi    |
| Final Fantasy VII | Horror    |
| Final Fantasy VII | Thriller  |
| Final Fantasy VII | Animation |
+-------------------+-----------+
10 rows in set (0.00 sec)

SELECT name, genre
FROM movies, movies_genres
WHERE id = movie_id AND id = 349066;

+---------------------+-----------+
| name                | genre     |
+---------------------+-----------+
| Utopia's Redemption | Action    |
| Utopia's Redemption | Family    |
| Utopia's Redemption | Short     |
| Utopia's Redemption | Adventure |
| Utopia's Redemption | Comedy    |
| Utopia's Redemption | Crime     |
| Utopia's Redemption | Drama     |
| Utopia's Redemption | Sci-Fi    |
| Utopia's Redemption | Thriller  |
+---------------------+-----------+
9 rows in set (0.00 sec)
```
</details>

Find actors who acted in the same movie under multiple (>1) roles. Show the movie name, actor name, and the number of roles.

<details>
<summary>answer</summary>
```sql
SELECT actor_id, first_name, last_name, movie_id, name, count(DISTINCT role)
FROM actors, roles, movies
WHERE actors.id = actor_id AND movies.id = movie_id
GROUP BY movie_id, actor_id
HAVING count(DISTINCT role) > 1
ORDER BY count(DISTINCT role) DESC
LIMIT 10;

+----------+------------+-----------+----------+------------------------------+----------------------+
| actor_id | first_name | last_name | movie_id | name                         | count(DISTINCT role) |
+----------+------------+-----------+----------+------------------------------+----------------------+
|    17921 | William H. | Artist    |    20042 | Arena smelykh                |                    8 |
|   426212 | David      | Schurmann |   399153 | "Nero Wolfe Mystery, A"      |                    6 |
|    47312 | Robert     | Bockstael |   399153 | "Nero Wolfe Mystery, A"      |                    6 |
|   475551 | James      | Tolkan    |   399153 | "Nero Wolfe Mystery, A"      |                    6 |
|   641104 | Nicky      | Guadagni  |   399153 | "Nero Wolfe Mystery, A"      |                    5 |
|   712980 | Kari       | Matchett  |   399153 | "Nero Wolfe Mystery, A"      |                    5 |
|   186795 | Richard    | Grupe     |   225804 | Na arene tsirka              |                    5 |
|   326721 | Pierre     | Mondy     |   385003 | "Cordier, juge et flic, Les" |                    4 |
|     6217 | John       | Alderton  |   411330 | "Wodehouse Playhouse"        |                    4 |
|   609183 | Shelley    | Duvall    |   388202 | "Faerie Tale Theatre"        |                    4 |
+----------+------------+-----------+----------+------------------------------+----------------------+
10 rows in set (1 min 15.54 sec)
```
</details>

Find the roles an actor played in a movie.

<details>
<summary>answer</summary>
```sql
SELECT first_name, last_name, name, role
FROM actors, roles, movies
WHERE actors.id = actor_id AND movies.id = movie_id
AND actor_id = 17921 AND movie_id = 20042;

+------------+-----------+---------------+-------------------------------+
| first_name | last_name | name          | role                          |
+------------+-----------+---------------+-------------------------------+
| William H. | Artist    | Arena smelykh | Themselves (cyclist-acrobats) |
| William H. | Artist    | Arena smelykh | Themselves (musical acrobats) |
| William H. | Artist    | Arena smelykh | Themselves (aerialists)       |
| William H. | Artist    | Arena smelykh | Themselves (acrobats)         |
| William H. | Artist    | Arena smelykh | Themselves (trapeze artists)  |
| William H. | Artist    | Arena smelykh | Themselves (jugglers)         |
| William H. | Artist    | Arena smelykh | Themselves (gymnasts)         |
| William H. | Artist    | Arena smelykh | Themselves (dancers-acrobats) |
| William H. | Artist    | Arena smelykh | Themselves (gymnasts)         |
| William H. | Artist    | Arena smelykh | Themselves (acrobats)         |
+------------+-----------+---------------+-------------------------------+
10 rows in set (0.06 sec)

SELECT first_name, last_name, name, role
FROM actors, roles, movies
WHERE actors.id = actor_id AND movies.id = movie_id
AND actor_id = 426212 AND movie_id = 399153;

+------------+-----------+-------------------------+------------------+
| first_name | last_name | name                    | role             |
+------------+-----------+-------------------------+------------------+
| David      | Schurmann | "Nero Wolfe Mystery, A" | Robert Robilotti |
| David      | Schurmann | "Nero Wolfe Mystery, A" | Alfred Kiernan   |
| David      | Schurmann | "Nero Wolfe Mystery, A" | Adrian Evers     |
| David      | Schurmann | "Nero Wolfe Mystery, A" | Miles Haydecker  |
| David      | Schurmann | "Nero Wolfe Mystery, A" | Mr. Thompson     |
| David      | Schurmann | "Nero Wolfe Mystery, A" | Jay Bruckner     |
+------------+-----------+-------------------------+------------------+
6 rows in set (0.03 sec)
```
</details>

Show all different "roles" in all movies in the database.

<details>
<summary>answer</summary>
```sql
SELECT DISTINCT role
FROM roles;

+----------------------------------------+
| role                                   |
+----------------------------------------+
| Stevie                                 |
| Various/lyricist                       |
| Gitano 1                               |
| El Cigala                              |
| Himself                                |
| Staff Humorist (1996)                  |
...
| Turkish singer                         |
| Andrea Melcher                         |
| Sanieba                                |
| Tanja Lohhoff                          |
| Emel (1996-1997)                       |
| Sanem                                  |
| Bayirgülü                              |
+----------------------------------------+
1153615 rows in set (1 min 10.58 sec)
```
</details>

Show movies with multiple actors/actresses who acted in the same role.

<details>
<summary>answer</summary>
```sql
SELECT movie_id, role, count(actor_id)
FROM roles
GROUP BY movie_id, role
ORDER BY count(actor_id) DESC
LIMIT 10;

+----------+---------+-----------------+
| movie_id | role    | count(actor_id) |
+----------+---------+-----------------+
|    20625 | Extra   |            1127 |
|   131232 | Extra   |             240 |
|   411420 | Himself |             222 |
|   394750 |         |             195 |
|   406321 |         |             189 |
|   144406 |         |             178 |
|   380430 |         |             171 |
|    56545 | Herself |             170 |
|   384590 | Himself |             168 |
|   240429 |         |             155 |
+----------+---------+-----------------+
10 rows in set (1 min 37.90 sec)
```
</details>
