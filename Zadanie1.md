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