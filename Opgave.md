# Birgers Bolscher
### En SQL og database øvelse til Nodejs
Birger fik en bog om bolcher til sin fødselsdag da han blev 14 år. Siden har han eksperimenteret med fremstilling af mange forskellige bolcher og han har flittigt delt smagsprøver ud til sin familie og venner.

Nu er efterspørgslen blevet så stor, at han vil starte en egentlig produktion.  Birger har foreløbig otte forskellige bolchetyper han vil producere og han har lavet en tabel med navne, farver, smag osv. til sine bolcher.
Birger har ambitioner, så han vil have en webside med sine bolcher.

Det er her du kommer ind i billedet. Du får til opgave at sætte en database op og lave en række forskellige udtræk til visning i konsollen i første omgang - senere på en simpel webside.
Database skal være en MySQL database og udtrækket skal foregå via nodejs.


> Da det hele nu er en øvelse hvor du skal lære og træne SQL, sætte en database op og connecte via nodejs, så er det vigtigt at du løser opgaven slavisk og selvstændigt.
**Det handler ikke om at få løsningen**, for så er det ikke længere en øvelse - men du skal selv finde løsningen ved at læse og prøve dig frem – heri opstår læring som er målet med opgaven.


W3Schools er en rigtig god reference til det du søger i <a href="https://www.w3schools.com/sql" target="_blank">SQL</a>


Forklaring følger også i undervisningen


## Øvelse 1
1.1	Opret en database kaldet birgers_bolcher med én tabel kaldet bolche

1.2	Tabellen skal indeholde de viste felter og data – du bestemmer både feltnavne og datatyper

 ![Birgers Bolscher](./assets/birgers.png)
## Øvelse 2
Skriv en sql sætning for hver af følgende

2.1	Udskriv alle informationer om alle bolcher.
~~~
SELECT * FROM Bolscher
~~~

2.2	Find og udskriv navnene på alle de røde bolcher.
~~~
SELECT navn FROM Bolscher WHERE farve="Rød"
~~~

2.3	Find og udskriv navnene på alle de røde og de blå bolcher, i samme SQL udtræk.
~~~
SELECT navn FROM Bolscher WHERE farve="Rød" OR farve="Blå"
~~~

2.4	Find og udskriv navnene på alle bolcher, der ikke er røde, sorteret alfabetisk.
~~~
SELECT navn FROM Bolscher WHERE NOT farve="Rød" ORDER BY navn
~~~
el. 
~~~
SELECT navn FROM Bolscher WHERE farve!="Rød" ORDER BY navn
~~~

2.5	Find og udskriv navnene på alle bolcher som starter med et “B”.
~~~
SELECT navn FROM Bolscher WHERE navn LIKE "B%"
~~~

2.6	Find og udskriv navene på alle bolcher, hvor der i navnet findes mindst ét “e”.
~~~
SELECT navn FROM Bolscher WHERE navn LIKE "%E%"
~~~

2.7	Find og udskriv navn og vægt på alle bolcher der vejer mindre end 10 gram, sorter stigende efter vægt.
~~~
SELECT navn,vægt FROM Bolscher WHERE vægt <10 ORDER BY vægt ASC
~~~

2.8	Find og udskriv navne på alle bolcher, der vejer mellem 10 og 12 gram (begge tal inklusiv), sorteret alfabetisk og derefter vægt.
~~~
SELECT navn, vægt FROM Bolscher WHERE vægt >=10 AND vægt <=12 ORDER BY navn,vægt
~~~

2.9	Find og udskriv de tre største (tungeste) bolcher.
~~~
SELECT * FROM Bolscher ORDER BY vægt DESC LIMIT 3
~~~

2.10 Udskriv alle informationer om et tilfældigt bolche, udvalgt af systemet (sql).
~~~
SELECT * FROM Bolscher ORDER BY RANDOM() LIMIT 1
~~~

## Øvelse 3
3.1	Normaliser tabellen Bolscher så der dannes ”domænetabeller” til de felter hvor flere bolcher ofte har samme værdi.
~~~
CREATE TABLE Bolscher_Normalized (
    id INTEGER PRIMARY KEY,
    navn TEXT NOT NULL,
    farve_id INTEGER,
    vægt INTEGER NOT NULL,
    surhed_id INTEGER,
    styrke_id INTEGER,
    smag_id INTEGER,
    omkostninger INTEGER NOT NULL,
    FOREIGN KEY (farve_id) REFERENCES Farve(id),
    FOREIGN KEY (surhed_id) REFERENCES Surhed(id),
    FOREIGN KEY (styrke_id) REFERENCES Styrke(id),
    FOREIGN KEY (smag_id) REFERENCES Smag(id)
);`
~~~
~~~
INSERT INTO Bolscher_Normalized (id, navn, farve_id, vægt, surhed_id, styrke_id, smag_id, omkostninger)
SELECT 
    b.id, 
    b.navn, 
    (SELECT f.id FROM Farve f WHERE f.farve = b.farve) AS farve_id,
    b.vægt, 
    (SELECT s.id FROM Surhed s WHERE s.surhed = b.surhed) AS surhed_id,
    (SELECT st.id FROM Styrke st WHERE st.styrke = b.styrke) AS styrke_id,
    (SELECT sm.id FROM Smag sm WHERE sm.smag = b.smag) AS smag_id,
    b.omkostninger
FROM Bolscher b;
~~~

## Øvelse 4
4.1	Udskriv alle informationer om alle bolcher.
~~~
SELECT 
bo_no.id,bo_no.navn,bo_no.vægt,bo_no.omkostninger,
fa.farve, sm.smag, su.surhed, st.styrke
 
FROM Bolscher_Normalized bo_no
JOIN Farve fa ON bo_no.farve_id = fa.id
JOIN Smag sm ON bo_no.smag_id = sm.id
JOIN Surhed su ON bo_no.surhed_id = su.id
JOIN Styrke st ON bo_no.styrke_id = st.id
~~~

4.2	Find og udskriv navnene på alle de røde bolcher.
~~~
SELECT 
bo_no.navn,
fa.farve
 
FROM Bolscher_Normalized bo_no
JOIN Farve fa ON bo_no.farve_id = fa.id
WHERE fa.farve = "Rød"
~~~

4.3	Find og udskriv navnene på alle de røde og de blå bolcher, i samme SQL udtræk.
~~~
SELECT 
bo_no.navn,
fa.farve
 
FROM Bolscher_Normalized bo_no
JOIN Farve fa ON bo_no.farve_id = fa.id
WHERE fa.farve = "Rød" OR fa.farve = "Blå"
~~~
Alt:
~~~
WHERE fa.farve IN ("Rød","Blå");
~~~

4.4	Find og udskriv navnene på alle bolcher, der ikke er røde, sorteret alfabetisk.
~~~
SELECT 
bo_no.navn,
fa.farve
 
FROM Bolscher_Normalized bo_no
JOIN Farve fa ON bo_no.farve_id = fa.id
WHERE NOT fa.farve = "Rød" ORDER BY navn
~~~
Alt:
~~~
WHERE NOT fa.farve IN ("Rød") ORDER BY navn
~~~

4.5	Find og udskriv navnene på alle bolcher som starter med et “B”.
~~~
SELECT 
navn
 
FROM Bolscher_Normalized bo_no
WHERE navn LIKE "B%"
~~~

Tip: Tilføj dette i toppen (før select), hvis man ønsker case-sensetive search
~~~
PRAGMA case_sensitive_like=ON;
~~~


4.6	Find og udskriv navene på alle bolcher, hvor der i navnet findes mindst ét “e”.
~~~
SELECT 
navn
 
FROM Bolscher_Normalized bo_no
WHERE navn LIKE "%E%"
~~~

Tip: Tilføj dette i toppen (før select), hvis man ønsker case-sensetive search
~~~
PRAGMA case_sensitive_like=ON;
~~~

4.7	Find og udskriv navn og vægt på alle bolcher der vejer mindre end 10 gram, sorter stigende efter vægt.
~~~
SELECT 
navn, vægt
 
FROM Bolscher_Normalized bo_no
WHERE vægt <10 ORDER BY vægt ASC
~~~

4.8	Find og udskriv navne på alle bolcher, der vejer mellem 10 og 12 gram (begge tal inklusiv), sorteret alfabetisk og derefter vægt.
~~~
SELECT 
navn,vægt
 
FROM Bolscher_Normalized bo_no
WHERE vægt BETWEEN 10 AND 12 ORDER BY navn,vægt
~~~

4.9	Find og udskriv de tre største (tungeste) bolcher.
~~~
SELECT 
bo_no.id,bo_no.navn,bo_no.vægt,bo_no.omkostninger,
fa.farve, sm.smag, su.surhed, st.styrke
 
FROM Bolscher_Normalized bo_no
JOIN Farve fa ON bo_no.farve_id = fa.id
JOIN Smag sm ON bo_no.smag_id = sm.id
JOIN Surhed su ON bo_no.surhed_id = su.id
JOIN Styrke st ON bo_no.styrke_id = st.id
ORDER BY bo_no.vægt DESC LIMIT 3
~~~

4.10 Udskriv alle informationer om et tilfældigt bolche, udvalgt af systemet (sql).
~~~
SELECT 
bo_no.id,bo_no.navn,bo_no.vægt,bo_no.omkostninger,
fa.farve, sm.smag, su.surhed, st.styrke
 
FROM Bolscher_Normalized bo_no
JOIN Farve fa ON bo_no.farve_id = fa.id
JOIN Smag sm ON bo_no.smag_id = sm.id
JOIN Surhed su ON bo_no.surhed_id = su.id
JOIN Styrke st ON bo_no.styrke_id = st.id
ORDER BY RANDOM() LIMIT 1
~~~

## Øvelse 5
Nettopris for et bolche er råvareprisen plus 250 % (begge uden moms) 

5.1	Udskriv en prisliste med bolchenavn og kilopris henholdsvis med og uden moms
~~~
SELECT 
bo_no.id,bo_no.navn,bo_no.vægt,bo_no.omkostninger,
(bo_no.omkostninger + (bo_no.omkostninger * 2.5)) AS totalPris_uMoms,
(bo_no.omkostninger + (bo_no.omkostninger * 2.5)) * 1.25 AS totalPris_mMoms,
fa.farve, sm.smag, su.surhed, st.styrke

 
FROM Bolscher_Normalized bo_no
JOIN Farve fa ON bo_no.farve_id = fa.id
JOIN Smag sm ON bo_no.smag_id = sm.id
JOIN Surhed su ON bo_no.surhed_id = su.id
JOIN Styrke st ON bo_no.styrke_id = st.id
~~~

## Øvelse 6

~~.1	Løs opgave 2.3 ved brug af IN~~

~~6.2	Løs opgave 2.4 ved brug af NOT IN~~

6.3	Udskriv hvor mange bolscher der vejer under 15 g.
~~~
SELECT COUNT(vægt) 
FROM Bolscher_Normalized bo_no 
WHERE vægt < 15
~~~

6.4	Udskriv hvor mange forskellige forskellige bolcher der er i tabellen
Print alle unikke navne som text
~~~
SELECT DISTINCT navn FROM Bolscher_Normalized bo_no
~~~
Print count af unikke navne som tal
~~~
SELECT COUNT(DISTINCT navn) FROM Bolscher_Normalized bo_no
~~~
Bonus: Print count af unikke farver som tal
~~~
SELECT COUNT(DISTINCT farve_id) FROM Bolscher_Normalized bo_no
~~~


6.5	Udskriv gennemsnitsprisen per bolche
Her vælger jeg distinct igen, bare for at tage højde for evt. duplicates
~~~
SELECT SUM(omkostninger)/COUNT(DISTINCT navn) AS averagePrice
FROM Bolscher_Normalized bo_no
~~~

6.6	Udskriv navn og pris på det dyreste og billigste bolche
Simpel måde at printe højeste og laveste omkostninger uden at printe bolsche objektet
~~~
SELECT MIN(omkostninger), MAX(omkostninger)
FROM Bolscher_Normalized bo_no
~~~
Her printes bolsche objekterne med:
~~~
SELECT * 
FROM Bolscher_Normalized
WHERE omkostninger = (SELECT MIN(omkostninger) FROM Bolscher_Normalized)

UNION

SELECT * 
FROM Bolscher_Normalized
WHERE omkostninger = (SELECT MAX(omkostninger) FROM Bolscher_Normalized);
~~~



SIDE-NOTE: Nedenstående query virker IKKE SQLite, men måske i andre typer SQL: 
~~~
(SELECT * FROM Bolscher_Normalized ORDER BY omkostninger ASC LIMIT 1)
UNION
(SELECT * FROM Bolscher_Normalized ORDER BY omkostninger DESC LIMIT 1);
~~~
Husk paranteser omkring queries, in case SQL ikke kan lide at man bruge ORDER BY før en UNION