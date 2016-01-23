#Zadanie 2 - Aggregation Pipeline

Do zadania egzaminacyjnego wykorzystałem bazę reddit z zadania 1. Baza zawiera ponad 50 milionów rekordów.
Jako drivera do Mongo użyłem PyMongo, które znalazłem tutaj [PyMongo](https://docs.mongodb.org/ecosystem/drivers/python/).
Zapytania wyglądają dokładnie tak samo.

###Aby sprawdzić przykładowy rekord użyłem zapytania:
```js
db.redditColl.findOne()
```

###Przykładowy rekord wygląda następująco: 
```js
{
        "_id" : ObjectId("569a2dcf0545e456e89600a8"),
        "score_hidden" : false,
        "name" : "t1_cnas8zv",
        "link_id" : "t3_2qyr1a",
        "body" : "Most of us have some family members like this. *Most* of my family is like this. ",
        "downs" : 0,
        "created_utc" : "1420070400",
        "score" : 14,
        "author" : "YoungModern",
        "distinguished" : null,
        "id" : "cnas8zv",
        "archived" : false,
        "parent_id" : "t3_2qyr1a",
        "subreddit" : "exmormon",
        "author_flair_css_class" : null,
        "author_flair_text" : null,
        "gilded" : 0,
        "retrieved_on" : 1425124282,
        "ups" : 14,
        "controversiality" : 0,
        "subreddit_id" : "t5_2r0gj",
        "edited" : false
}
```

###Zapytanie o 3 najgorzyszych autorów postów: 
```js
db.redditColl.aggregate([ { $group: { _id: "$author", score: {$sum: "$score" } } },
{ $sort: {score: 1} }, { $limit: 3} ], {allowDiskUse: true})
```

Wynik zapytania:
```js
{ "_id" : "wutshappening", "score" : -8066 }
{ "_id" : "dwimback", "score" : -5241 }
{ "_id" : "salmonhelmet", "score" : -3499 }
```

Zapytanie w PyMongo:
```js
import pymongo
client = pymongo.MongoClient("localhost", 27017)
db = client.redditColl
db.redditColl.aggregate([ { $group: { _id: "$author", score: {$sum: "$score" } } },  
{ $sort: {score: 1} }, { $limit: 3} ], {allowDiskUse: true})
```


###Zapytanie o 3 najlepszych autorów postów:
```js
db.redditColl.aggregate([ {$group: { _id: "$author", score: { $sum: "$score"} } },
 { $sort: {score: -1}}, {$limit: 3}], {allowDiskUse: true})
```

Wynik zapytania:
```js
{ "_id" : "[deleted]", "score" : 7486663 }
{ "_id" : "AutoModerator", "score" : 185250 }
{ "_id" : "PainMatrix", "score" : 164494 }
```

###Zapytanie o ilość komentarzy mężczyzn: 
```js
db.redditColl.aggregate([ {$match: { author_flair_text: "Male" } }, 
{ $group: { _id: null, count: {$sum: 1} } } ])
```

Wynik zapytania: 
```js
{ "_id" : null, "count" : 26193 }
```


###Zapytanie o ilość komentarzy kobiet:
```js
db.redditColl.aggregate([ {$match: { author_flair_text: "Female" } }, 
{ $group: { _id: null, count: {$sum: 1} } } ])
```


Wynik zapytania:
```js
{ "_id" : null, "count" : 5025 }
```


###Zapytanie o ilość komentarzy osób, które nie podały płci:
```js
db.redditColl.aggregate( [ {$match: {author_flair_text: null} }, 
{ $group: { _id: null, count: {$sum: 1}}}])
```

Wynik zapytania: 
```js
{ "_id" : null, "count" : 29062486 }
```

Wszystkich rekordów w bazie jest 53 851 542, a postów które nie posiadają określonej płci jest 29 062 486. Stanowi to 53,96%. 


###Zapytanie o ilość komentarzy użytkownika "PainMatrix": 

```js
db.redditColl.aggregate ([ {$match: {author: "PainMatrix"} }, 
{$group: { _id: "$author", count: {$sum:1}}} ])
```

Wynik zapytania: 
```js
{ "_id" : "PainMatrix", "count" : 1109 }
```

Użytkownik na pozycji 3 w ilości zebranych pozytywnych ocen napisał 1109 postów.


###Wykorzystanie zasobów komputera podczas wykonywania operacji agregacji.
![](http://i.imgur.com/xWgWm85.jpg)

Podzespoły komputera były wykorzystywane intensywnie podczas wykonywania zapytań. Procesor był wykorzystywany w ponad 50% (każdy jego rdzeń), dysk miał wahania od 20% do 50% (przeważnie było to 40%). Pamięć RAM była wykorzystywana w 90%, będąc jednocześnie najbardziej wykorzystywanym podzespołem.

Długość kolejki dysku to liczba oczekujących żądań na dysku w momencie zbierania danych o wydajności. Oznacza to, że dysk fizyczny nie może obsługiwać żądań operacji we/wy tak szybko, jak są one generowane.
Skoki do 5 (w skali od 0 do 5) wystąpiły, gdy wysłałem zapytanie do MongoDB. Jednakże licznik ten może nieco zawyżać aktywność dysku.
