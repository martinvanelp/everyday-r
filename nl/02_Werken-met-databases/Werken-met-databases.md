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
4. Query met een functie
5. Wegschrijven
6. Disclaimer
7. Oefenen (facultatief)

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
db <- RODBC::odbcConnectAccess2007(paste0(tempdir(), "/datasets-from-readxl.accdb"))

RODBC::sqlTables(db, tableType = "TABLE")
```

```
                                                                       TABLE_CAT
1 C:\\Users\\mvane\\AppData\\Local\\Temp\\Rtmp0ME3vv\\datasets-from-readxl.accdb
2 C:\\Users\\mvane\\AppData\\Local\\Temp\\Rtmp0ME3vv\\datasets-from-readxl.accdb
3 C:\\Users\\mvane\\AppData\\Local\\Temp\\Rtmp0ME3vv\\datasets-from-readxl.accdb
4 C:\\Users\\mvane\\AppData\\Local\\Temp\\Rtmp0ME3vv\\datasets-from-readxl.accdb
  TABLE_SCHEM TABLE_NAME TABLE_TYPE REMARKS
1        <NA>   chickwts      TABLE    <NA>
2        <NA>       iris      TABLE    <NA>
3        <NA>     mtcars      TABLE    <NA>
4        <NA>     quakes      TABLE    <NA>
```

Netjes verbindingen sluiten:

```r
RODBC::odbcCloseAll()
```


Queries 1/3
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


Queries 2/3
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


Queries 3/3
========================================================

En even opruimen:


```r
RODBC::sqlDrop(db, "iris2")
```


Query met een functie 1/2
========================================================

Schrijf een functie die netjes afsluit:


```r
query_functie <- function(query)
{
    db <- RODBC::odbcConnectAccess2007(paste0(tempdir(), 
                                              "/datasets-from-readxl.accdb"))
    output <- RODBC::sqlQuery(db, query)
    RODBC::odbcClose(db)
    return(output)
}

head(query_functie("SELECT Id, Species, SepalLength, SepalWidth FROM iris"))
```

```
  Id Species SepalLength SepalWidth
1  1  setosa         5.1        3.5
2  2  setosa         4.9        3.0
3  3  setosa         4.7        3.2
4  4  setosa         4.6        3.1
5  5  setosa         5.0        3.6
6  6  setosa         5.4        3.9
```


Query met een functie 2/2
========================================================

Functie met parameter:


```r
query_species <- function(species)
{
    query <- paste0("SELECT * FROM iris WHERE Species = '", species, "'")
    query_functie(query)
}

head(query_species("virginica"))
```

```
   Id SepalLength SepalWidth PetalLength PetalWidth   Species
1 101         6.3        3.3         6.0        2.5 virginica
2 102         5.8        2.7         5.1        1.9 virginica
3 103         7.1        3.0         5.9        2.1 virginica
4 104         6.3        2.9         5.6        1.8 virginica
5 105         6.5        3.0         5.8        2.2 virginica
6 106         7.6        3.0         6.6        2.1 virginica
```

    
Wegschrijven 1/2
========================================================

Tabel in R, wegschrijven naar database:


```r
tabel <- data.frame(x = 1:10, y = 11:20)

RODBC::sqlSave(db, tabel, rownames = FALSE)

RODBC::sqlTables(db, tableType = "TABLE")
```

```
                                                                       TABLE_CAT
1 C:\\Users\\mvane\\AppData\\Local\\Temp\\Rtmp0ME3vv\\datasets-from-readxl.accdb
2 C:\\Users\\mvane\\AppData\\Local\\Temp\\Rtmp0ME3vv\\datasets-from-readxl.accdb
3 C:\\Users\\mvane\\AppData\\Local\\Temp\\Rtmp0ME3vv\\datasets-from-readxl.accdb
4 C:\\Users\\mvane\\AppData\\Local\\Temp\\Rtmp0ME3vv\\datasets-from-readxl.accdb
5 C:\\Users\\mvane\\AppData\\Local\\Temp\\Rtmp0ME3vv\\datasets-from-readxl.accdb
  TABLE_SCHEM TABLE_NAME TABLE_TYPE REMARKS
1        <NA>   chickwts      TABLE    <NA>
2        <NA>       iris      TABLE    <NA>
3        <NA>     mtcars      TABLE    <NA>
4        <NA>     quakes      TABLE    <NA>
5        <NA>      tabel      TABLE    <NA>
```


Wegschrijven 2/2
========================================================


```r
RODBC::sqlQuery(db, "SELECT x, y, x + y AS z FROM tabel")
```

```
    x  y  z
1   1 11 12
2   2 12 14
3   3 13 16
4   4 14 18
5   5 15 20
6   6 16 22
7   7 17 24
8   8 18 26
9   9 19 28
10 10 20 30
```




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
