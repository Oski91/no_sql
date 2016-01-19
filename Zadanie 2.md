#Zadanie 2 - Aggregation Pipeline

Do zadania egzaminacyjnego wykorzystałem bazę reddit z zadania 1. Baza zawiera ponad 50 milionów rekordów.

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

```

Wynik zapytania: 

###Zapytanie o ilość komentarzy użytkownika "PainMatrix": 
```js
db.redditColl.aggregate ([ {$match: {author: "PainMatrix"} }, 
{$group: { _id: "$author", count: {$sum:1}}} ])
```

Wynik zapytania: 
```js
{ "_id" : "PainMatrix", "count" : 1109 }
```

###Wykorzystanie zasobów komputera podczas wykonywania operacji agregacji.
![](http://i.imgur.com/xWgWm85.jpg)
