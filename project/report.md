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