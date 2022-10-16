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

Finally I tried to use the `rdfs:label` of the entities to compare them (the duck and the car model). But after multiple unsuccessful results, I found out that the car model `rdfs:label` did not include the car's nickname. So I discovered I could use instead the `skos:altLabel` property of the entities that contained many more nicknames for the entity.

#### Filtering

As a final requirement I needed to filter my result, I wanted to compare only the german `skos:altLabel` of the car with the german `rdfs:label`. So I searched and found the `LANG` function (see below).

```sql
-- Car
?model wdt:P31 wd:Q3231690;
    skos:altLabel ?modelAlias;
    FILTER(LANG(?modelAlias) = "de").

-- Duck
wd:Q3736439 rdfs:label ?duckLabel;
    FILTER(LANG(?duckLabel) = "de").
```
#### The query and result

With all the previous work done earlier I finally was able to write a working query that gave me the following result (see below).

```sql
-- Which car is called a “duck” in German ?

CONSTRUCT {
    --      instance of    automobile model
    ?model wdt:P31         wd:Q3231690;
        skos:altLabel ?modelAlias;
        rdfs:label ?modelLabel.
            
    -- Duck
    wd:Q3736439 rdfs:label ?duckLabel.
}
WHERE 
{
    --     instance of     automobile model
    ?model wdt:P31         wd:Q3231690;
        skos:altLabel ?modelAlias;
        rdfs:label ?modelLabel;
    FILTER(LANG(?modelAlias) = "de").
    FILTER(LANG(?modelLabel) = "fr").
    FILTER(STRSTARTS(?duckLabel, ?modelAlias)).
            
    -- Duck
    wd:Q3736439 rdfs:label ?duckLabel;
        FILTER(LANG(?duckLabel) = "de").
    
}
```
![](images/question1_graph.png)

Here we can see that the car found is the `Citroën 2CV`. They both have a label and/or an alias wich begin with the string `Ente`.

## Question 2

### Problem I want to solve
With what other famous people did Barck Obama studied throughout his life ?

### Data source

For this question I will use the same data source as the previous question: WikiData.

### Entities I need

For this question I will need the following entities:

- Barack Obama `Q76`
- Human `Q5`

### Relations I need

For this question I will need the following relations:

- educated at `P69`

### My experiments

#### Listing the schools of Barack Obama

To list the schools of Barack Obama I used the following query:

```sql
%%rdf sparql --endpoint https://query.wikidata.org/sparql

# With what other famous people did Barck Obama studied throughout his life ?

SELECT ?schoolLabel
WHERE 
{
    # Barrack Obama   # Educated At
    wd:Q76            wdt:P69          ?school.
    ?school rdfs:label ?schoolLabel;
        FILTER(LANG(?schoolLabel) = "en"). 
}
```

## Listing 100 people who have studied at the same school as Barack Obama

To list 100 people who have studied at the same school as Barack Obama I used the following query:

```sql
%%rdf sparql --endpoint https://query.wikidata.org/sparql

-- With what other famous people did Barck Obama studied throughout his life ?

SELECT ?personName
WHERE 
{
    -- Barrack Obama   -- Educated At
    wd:Q76            wdt:P69          ?school.
    ?school rdfs:label ?schoolLabel;
    FILTER(LANG(?schoolLabel) = "en").
    
            -- instance of  -- Human  
    ?person wdt:P31        wd:Q5;
        -- Educated At
        wdt:P69         ?school2;
        rdfs:label ?personName.
    FILTER(LANG(?personName) = "en").
    FILTER(?school = ?school2).
}
LIMIT 100
```

 ### Problem encountered


#### Filtering with date

 When I tried to filter the result to only keep the people who studied has Barack Obama did, It became harder for me. The relation `educated at` had a qualifier `start time` but I didn't know how to use it. So I had to look up WikiData documentation to find out how to use it.

 After reading the documentation I found an example describing a similar problem to mine. So I tried to use it in my query and after mutiple tries I finally got a working query.

```sql
    --! Educated At
    ?person p:P69 [
            --! Educated At
            ps:P69 ?school;
            --! Start Time
            pq:P580 ?date
        ];
```

 #### Removing Barack Obama from the results

I also wanted to remove Barack Obama from the result, because Barack Obama indeed studied in the same school and at the same period that Barack Obama. So I had to find a way to remove him from the result. I tried to use the `FILTER`.

```sql
FILTER(?person != wd:Q76).
```

 ### The query and result

# With what other famous people did Barck Obama studied throughout his life ?

```sql
SELECT DISTINCT ?personName ?date ?schoolLabel
WHERE 
{
    # Barrack Obama   # Educated At
    wd:Q76            p:P69        [
            ps:P69 ?school;
            pq:P580 ?date
    ].
   
            # instance of  # Human  
    ?person wdt:P31        wd:Q5.
            
        # Educated At
    ?person p:P69 [
            ps:P69 ?school;
            pq:P580 ?date
        ];
        rdfs:label ?personName.
    
   
    ?school rdfs:label ?schoolLabel;
        FILTER(LANG(?schoolLabel) = "en"). 
    FILTER(LANG(?personName) = "en").
  FILTER(?person != wd:Q76).
}
```