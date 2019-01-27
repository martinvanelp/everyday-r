Vervangen Excel copy-paste straten
========================================================
author: Martin van Elp
date: 2019-01-27
autosize: true


Agenda
========================================================

1. Beginsituatie
2. Inlezen
3. Bewerken
4. Wegschrijven
5. Disclaimer
6. Oefenen (facultatief)


Beginsituatie
========================================================

Stereotype verwerking met Excel:

- Levering van een bestand (map, e-mail, ...)
- Bestand openen (Excel, Access, SPSS, ...)
- Tabel kopi&#235;ren en plakken in inputtabblad eigen Excel
- Excel-formules bewerken de data
- Tabel/uitkomst uit outputtabblad overnemen/kopi&#235;ren/inlezen

Arbeidsintensief en gevoelig voor incidentele fouten.


Inlezen 1/3
========================================================

Een Excel bestand


```r
xlsx_bestand <- system.file(
    "readTest.xlsx", 
    package = "openxlsx")

xlsx_bestand
```

```
[1] "C:/Users/mvane/Documents/R/win-library/3.5/openxlsx/readTest.xlsx"
```

Bestand bekijken in Excel


```r
openxlsx::openXL(xlsx_bestand)
```


Inlezen 2/3
========================================================

Tabel uit bestand inlezen


```r
input <- openxlsx::read.xlsx(
    xlsx_bestand
    , sheet = 3
    , skipEmptyRows = TRUE
    , detectDates = TRUE)

head(input)
```

```
        Date     value      word  bool  wordZ2
1 2014-04-28 0.8390764 N-U-B-R-A FALSE FALSE-Z
2 2014-04-27 0.8863800 N-Z-P-S-Y  TRUE  TRUE-Z
3 2014-04-26 0.5741314 C-G-D-X-H  TRUE  TRUE-Z
4 2014-04-25 0.1366065      <NA> FALSE FALSE-Z
5 2014-04-24 0.3692582 B-K-A-O-W  TRUE  TRUE-Z
6 2014-04-23        NA H-P-G-O-K  TRUE  TRUE-Z
```


Inlezen 3/3
========================================================



Werken met bestanden


```r
bestanden <- list.files(path = tempdir(), 
                        pattern = ".tmp")
bestanden
```

```
[1] "bron_2019-01-01.tmp" "bron_2019-01-02.tmp" "bron_2019-01-03.tmp"
```

```r
bestand <- tail(sort(bestanden), 1)
bestand
```

```
[1] "bron_2019-01-03.tmp"
```

```r
paste0(tempdir(), "\\", bestand)
```

```
[1] "C:\\Users\\mvane\\AppData\\Local\\Temp\\RtmpsjShhO\\bron_2019-01-03.tmp"
```


Bewerken 1/2
========================================================

Aggregeren op periode


```r
library(dplyr)

output <- input %>%
    mutate(Period = substr(Date, 1, 7),
           Day    = substr(Date, 9,10)) %>%
    group_by(Period) %>%
    summarize(sumValue = 
                  sum(value, na.rm = TRUE))
head(output)
```

```
# A tibble: 6 x 2
  Period  sumValue
  <chr>      <dbl>
1 2008-08     7.99
2 2008-09    16.8 
3 2008-10    15.5 
4 2008-11    13.4 
5 2008-12    15.1 
6 2009-01    14.6 
```


Bewerken 2/2
========================================================

Analyseren


```r
plot(x = as.factor(output$Period), 
     y = output$sumValue)
```

<img src="Vervangen-Excel-copy-paste-straten-figure/unnamed-chunk-7-1.png" title="plot of chunk unnamed-chunk-7" alt="plot of chunk unnamed-chunk-7" style="display: block; margin: auto;" />


Wegschrijven 1/2
========================================================


```r
dir <- tempdir()

# simpel wegschrijven naar een CSV
write.csv2(
    output, 
    file = paste0(dir,"\\","verwerkt.csv"))

# simpel wegschrijven naar een Excel
openxlsx::write.xlsx(
    output, 
    file = paste0(dir,"\\","verwerkt.xlsx"))
```

.

Hoe ziet die Excel er uit?




Wegschrijven 1/2
========================================================

Datum in bestandsnaam


```r
naam  <- "output"
datum <- format(Sys.Date(), "%Y-%m-%d")

paste0(naam, "_", datum, ".tmp")
```

```
[1] "output_2019-01-27.tmp"
```


Disclaimer
========================================================

Alternatieven met Excel:

- querie&#235;n (Power Query) op bestand of database, met als resultaat een tabel;
- celverwijzingen naar een bestand en dan telkens bijwerken.

Nadelen van R:

- minder geneigd tijdens het werk "ff" iets aan te passen/verbeteren; 
- structurele fouten (bv. fout in formule) blijven zitten als ze niet opvallen.


Oefenen
========================================================

<center>
Voor wie wil: 

**Pak eigen bestanden en ga aan de slag**

<font color="red">(voor wie met productiedata werkt: maak een kopie!)</font>

.

*Ik loop rond om mee te denken en te helpen.*
</center>
