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




4.
Which country has a population that is more than Canada but less than Poland? Show the name and the population.

SELECT name, population
FROM world
WHERE population > (SELECT population FROM world WHERE name='Canada') AND population < (SELECT population FROM world WHERE name='Poland')


5.
Germany (population 80 million) has the largest population of the countries in Europe. Austria (population 8.5 million) has 11% of the population of Germany.

Show the name and the population of each country in Europe. Show the population as a percentage of the population of Germany.

SELECT name, CONCAT(ROUND(population/(SELECT population FROM world WHERE name='Germany')*100), '%')
FROM world
WHERE continent='Europe'



6.
Which countries have a GDP greater than every country in Europe? [Give the name only.] (Some countries may have NULL gdp values)

SELECT name
FROM world
WHERE gdp > ALL
(SELECT gdp FROM world WHERE continent='Europe' AND gdp IS NOT NULL)

/* NULL does not evalute to 0. If you want to compare values, remove
NULL first */


7.
Find the largest country (by area) in each continent, show the continent, the name and the area:

SELECT continent, name, area
FROM world x
  WHERE area >= ALL
    (SELECT area FROM world y
        WHERE y.continent=x.continent AND area IS NOT NULL)


8.
List each continent and the name of the country that comes first alphabetically.

SELECT continent, MIN(name)
FROM world
GROUP BY continent


9.
Find the continents where all countries have a population <= 25000000. Then find the names of the countries associated with these continents. Show name, continent and population.

SELECT name, continent, population
FROM world x
WHERE 25000000 >= ALL(SELECT population FROM world y WHERE x.continent=y.continent)


10.
Some countries have populations more than three times that of any of their neighbours (in the same continent). Give the countries and continents.

SELECT name, continent
FROM world x
WHERE population >= ALL(SELECT population*3 FROM world y WHERE x.continent=y.continent AND x.name != y.name)


USING NULL
==========


SELECT name
FROM teacher
WHERE dept IS NULL


3.
Use a different JOIN so that all teachers are listed.

SELECT teacher.name, dept.name
FROM teacher LEFT JOIN dept
ON (teacher.dept=dept.id)

4.
Use a different JOIN so that all departments are listed.

SELECT teacher.name, dept.name
FROM teacher RIGHT JOIN dept
ON (teacher.dept=dept.id)



5.
Use COALESCE to print the mobile number. Use the number '07986 444 2266' if there is no number given. Show teacher name and mobile number or '07986 444 2266'

SELECT name, COALESCE(mobile,'07986 444 2266')
FROM teacher


6.
Use the COALESCE function and a LEFT JOIN to print the teacher name and department name. Use the string 'None' where there is no department.

SELECT teacher.name, COALESCE(dept.name, 'None')
FROM teacher LEFT JOIN dept
ON (teacher.dept=dept.id)

7.
Use COUNT to show the number of teachers and the number of mobile phones.

SELECT COUNT(name), COUNT(mobile)
FROM teacher



8.
Use COUNT and GROUP BY dept.name to show each department and the number of staff. Use a RIGHT JOIN to ensure that the Engineering department is listed.

SELECT dept.name, COUNT(teacher.name)
FROM teacher RIGHT JOIN dept
ON (teacher.dept=dept.id)
GROUP BY dept.name


9.
Use CASE to show the name of each teacher followed by 'Sci' if the teacher is in dept 1 or 2 and 'Art' otherwise.

SELECT name,
CASE WHEN (dept=1 OR dept=2) THEN 'Sci' ELSE 'Art' END
FROM teacher


10.
Use CASE to show the name of each teacher followed by 'Sci' if the teacher is in dept 1 or 2, show 'Art' if the teacher's dept is 3 and 'None' otherwise.

SELECT name,
CASE WHEN (dept=1 OR dept=2) THEN 'Sci'
WHEN (dept=3) THEN 'Art'
ELSE 'None' END
FROM teacher


SELF JOIN
=========

1.
How many stops are in the database.

SELECT Count(id)
FROM stops

2.
Find the id value for the stop 'Craiglockhart'

SELECT id
FROM stops
WHERE stops.name='Craiglockhart'

3.
Give the id and the name for the stops on the '4' 'LRT' service.

SELECT stops.id, stops.name
FROM stops JOIN route ON stops.id=route.stop
WHERE num=4 AND company='LRT'


4.
The query shown gives the number of routes that visit either London Road (149) or Craiglockhart (53). Run the query and notice the two services that link these stops have a count of 2. Add a HAVING clause to restrict the output to these two routes.

SELECT company, num, COUNT(*)
FROM route WHERE stop=149 OR stop=53
GROUP BY company, num
HAVING COUNT(*)=2


5.
Execute the self join shown and observe that b.stop gives all the places you can get to from Craiglockhart, without changing routes. Change the query so that it shows the services from Craiglockhart to London Road.

SELECT a.company, a.num, a.stop, b.stop
FROM route a JOIN route b ON
  (a.company=b.company AND a.num=b.num)
WHERE a.stop=53 AND b.stop=(SELECT id FROM stops WHERE name='London Road')


6.
The query shown is similar to the previous one, however by joining two copies of the stops table we can refer to stops by name rather than by number. Change the query so that the services between 'Craiglockhart' and 'London Road' are shown. If you are tired of these places try 'Fairmilehead' against 'Tollcross'

SELECT a.company, a.num, stopa.name, stopb.name
FROM route a JOIN route b ON
  (a.company=b.company AND a.num=b.num)
  JOIN stops stopa ON (a.stop=stopa.id)
  JOIN stops stopb ON (b.stop=stopb.id)
WHERE stopa.name='Craiglockhart' AND stopb.name = 'London Road'

7.
Give a list of all the services which connect stops 115 and 137 ('Haymarket' and 'Leith')

SELECT DISTINCT a.company, a.num
FROM route a JOIN route b ON
  (a.company=b.company AND a.num=b.num)
WHERE a.stop = 115 AND b.stop = 137

8.
Give a list of the services which connect the stops 'Craiglockhart' and 'Tollcross'

SELECT a.company, a.num
FROM route a JOIN route b ON
  (a.company=b.company AND a.num=b.num)
WHERE a.stop = (SELECT id FROM stops WHERE name = 'Craiglockhart') AND b.stop = (SELECT id FROM stops WHERE name = 'Tollcross')

9.
Give a distinct list of the stops which may be reached from 'Craiglockhart' by taking one bus, including 'Craiglockhart' itself, offered by the LRT company. Include the company and bus no. of the relevant services.

SELECT stops.name, company, num
FROM route a JOIN stops ON stops.id = a.stop
WHERE a.company IN (SELECT company
FROM route
WHERE company = 'LRT' and stop = (SELECT id from stops where name = 'Craiglockhart')
) AND a.num IN (SELECT num
FROM route
WHERE company = 'LRT' and stop = (SELECT id from stops where name = 'Craiglockhart')
)
