# Zadanie 1 - EDA
Zadanie wykonałem na swoim komputerze stacjonarnym. Parametry komputera znajduja
 się w pliku [README.md](https://github.com/Oski91/no_sql/blob/master/README.md).
 
## Import bazy danych do MongoDB i PostgreSQL
 Do swojego zadania wybrałem bazę komentarzy z serwisu Reddit. Baza spakowana miała rozmiar 5.07 GB, a po rozpakowaniu 29.4 GB.
 
### Import do MongoDB
 Po skonfugurowaniu MongoDB przystąpiłem do importu bazy danych. Bazą był plik z plikami .json. Polecenie, którego użyłem do importu:
 ```javascript
mongoimport --db reddit --collection redditColl  <  C:\RC_2015-01.json 
```

*Użycie zasobów komputera poczas importu bazy:*

- *Procesor:*
![](http://i.imgur.com/v0rVseE.jpg)

- *Dysk Twardy:*
![](http://i.imgur.com/mQfcRjk.jpg)

Niestety nie wykonałem zrzutu ekranu wykorzystania zasobów pamięci RAM. Na załączonych zrzutach ekranowych widać, że pamięć RAM była wykorzystana w 97%. Przedstawione dane utrzymywały się przez większość czasu procesu importowania bazy do MongoDB. Występowały niewielkie wahania (+/- 5%).

Importowanie bazy rozpoczęło się o godzinie 17:01, a zakończyło o godzinie 17:27. Import trwał więc 26 minut. Zaimportowano 53 851 542 rekordy.

*Screen z MongoDB:*
![](http://i.imgur.com/NVYCVpI.jpg)

Na powyższym zrzucie ekranu widać, że baza danych "reddi" została zaimportowana do MongoDB. Zaimportowanych zostało 53 851 542 rekordy, które zajmują 45.932 GB. 
Statystyki te uzyskałem wykonując polecenia: 
```javascript
db.reddiColl.count()
``` 
To polecenie zlicza rekordy w bazie danych "reddit" z kolekcją danych "redditColl".
```javascript
db.redditColl.stats()
```
To polecenie pokazuje statystyki kolekcji danych w bazie "reddit". Widzimy między innymi liczbę zliczonych rekordów, rozmiar, średni rozmiar obiektu.




### Import do bazy PostreSQL
Import do PostgreSQL w wersji 9.4. 
Wykorzystałem tę samą bazę danych co w przypadku MongoDB. Do zaimprotowania plików .json do bazy danych wykorzystałem narzędznie PGFutter, które znalazłem na GitHubie. 
```javascript
https://github.com/lukasmartinelli/pgfutter
```
Import rozpocząłem poleceniem:
```javascript
pgfutter_windows_amd64.exe --pw "123qq" json "reddit.json"
```
Polecenie to uruchamia narzędzie PGFutter. --pw (password) zostało użyte, ponieważ dostęp do PostgreSQL jest możliwy tylko za pomocą hasła (w moim przypadku "123qq"). "json" jest parametrem, który informuje narzędzie o wczytaniu pliku .json.

*Użycie zasobów komputera podczas importu:*

- *Procesor*
![](http://i.imgur.com/xRjwvBL.png)

- *Pamięć*
![](http://i.imgur.com/hZGupH2.png)

Dysk w czasie importu został wykorzystywany w różnym stopniu. Na początku było to 50%, potem 100%. Skoki wykorzystywania dysku były przez cały czas importu. Wachały się między 50% a 100%.

Import trwał 27 minut i 7 sekund.

*Wyniki PostgreSQL*

Przeprowadziłem zliczanie rekordów w bazie zaimportowanej w PostgreSQL. Włączyłem pomiar czasu poprzez: 
```
\timing
```
i wpisałem komendę:
```
select count(*) from import.reddit;
```
Oto wyniki: 
![](http://i.imgur.com/2zbNqST.jpg)

Jak widać wykonanie zliczania instrukcji trwało aż 149816 ms, czyli ponad 2 minuty.

Zliczonych zostało 53 851 542 rekordów.


## Powrównanie wyników MongoDB oraz PostgreSQL
### Czas importu
Import bazy danych w MongoDB trwał 26 minut, zaś w PostrgreSQL 27 minut. MongoDB zaimportowało bazę szybciej o 1 minutę niż PostgreSQL.

### Użycie zasobów komputera
MongoDB podczas importu bazy danych używało 60% zasobów procesora, 97% pamięci oraz 60% dysku. Występowały małe wahania (+/- 5% każdego zasobu). Przy imporcie danych do PostgreSQL wykorzystywano mniej zasobów. Procesor około 50%, pamięć 50%, dysk od 60% do 100%. Wahania były bardzo duże, zużycie skakało od 50% do 100%. 

### Wynik importu
Zarówno MongoDB jak i PostgreSQL zliczyło tyle samo rekordów - 53 851 542. Jednakże wykonywanie instrukcji zliczających było dużo szybsze w MongoDB, były to zaledwie setki ms, tymczasem w PostgreSQL zliczenie wyników trwało ponad 2 minuty.



# Zadanie 2
## GeoJson
Do utworzenia mapy GeoJson wykorzystałem narzędzie, które znalazłem pod adresem:
http://geojson.io.
Wykonałem mapkę 34 stadionów w Polsce, na których swoje mecze rozgrywają lokalne drużyny.  [Mapka Stadionów](https://github.com/Oski91/no_sql/blob/master/stadiony.geojson). Kolorem żółtym oznaczyłem dużyny występujące w Ekstraklasie, a kolorem niebieskim drużyny występujące w pierwsze lidze.

Bazę danych zaimportowałem do MongoDB poleceniem:
```
    mongoimport -c stadiony5 < C:\stadiony.json
```

Przykładowy rekord wygląda następująco. Sprawdziem to poleceniem:

```
db.stadiony5.findOne()
```

Rekord: 
```
{
        "_id" : ObjectId("5672d410a1d0f12300c804af"),
        "type" : "Feature",
        "properties" : {
                "marker-color" : "#0000ff",
                "marker-size" : "medium",
                "marker-symbol" : "",
                "miasto" : "Gdynia",
                "klub" : "Arka"
        },
        "loc" : {
                "type" : "Point",
                "coordinates" : [
                        18.53114604949951,
                        54.49332432102238
                ]
        }
}
```

Dodałem geo-indexy poleceniem: 
```
db.stadiony5.ensureIndex({loc : "2dsphere"})
```
### Point

Załóżmy, że do Warszawy przyjeżdża osoba zza granicy i chce obejrzeć mecz ligi polskiej. Dzięki poleceniu: 
```
db.stadiony5.find ( {loc : { $geoWithin : { $centerSphere : [[ 21.020, 52.259], 100 / 3963.2 ] } } } )
```
możemy sprawdzić, który stadion jest nabliżej Warszawy w promieniu 100 mil.

Wyniki: [Mapka](https://github.com/Oski91/no_sql/blob/master/100milWarszawa.geojson)


Wykonałem to samo zapytanie, ale z innymi koordynatami. Tym razem wybrałem miasto Łódź.
```
db.stadiony5.find ( {loc : { $geoWithin : { $centerSphere : [[ 19.4561, 51.7686], 100 / 3963.2 ] } } } )
```

Wyniki wyglądają następująco : [Mapka](https://github.com/Oski91/no_sql/blob/master/100milLodz.geojson)

### Polygon
Stworzyłem kształt wojwództwa pomorskiego [Pomorskie](https://github.com/Oski91/no_sql/blob/master/polygonPomorskie.geojson)

Za pomoca polygonu chciałbym sprawdzić jakie stadiony znajdują się w województwie pomorskim. Oto zapytanie do MongoDB: 
```
db.stadiony5.find({loc: {$geoWithin: {$geometry: {type: "Polygon", 
coordinates: [ [ [ 16.72119140625, 54.56250772767092], [16.7431640625, 54.482804559582554] ... ]]]}}}})
 ```
 
 Wyniki prezentują się następująco: [Mapka](https://github.com/Oski91/no_sql/blob/master/wynikiPomorskie.geojson)


Załóżmy, że przyleciał turysta (groundhopper) do trójmiasta i chciałby wiedzieć gdzie i ile jest stadionów na dwóch najwyższych szczeblach rozgrywkowych w Polsce. Wykorzystałem do tego polygon Trójmiasta [Mapka](https://github.com/Oski91/no_sql/blob/master/polygonTrojmiasto.geojson)

Wyniki: [Mapka](https://github.com/Oski91/no_sql/blob/master/WynikiTrojmiasto.geojson)


### Line-String

TODO
