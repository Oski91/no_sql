# Zadanie 1
Zadanie wykonałem na swoim komputerze stacjonarnym. Parametry komputera znajduj
 się w pliku README.md.
 
## Import bazy danych do MongoDB i PostgreSQL
 Do swojego zadania wybrałem bazę komentarzy z serwisu Reddit. Baza spakowana miała rozmiar 5.07 GB, a po rozpakowaniu 29.4 GB.
 
### Import do MongoDB
 Po skonfugurowaniu MongoDB przystąpiłem do importu bazy danych. Bazą był plik z plikami .json. Polecenie, którego użyłem do importu: 
 ```
mongoimport --db reddit --collection redditColl  <  C:\RC_2015-01.json 
````

![](http://i.imgur.com/0Bxlv2P.jpg)

*Użycie zasobów komputera poczas importu bazy:*

- *Procesor:*
![](http://i.imgur.com/v0rVseE.jpg)

- *Dysk Twardy:*
![](http://i.imgur.com/mQfcRjk.jpg)

Niestety nie wykonałem zrzutu ekranu wykorzystania zasobów pamięci RAM. Na załączonych zrzutach ekranowych widać, że pamięć RAM była wykorzystana w 97%. Przedstawione dane utrzymywały się przez większość czasu procesu importowania bazy do MongoDB. Występowały niewielkie wahania (+/- 5%).

*Koniec importowania:*
![](http://i.imgur.com/zb8uWk6.jpg)

Importowanie bazy rozpoczęło się o godzinie 17:01, a zakończyło o godzinie 17:27. Import trwał więc 26 minut. Zaimportowano 53 851 542 rekordy.

*Screen z MongoDB:*
![](http://i.imgur.com/NVYCVpI.jpg)

Na powyższym zrzucie ekranu widać, że baza danych "reddi" została zaimportowana do MongoDB. Zaimportowanych zostało 53 851 542 rekordy, które zajmują 45.932 GB. 
Statystyki te uzyskałem wykonując polecenia: 
```
db.reddiColl.count()
``` 
To polecenie zlicza rekordy w bazie danych "reddit" z kolekcją danych "redditColl".
```
db.redditColl.stats()
```
To polecenie pokazuje statystyki kolekcji danych w bazie "reddit". Widzimy między innymi liczbę zliczonych rekordów, rozmiar, średni rozmiar obiektu.




### Import do bazy PostreSQL
Import do PostgreSQL w wersji 9.4. 
Wykorzystałem tę samą bazę danych co w przypadku MongoDB. Do zaimprotowania plików .json do bazy danych wykorzystałem narzędznie PGFutter, które znalazłem na GitHubie. 
```
https://github.com/lukasmartinelli/pgfutter
```
Import rozpocząłem poleceniem:
```
pgfutter_windows_amd64.exe --pw "123qq" json "reddit.json"
```
Polecenie to uruchamia narzędzie PGFutter. --pw (password) zostało użyte, ponieważ dostęp do PostgreSQL jest możliwy tylko za pomocą hasła (w moim przypadku "123qq"). "json" jest parametrem, który informuje narzędzie o wczytaniu pliku .json.

*Początek importu:*

![](http://i.imgur.com/qpfe8r7.png)

*Użycie zasobów komputera podczas importu:*

- *Procesor*
![](http://i.imgur.com/xRjwvBL.png)

- *Pamięć*
![](http://i.imgur.com/hZGupH2.png)

Dysk w czasie importu został wykorzystywany w różnym stopniu. Na początku było to 50%, potem 100%. Skoki wykorzystywania dysku były przez cały czas importu. Wachały się między 50% a 100%.

*Koniec importu:*
![](http://i.imgur.com/pu1sBEu.png)
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




## GeoJson
Do utworzenia mapy GeoJson wykorzystałem narzędzie, które znalazłem pod adresem:
http://geojson.io.
Wykonałem mapkę 34 stadionów w Polsce, na których swoje mecze rozgrywają lokalne drużyny.  [Mapka Stadionów](https://github.com/Oski91/no_sql/blob/master/stadiony.geojson). Kolorem żółtym oznaczyłem dużyny występujące w Ekstraklasie, a kolorem niebieskim drużyny występujące w pierwsze lidze.

Przykładowy rekord wygląda następująco: 
```
{
      "type": "Feature",
      "properties": {
        "marker-color": "#ffff00",
        "marker-size": "medium",
        "marker-symbol": "",
        "miasto": "Gliwice",
        "klub": "Piast"
      },
      "geometry": {
        "type": "Point",
        "coordinates": [
          18.695538640022278,
          50.30667181153402
        ]
      }
    }

 ```
    Bazę danych zaimportowałem do MongoDB poleceniem:
    ```
    mongoimport -d GeoTest -c stadiony < C:\stadiony2.geojson
    ```
![](http://i.imgur.com/YzHwKCk.jpg)
