JOIN Operation

1. The first example shows the goal scored by a player with the last name 'Bender'. The * says to list all the columns in the table - a shorter way of saying matchid, teamid, player, gtime

Modify it to show the matchid and player name for all goals scored by Germany. To identify German players, check for: teamid = 'GER'

SELECT goal.matchid, goal.player
  FROM goal JOIN eteam ON eteam.ID = goal.teamid
WHERE eteam.id= 'GER'

2.
From the previous query you can see that Lars Bender's scored a goal in game 1012. Now we want to know what teams were playing in that match.

Notice in the that the column matchid in the goal table corresponds to the id column in the game table. We can look up information about game 1012 by finding that row in the game table.

Show id, stadium, team1, team2 for just game 1012

SELECT id,stadium,team1,team2
  FROM game
WHERE game.id = 1012

3.
You can combine the two steps into a single query with a JOIN.

SELECT *
  FROM game JOIN goal ON (id=matchid)
The FROM clause says to merge data from the goal table with that from the game table. The ON says how to figure out which rows in game go with which rows in goal - the id from goal must match matchid from game. (If we wanted to be more clear/specific we could say
ON (game.id=goal.matchid)

The code below shows the player (from the goal) and stadium name (from the game table) for every goal scored.

Modify it to show the player, teamid, stadium and mdate for every German goal.

SELECT player,goal.teamid, stadium, game.mdate
  FROM game JOIN goal ON (id=matchid)
WHERE goal.teamid= 'GER'


4.
Use the same JOIN as in the previous question.

Show the team1, team2 and player for every goal scored by a player called Mario player LIKE 'Mario%'

SELECT team1, team2, goal.player
FROM goal JOIN game ON goal.matchid= game.id
WHERE player LIKE 'Mario%'

The table eteam gives details of every national team including the coach. You can JOIN goal to eteam using the phrase goal JOIN eteam on teamid=id

Show player, teamid, coach, gtime for all goals scored in the first 10 minutes gtime<=10

SELECT player, teamid, coach, gtime
  FROM goal JOIN eteam ON teamid=id
 WHERE gtime<=10


6.
To JOIN game with eteam you could use either
game JOIN eteam ON (team1=eteam.id) or game JOIN eteam ON (team2=eteam.id)

Notice that because id is a column name in both game and eteam you must specify eteam.id instead of just id

List the the dates of the matches and the name of the team in which 'Fernando Santos' was the team1 coach.

SELECT game.mdate, eteam.teamname
FROM game JOIN eteam ON game.team1 = eteam.id
WHERE eteam.coach LIKE 'Fernando Santos'

7.
List the player for every goal scored in a game where the stadium was 'National Stadium, Warsaw'

SELECT player
FROM game JOIN goal ON game.id = goal.matchid
WHERE game.stadium LIKE 'National Stadium, Warsaw'

8.
The example query shows all goals scored in the Germany-Greece quarterfinal.
Instead show the name of all players who scored a goal against Germany.

SELECT DISTINCT player
  FROM game JOIN goal ON matchid = id
    WHERE (team1='GER' OR team2='GER') AND goal.teamid != 'GER'

Show teamname and the total number of goals scored.
COUNT and GROUP BY
You should COUNT(*) in the SELECT line and GROUP BY teamname

SELECT teamname, COUNT(*)
FROM eteam JOIN goal ON id=teamid
GROUP BY teamname
ORDER BY teamname

10.
Show the stadium and the number of goals scored in each stadium.

SELECT stadium, COUNT(*)
FROM game JOIN goal ON id=matchid
GROUP BY stadium

11.
For every match involving 'POL', show the matchid, date and the number of goals scored.

SELECT matchid,mdate, count(*)
  FROM game JOIN goal ON matchid = id
 WHERE (team1 = 'POL' OR team2 = 'POL')
 GROUP BY matchid, mdate

12.
For every match where 'GER' scored, show matchid, match date and the number of goals scored by 'GER'

SELECT matchid, mdate, count(matchid)
FROM goal JOIN game on id = matchid
WHERE goal.teamid = 'GER'
GROUP BY matchid, mdate

13.

SELECT mdate,
  team1,
  SUM(CASE WHEN teamid=team1 THEN 1 ELSE 0 END) score1,
  team2,
  SUM(CASE WHEN teamid=team2 THEN 1 ELSE 0 END) score2
FROM game LEFT JOIN goal ON matchid = id
GROUP BY matchid, mdate, team1, team2
ORDER BY mdate, team1


More Joins
===========

3.
List all of the Star Trek movies, include the id, title and yr (all of these movies include the words Star Trek in the title). Order results by year.

SELECT id, title, yr
FROM movie
WHERE "title" LIKE '%Star Trek%'


Obtain the cast list for 'Casablanca'.

what is a cast list?
Use movieid=11768, (or whatever value you got from the previous question)

SELECT actor.name
FROM actor JOIN casting ON actor.id=casting.actorid
WHERE casting.movieid=11768

7.
Obtain the cast list for the film 'Alien'


SELECT actor.name
FROM actor JOIN casting ON actor.id=casting.actorid JOIN movie ON movie.id=casting.movieid
WHERE movie.title='Alien'

8.
List the films in which 'Harrison Ford' has appeared

SELECT movie.title
FROM movie JOIN casting ON movie.id=casting.movieid
WHERE casting.actorid=(SELECT id
FROM actor WHERE name LIKE 'Harrison Ford')

9.
List the films where 'Harrison Ford' has appeared - but not in the starring role. [Note: the ord field of casting gives the position of the actor. If ord=1 then this actor is in the starring role]

SELECT movie.title
FROM movie JOIN casting ON movie.id=casting.movieid
WHERE casting.actorid=(SELECT id
FROM actor WHERE name LIKE 'Harrison Ford')
AND casting.ord != 1


10.
List the films together with the leading star for all 1962 films.

SELECT movie.title, actor.name
FROM casting JOIN movie ON casting.movieid=movie.id JOIN actor ON casting.actorid=actor.id
WHERE movie.yr=1962 AND casting.ord=1


12.
List the film title and the leading actor for all of the films 'Julie Andrews' played in.

SELECT title, actor.name
FROM movie JOIN casting on movie.id=casting.movieid JOIN actor ON actor.id=casting.actorid
WHERE movie.id IN (SELECT movie.id FROM casting JOIN movie ON casting.movieid=movie.id JOIN actor ON casting.actorid = actor.id WHERE actor.name='Julie Andrews') AND ord=1

13.
Obtain a list, in alphabetical order, of actors who've had at least 30 starring roles.

SELECT actor.name
FROM actor JOIN casting ON actor.id=casting.actorid
WHERE ord=1
GROUP BY ord, actor.name
HAVING COUNT(actor.id)>=30


14.
List the films released in the year 1978 ordered by the number of actors in the cast, then by title.

SELECT movie.title, COUNT(actorid)
FROM movie JOIN casting ON movie.id=casting.movieid
WHERE movie.yr=1978
GROUP BY movie.title
ORDER BY COUNT(casting.actorid) DESC, movie.title




15.
List all the people who have worked with 'Art Garfunkel'.

SELECT actor.name
FROM actor JOIN casting ON actor.id=casting.actorid
WHERE casting.movieid IN (SELECT casting.movieid FROM casting WHERE casting.actorid=(SELECT id FROM actor WHERE name='Art Garfunkel')) AND actor.name != 'Art Garfunkel'


Aggregate Functions
=========
1.
SELECT SUM(population)
FROM world

2.
List all the continents - just once each.

SELECT DISTINCT continent
FROM world

3.
Give the total GDP of Africa

SELECT SUM(gdp)
FROM world
WHERE continent = 'Africa'

4.
How many countries have an area of at least 1000000

SELECT COUNT(*)
FROM world
WHERE area >= 1000000

5.
What is the total population of ('Estonia', 'Latvia', 'Lithuania')

SELECT SUM(population)
FROM world
WHERE name IN ('Estonia', 'Latvia', 'Lithuania')

6.
For each continent show the continent and number of countries.

SELECT continent, COUNT(*)
FROM world
GROUP BY continent

7.
For each continent show the continent and number of countries with populations of at least 10 million.

SELECT continent, COUNT(*)
FROM world
WHERE population >= 10000000
GROUP BY continent

8.
List the continents that have a total population of at least 100 million.

SELECT continent
FROM world
GROUP BY continent
HAVING SUM(population) > 100000000

SELECT WITH SELECT
================

1.
List each country name where the population is larger than that of 'Russia'.

SELECT DISTINCT name FROM world
  WHERE population >(
SELECT population FROM world
      WHERE name='Russia')

2.
Show the countries in Europe with a per capita GDP greater than 'United Kingdom'.

/*finds gdp per capita of UK*/
(SELECT (gdp/population)
FROM world
WHERE name = 'United Kingdom')

/*actual query*/
SELECT name
FROM world
WHERE gdp/population > (
  /* above subquery */
) AND continent = 'Europe'

3.
List the name and continent of countries in the continents containing either Argentina or Australia. Order by name of the country.

/*find the arg and aus cont.*/
SELECT continent
FROM world
WHERE name IN ('Argentina', 'Australia')

/*query*/
SELECT name, continent
FROM world
WHERE continent IN (
   /*above subquery*/
)
ORDER BY name
