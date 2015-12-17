# Zadanie 1 - EDA
Zadanie wykonałem na swoim komputerze stacjonarnym. Parametry komputera znajduj
 się w pliku [README.md](https://github.com/Oski91/no_sql/blob/master/README.md).
 
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

Bazę danych zaimportowałem do MongoDB poleceniem:
```
    mongoimport -c stadiony5 < C:\stadiony.geojson
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
coordinates: [
          [
            [
              16.72119140625,
              54.56250772767092
            ],
            [
              16.7431640625,
              54.482804559582554
            ],
            [
              16.8310546875,
              54.45407332522336
            ],
            [
              16.864013671875,
              54.39655031438518
            ],
            [
              16.8255615234375,
              54.36135760559306
            ],
            [
              16.8310546875,
              54.322931143263474
            ],
            [
              16.864013671875,
              54.265224078605655
            ],
            [
              16.7486572265625,
              54.226707764386695
            ],
            [
              16.72119140625,
              54.19136954556111
            ],
            [
              16.7431640625,
              54.159217654166895
            ],
            [
              16.7816162109375,
              54.120602386175754
            ],
            [
              16.776123046875,
              54.0787285386706
            ],
            [
              16.7816162109375,
              54.03358633521085
            ],
            [
              16.7926025390625,
              53.991624640444314
            ],
            [
              16.875,
              53.985165238685454
            ],
            [
              16.8804931640625,
              53.94962061777842
            ],
            [
              16.907958984375,
              53.92375094101389
            ],
            [
              16.9903564453125,
              53.89786522246521
            ],
            [
              16.8914794921875,
              53.87196345747181
            ],
            [
              16.875,
              53.83308071272798
            ],
            [
              16.9134521484375,
              53.81362579235235
            ],
            [
              16.907958984375,
              53.784426471294296
            ],
            [
              16.85302734375,
              53.73246635451261
            ],
            [
              16.885986328125,
              53.6544055985474
            ],
            [
              16.8914794921875,
              53.608803292930894
            ],
            [
              16.951904296875,
              53.55988897245466
            ],
            [
              17.0123291015625,
              53.556626004824615
            ],
            [
              16.995849609375,
              53.52071674896369
            ],
            [
              17.0892333984375,
              53.54683559190011
            ],
            [
              17.20458984375,
              53.533778184257805
            ],
            [
              17.38037109375,
              53.494581793167185
            ],
            [
              17.3968505859375,
              53.51418452077113
            ],
            [
              17.4188232421875,
              53.579461493732296
            ],
            [
              17.501220703125,
              53.605544099237974
            ],
            [
              17.5726318359375,
              53.57620003591595
            ],
            [
              17.6055908203125,
              53.60228465401125
            ],
            [
              17.7264404296875,
              53.59902495724183
            ],
            [
              17.7703857421875,
              53.69020141273198
            ],
            [
              17.8363037109375,
              53.68369534495075
            ],
            [
              17.885742187499996,
              53.69020141273198
            ],
            [
              17.896728515625,
              53.716215632472036
            ],
            [
              17.852783203125,
              53.76170183021049
            ],
            [
              17.9681396484375,
              53.73571574532637
            ],
            [
              18.028564453125,
              53.72921671251269
            ],
            [
              18.072509765625,
              53.78118084719588
            ],
            [
              18.1109619140625,
              53.76170183021049
            ],
            [
              18.1439208984375,
              53.76170183021049
            ],
            [
              18.183059692382812,
              53.74221377343122
            ],
            [
              18.185806274414062,
              53.753582905249914
            ],
            [
              18.19953918457031,
              53.75845444856318
            ],
            [
              18.20159912109375,
              53.73896488496292
            ],
            [
              18.214645385742188,
              53.75033489579978
            ],
            [
              18.22357177734375,
              53.74261986682994
            ],
            [
              18.249664306640625,
              53.743025956304315
            ],
            [
              18.255844116210938,
              53.74261986682994
            ],
            [
              18.266143798828125,
              53.74343204185437
            ],
            [
              18.2757568359375,
              53.71743465447551
            ],
            [
              18.255157470703125,
              53.71133919115985
            ],
            [
              18.26202392578125,
              53.70077162898384
            ],
            [
              18.33343505859375,
              53.69345406966439
            ],
            [
              18.3526611328125,
              53.68816837453525
            ],
            [
              18.365020751953125,
              53.689794812945905
            ],
            [
              18.378753662109375,
              53.687761755115886
            ],
            [
              18.450851440429688,
              53.69386063411168
            ],
            [
              18.453941345214844,
              53.6874567879745
            ],
            [
              18.461322784423828,
              53.68796506531642
            ],
            [
              18.467159271240234,
              53.69182777261044
            ],
            [
              18.467159271240234,
              53.69985700363742
            ],
            [
              18.471965789794922,
              53.70392185304006
            ],
            [
              18.480377197265625,
              53.70310891456818
            ],
            [
              18.48724365234375,
              53.70382023658989
            ],
            [
              18.49325180053711,
              53.69985700363742
            ],
            [
              18.511619567871094,
              53.702499200408404
            ],
            [
              18.519344329833984,
              53.693047501290636
            ],
            [
              18.51093292236328,
              53.69009976315357
            ],
            [
              18.510417938232422,
              53.68105196776549
            ],
            [
              18.52518081665039,
              53.6800352400543
            ],
            [
              18.529987335205078,
              53.6688496152683
            ],
            [
              18.536682128906246,
              53.667934296341635
            ],
            [
              18.548870086669922,
              53.65613507265415
            ],
            [
              18.57685089111328,
              53.65847483645534
            ],
            [
              18.580799102783203,
              53.66681554620711
            ],
            [
              18.574790954589844,
              53.67169714699876
            ],
            [
              18.56363296508789,
              53.67342591165974
            ],
            [
              18.573589324951172,
              53.67891681122369
            ],
            [
              18.585262298583984,
              53.67362929107377
            ],
            [
              18.590755462646484,
              53.672714075978654
            ],
            [
              18.597965240478516,
              53.672714075978654
            ],
            [
              18.60877990722656,
              53.67047679982184
            ],
            [
              18.619251251220703,
              53.673324221584544
            ],
            [
              18.628520965576172,
              53.67403604695639
            ],
            [
              18.634357452392578,
              53.67749331339578
            ],
            [
              18.63555908203125,
              53.68217033989985
            ],
            [
              18.652896881103516,
              53.69487702805174
            ],
            [
              18.651866912841797,
              53.69680810893038
            ],
            [
              18.662853240966797,
              53.69802769331543
            ],
            [
              18.674697875976562,
              53.69375899336796
            ],
            [
              18.696670532226562,
              53.69975537737199
            ],
            [
              18.709373474121094,
              53.68613523817129
            ],
            [
              18.717269897460938,
              53.68898160159386
            ],
            [
              18.727569580078125,
              53.68369534495075
            ],
            [
              18.74164581298828,
              53.69365735237884
            ],
            [
              18.745765686035156,
              53.68613523817129
            ],
            [
              18.76636505126953,
              53.70056838062454
            ],
            [
              18.783187866210938,
              53.70321053273598
            ],
            [
              18.75640869140625,
              53.6039144080669
            ],
            [
              19.12445068359375,
              53.58435320870081
            ],
            [
              19.17938232421875,
              53.64300964258231
            ],
            [
              19.215087890624996,
              53.695080303894954
            ],
            [
              19.302978515625,
              53.7438381234801
            ],
            [
              19.31121826171875,
              53.81200414120256
            ],
            [
              19.456787109374996,
              53.79740645735382
            ],
            [
              19.489746093749996,
              53.77468884583577
            ],
            [
              19.5611572265625,
              53.92698552779884
            ],
            [
              19.40460205078125,
              53.93345394944651
            ],
            [
              19.35516357421875,
              53.96254944195083
            ],
            [
              19.396362304687496,
              53.996468534299375
            ],
            [
              19.30023193359375,
              54.02713344412544
            ],
            [
              19.275512695312496,
              54.04648911335576
            ],
            [
              19.22882080078125,
              54.07389422020433
            ],
            [
              19.22607421875,
              54.11416300731598
            ],
            [
              19.24530029296875,
              54.16243396806781
            ],
            [
              19.324951171875,
              54.20101023973888
            ],
            [
              19.314651489257812,
              54.22590495874594
            ],
            [
              19.29473876953125,
              54.24958116298901
            ],
            [
              19.260406494140625,
              54.26602611945041
            ],
            [
              19.255599975585938,
              54.28206365961121
            ],
            [
              19.638061523437496,
              54.38695529486881
            ],
            [
              19.64630126953125,
              54.457266680933856
            ],
            [
              19.322204589843746,
              54.374158445055734
            ],
            [
              18.9788818359375,
              54.35975722010785
            ],
            [
              18.7646484375,
              54.37575826945392
            ],
            [
              18.55316162109375,
              54.473229723078816
            ],
            [
              18.5614013671875,
              54.56728497153871
            ],
            [
              18.4954833984375,
              54.66271234496802
            ],
            [
              18.39385986328125,
              54.74206430970056
            ],
            [
              18.42132568359375,
              54.77851410375888
            ],
            [
              18.6163330078125,
              54.718275018302315
            ],
            [
              18.720703125,
              54.659535037041586
            ],
            [
              18.797607421875,
              54.59275415276928
            ],
            [
              18.841552734375,
              54.61184559302165
            ],
            [
              18.84429931640625,
              54.63092808215077
            ],
            [
              18.56964111328125,
              54.754746221869
            ],
            [
              18.42681884765625,
              54.80068486732233
            ],
            [
              18.2867431640625,
              54.83233630197034
            ],
            [
              17.68524169921875,
              54.79593501279976
            ],
            [
              17.221069335937496,
              54.73572186418134
            ],
            [
              17.03704833984375,
              54.66588940440647
            ],
            [
              16.9134521484375,
              54.61184559302165
            ],
            [
              16.72119140625,
              54.56250772767092
            ]]]}}}})
 ```
 
 Wyniki prezentują się następująco: [Mapka](https://github.com/Oski91/no_sql/blob/master/wynikiPomorskie.geojson)
