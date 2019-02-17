Werken met databases
========================================================
author: Martin van Elp
date: 2019-02-17
autosize: true


Agenda
========================================================
    
1. Beginsituatie
2. Allereerst
2. Verbinding maken
3. Queries
4. Wegschrijven
5. Disclaimer
6. Oefenen (facultatief)

&nbsp;

Bron: https://tbradley1013.github.io/2017/08/26/sql-management-in-r/


Beginsituatie
========================================================
    
Stereotype verwerking met Access:
    
- Open een Access-bestand met tabellen of koppeling met een SQL-database.
- Data in (gelinkte) tabel filteren en bekijken.
- Queries maken en draaien voor maatwerkuitkomsten.
- Tabel kopi&#235;ren en plakken, enzovoort.

Visualisaties, complexe bewerkingen en onderhoud zijn lastig.


Allereerst
========================================================

Bij het werken met databases ga je tegen technische obstakels oplopen.

Bekende problemen:

- Ontbrekende database-drivers;
- Verschillende versies (32/64-bit) van R, Windows en/of Office geven errors.

Wat kan helpen:

- Installeer de **Microsoft Access Database Engine 2016 Redistributable**
    (https://www.microsoft.com/en-us/download/details.aspx?id=54920).



Verbinding maken
========================================================

Verbinding met voorbeeld database:




```r
db <- RODBC::odbcConnectAccess2007("../../data/datasets-from-readxl.accdb")

RODBC::sqlTables(db, tableType = "TABLE")
```

```
                                 TABLE_CAT TABLE_SCHEM TABLE_NAME
1 ..\\..\\data\\datasets-from-readxl.accdb        <NA>   chickwts
2 ..\\..\\data\\datasets-from-readxl.accdb        <NA>       iris
3 ..\\..\\data\\datasets-from-readxl.accdb        <NA>     mtcars
4 ..\\..\\data\\datasets-from-readxl.accdb        <NA>     quakes
  TABLE_TYPE REMARKS
1      TABLE    <NA>
2      TABLE    <NA>
3      TABLE    <NA>
4      TABLE    <NA>
```

Netjes verbindingen sluiten:

```r
RODBC::odbcCloseAll()
```


Queries 1/n
========================================================




```r
x <- RODBC::sqlQuery(db, "SELECT * FROM iris")

head(x)
```

```
  Id SepalLength SepalWidth PetalLength PetalWidth Species
1  1         5.1        3.5         1.4        0.2  setosa
2  2         4.9        3.0         1.4        0.2  setosa
3  3         4.7        3.2         1.3        0.2  setosa
4  4         4.6        3.1         1.5        0.2  setosa
5  5         5.0        3.6         1.4        0.2  setosa
6  6         5.4        3.9         1.7        0.4  setosa
```


Queries 2/n
========================================================

Nieuwe tabel maken met query:


```r
RODBC::sqlQuery(db, "SELECT Id, Species INTO iris2 FROM iris")
```

```
character(0)
```

```r
y <- RODBC::sqlQuery(db, "SELECT * FROM iris2")

head(y)
```

```
  Id Species
1  1  setosa
2  2  setosa
3  3  setosa
4  4  setosa
5  5  setosa
6  6  setosa
```


Queries 3/n
========================================================

En even opruimen:


```r
RODBC::sqlDrop(db, "iris2")
```


Queries n/n
========================================================

Schrijf een functie die netjes afsluit:



    
Wegschrijven
========================================================


Disclaimer
========================================================
    
- Verwaarloos je SQL vaardigheden niet! Met SQL kun je veel tijdwinst boeken.

- Zijn databases helemaal nieuw voor je en ken je de tidyverse wel al? 
    Bekijk dan ook *dbplyr*!
    
- *RODBC* traag? Onderzoek of je *odbc* kunt gebruiken.
    
    
Oefenen
========================================================
    
<center>
Voor wie wil: 
    
**Pak eigen bestanden en ga aan de slag**
    
<font color="red">(voor wie met productiedata werkt: maak een kopie!)</font>
    
&nbsp;

*Ik loop rond om mee te denken en te helpen.*
</center>
