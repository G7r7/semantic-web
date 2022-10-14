# Project: Query Answering over Linked Data
## Introduction

This report will be documenting my work over using linked data database to answer real world questions. The technology used for that will be SPARQL, a querying language close to SQL but specialized in the use of linked data databases. The work has been done in 2 Jupyter notebooks, one for each question of the project.

## Question 1

The first problem I tried to tackle in this project was to try to answer a rather simple but yet specialized question. It was for me a way to initiate myself to the use of the SPARQL language and the browsing of linked databases.

The question I choosed to answer was `"Which car is called a “duck” in German?"`.

To solve this problem I had to answer multiple interrogations I had, see below.

### What source of data should I use ?

The first thing to determine was which source of data should I use to answer the question ? Multiple valid choices existed, so I began experimenting.

#### DBPedia

The first source of data I knew of was the website of DBPedia https://www.dbpedia.org/. Because it was referenced during our semantic web course in Polytech, in first I began searching on their website how to manually browse their database, to see if I was able to find the entities that where linked to my question: a `duck` and a `car`. This was harder than I thought because DBPedia doesn't provide a way to manually research entities through a search bar in the manner of the Google search engine.
The only way I found to browse existing data was through SPARQL queries. That appeared too hard for me, so I quickly decided to search for another data source for my problem.

#### WikiData

The second data source I knew was WikiData accessible at the url https://www.wikidata.org/. This data source have the avantage to offer a search bar wich allow everyone to just type in a word or some text and find some close results to what you are looking for.

For instance for the entity corresponding to a duck, we can type in the word `duck` and instantly find a link to the corresponding ressource (see figure below).

![](images/wikidata_search_duck.png)

Then by clicking on it we have all informations about the entity displayed. Here we can see that an unique identifier is assiocated to the duck entity: `Q3736439`. Thus we will be able to use it for our problem solving (see below).

![](images/wikidata_duck.png)

### How to translate my problem in a SPARQL query ?

Now that the datasource is chosen, I need to translate my question in a SPARQL query. To do that I will need to identify the features of the SPARQL language that will be useful for my problem.

My problem is to determine `"Which car is called a “duck” in German?"`.

#### String comparaison

To find which car is called `"duck"` there will be a operation of string comparaison involved at some point in my query so I searched what I could use to accomplish this comparaison. I searched on a search engine `"comparing strings in sparql"` and found the following function:

```sql
FILTER(ex:ldistance(?string1, ?string2) < 2)
```

This function returns `true` if the two string provided have a `Levenshtein distance` inferior to 2 in the previous example. But when I tried using it it wasn't recognized as a valid function so I searched for another function.

```sql
FILTER(STRSTARTS(?string1, ?string2))
```

The second function I found to compare string was `STRSTARTS`. It returns `true` when `string1` starts with `string2`. It will be useful to compare the name of a duck in german with the name of the cars.

#### Entities involved

The two entities I wanted to use as a base for my query were the `duck` entity and the `car` entity. As shown previously I already found the `duck` entity easily. Then I had to find the `car` entity.

After doing a quick search in the search bar I found the entity `Q1420` (see figure below).

![](/project/images/wikidata_motor_car.png)

But when I wanted to list all car names I got only a few hundred names which is far from from the total number of names (see figure below) of car model, so I decided to use another entity.

![](/project/images/jupyter_car_names.png)

I tried typing my car model name on WikiData search bar `Peugeot 107`. I found that the entity assiocated was instead `automobile model (Q3231690)`. So I decided to use this entity instead for my query (see below).

![](images/wikidata_peugeot_107.png)