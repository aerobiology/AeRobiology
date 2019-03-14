# AeRobiology
A computational tool to deal with aerobiological data
<style>
p.comment {
background-color: #DBDBDB;
padding: 10px;
border: 1px solid black;
margin-left: 25px;
border-radius: 5px;
font-style: italic;
}

</style>
This package gathers different tools for managing aerobiological databases, elaborating the main calculations and visualization of results. In a first step, data may be checked using tools for quality control and all missing gaps can be completed. Then, the main parameters of the pollen season can be calculated and represented graphically. Multiple graphical tools are available: pollen calendars, phenological plots, time series, tendencies, interactive plots, abundance plots...

**Please, take into account that not all the arguments of each function are explained in this document. This is a quick guide and further details can be consulted in the [official document](https://cran.r-project.org/web/packages/AeRobiology/AeRobiology.pdf) of the package.**

The first thing you have to do is to install from CRAN repository and to load the package <font size="2" face="verdana">AeRobiology</font>.
<div/>






``` r
install.packages("AeRobiology")
library (AeRobiology)
```

Using the attached data
=======================

During this tutorial we are going to use the **pollen data from Munich** which are **integrated in the package**. This will allow you to follow the tutorial obtaining the same results. If you want to follow the tutorial by using your own data, check the next section.

<p class="comment">
**`munich`** is a data set containing information of daily concentrations of pollen in the atmosphere of Munich during the years 2010\_2015. Pollen types included: "Alnus", "Betula", "Taxus", "Fraxinus", "Poaceae", "Quercus", "Ulmus" and "Urtica".
Data were obtained at Munich (Zentrum Allergie und Umwelt, ZAUM) using a Hirst\_type volumetric pollen trap (Hirst, 1952) following the standard methodology (VDI4252\_4\_2016). Some gaps have been added to test some functions of the package (e.g. <font size="2" face="verdana">qualitycontrol()</font>, <font size="2" face="verdana">interpollen()</font>). <br> The data were obtained by the research team of Prof. Jeroen Buters (Christine Weil & Ingrid Weichenmeier). At the Zentrum Allergie und Umwelt ([ZAUM](https://www.zaum-online.de/), directed by Prof. Carsten B. Schmidt\_Weber).
</p>
You can **load the data** in your working environment by typing:

``` r
data("munich")
```

Loading your database from Excel
================================

This section has been developed for people who are not familiar with R. **If you have experience in loading databases in R, you might not be interested in this section and you can skip it.**

There are several functions to import data from Excel. Arbitrarily, we are going to use the package **<font size="3" face="verdana">readxl</font>**.

``` r
install.packages("readxl")
library (readxl)
```

Now we can import the data with the function **<font size="3" face="verdana">read\_xlsx()</font>**. I strongly recommend you to **have your Excel file in the same folder in which you are working with R**. This will avoid long paths to your files and also will prevent broken paths when some files have been moved. If you are working with an old database, you might be interested in the **<font size="3" face="verdana">read\_xls()</font>** function.

``` r
Mydata<-read_xlsx("C:/Users/Antonio/Desktop/Prueba Markdown/mydata.xlsx")
```

You can also **select the sheet** you want to import:

``` r
Mydata<-read_xlsx("C:/Users/Antonio/Desktop/Prueba Markdown/mydata.xlsx", sheet=2)
```

**IMPORTANT**: You must remove all the symbols or letters from your database. **If you have missing gaps within your database don't put "-" or "No Data" or any letter in the gaps**, keep the cell empty or remove the cell. The dates should be in a complete format (E.g.:"14/02/2019"). There must be a first column with the date followed by as many columns as pollen types with only numeric values (**check decimal separation: commas must be replaced by dots**).

Now you should have loaded your data in the object `Mydata`. Let's check if there is any mistake:

``` r
str(munich)
```

    ## 'data.frame':    2191 obs. of  9 variables:
    ##  $ date    : Date, format: "2010-01-01" "2010-01-02" ...
    ##  $ Alnus   : num  NA NA NA NA NA NA NA NA NA NA ...
    ##  $ Betula  : num  NA NA NA NA NA NA NA NA NA NA ...
    ##  $ Taxus   : num  NA NA NA NA NA NA NA NA NA NA ...
    ##  $ Fraxinus: num  NA NA NA NA NA NA NA NA NA NA ...
    ##  $ Poaceae : num  NA NA NA NA NA NA NA NA NA NA ...
    ##  $ Quercus : num  NA NA NA NA NA NA NA NA NA NA ...
    ##  $ Ulmus   : num  NA NA NA NA NA NA NA NA NA NA ...
    ##  $ Urtica  : num  NA NA NA NA NA NA NA NA NA NA ...

**Note**: I'm using `munich` database as example but you should use `Mydata`.

As you can see, the object is a `data.frame`, the first column is "Date" type and the rest are "num", which means numeric. This is the format you database **must** have. Normally it is automatically recognised by the import function, but if not, check the functions: <font size="3" face="verdana">**as.Date()**</font>, <font size="3" face="verdana">**as.numeric()**</font> and <font size="3" face="verdana">**as.data.frame()**</font>.

<p>
**Your database must have this format to start working with the package**. The functions are designed to warn you in case the format is not correct but there may be some exceptions. **This is the most important step when using the package and some strange errors reported by the functions can be solved by doing this**. Sometimes the date column has 2 different types simultaneously ("POSIXlt" and "POSIXct") and it might cause mistakes. You can use one or another, but not both. Furthermore, it is strongly recommended to use "Date" format instead of "POSIXlt" or "POSIXct" despite theoretically there may not be problems by using them.
</p>
<br>

<p>
**Please, don\`t exasperate**. This is the **slowest step of using the package and the most important**. Once you have your data loaded, you only have to spend a few minutes in each function to have your results. If you have some unsolving problems, search on the internet. Some other people must have had your same problem and it should be solved in some forums. If not, write us and we will help you as soon as possible: <aerobiology.package@gmail.com>
</p>

<p class="comment">
If you want to import your data from **csv files**, you might be interested in **<font size="3" face="verdana">read.csv()</font>** function.
</p>
Function <font face="verdana">qualitycontrol()</font>
=====================================================

**This function was designed to check the quality of an historical database of several pollen types**. Since many of the quality requirements depend on the criteria selected to establish the main pollen season, this function has the **<font size="3" face="verdana">calculate\_ps()</font> function integrated**. You can insert arguments of calculate\_ps() in this function to select the method you want for calculating the main pollen season. calculate\_ps() details can be consulted later in this same manuscript.

<p class="comment">
This function returns a **graphical resume** of the quality of the database marking with different red intensities the "weak-points" of it, and a `data.frame` with the detailed reasons of their "weakness".
</p>
**It establishes the quality according to 5 different criteria**:

-   **It checks if the main pollen season cannot be calculated according to the minimal requirements:** lack of data for this pollen type and year. This criteria appears as **"Complete"** in the "QualityControl" data.frame

-   **If the start, end or peak dates of the main pollen season have been interpolated** (or a date near them specified by **`int.window`** argument). It is based on the following premise: If this day or a nearby date is missing/interpolated, the selected date as start/peak/end might not be the real one. These criteria appear as **"Start"**, **"Peak"** and **"End"** in the "QualityControl" data.frame.

-   **The percentage of missing data within the main pollen season.** It calculates the number of days which have been interpolated by interpollen() function and their percentage of the main pollen season. If a high percentage of the main pollen season has been interpolated, the information of this season might not be reliable. The maximal percentage allowed can be specified by the **`perc.miss`** argument. This filter appears as **"Comp.MPS"** in the "QualityControl" data.frame.

When running the function, it gives you a **graphical resume** with different color intensity depending on the risk of including each pollen/spore type for a concrete year and a **data.frame** with detailed information about the reasons of this evaluation.+

``` r
qualitycontrol(munich)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-10-1.png)

If the filter has been successfully passed, it appears as `TRUE` in the **QualityControl** `data.frame`. If not, it appears as `FALSE`.

``` r
head(QualityControl)
```

    ##    type seasons Complete Start  Peak   End Comp.MPS
    ## 1 Alnus    2010     TRUE  TRUE  TRUE FALSE    FALSE
    ## 2 Alnus    2011     TRUE FALSE  TRUE  TRUE    FALSE
    ## 3 Alnus    2012     TRUE  TRUE  TRUE  TRUE     TRUE
    ## 4 Alnus    2013     TRUE FALSE  TRUE FALSE     TRUE
    ## 5 Alnus    2014     TRUE FALSE  TRUE FALSE    FALSE
    ## 6 Alnus    2015     TRUE FALSE FALSE  TRUE    FALSE

There are lots of arguments for this function. You can consult them in the "help" section of the function in R or in the [official document](https://cran.r-project.org/web/packages/AeRobiology/AeRobiology.pdf) of the package.

Let's suppose we want to check the quality of our database, but we want to calculate it for a **80% defined Main Pollen Season** (arguments **`ps.method`** and **`perc`**). We don't want to take into account years with a missing data less than **4 days** away from the start, peak or end dates (argument **`int.window`**). Furthermore, we only want to exclude years with more than **50% of interpolated data** within the main pollen season (argument **`perc.miss`**):

``` r
qualitycontrol(munich, int.window = 4, perc.miss = 50, ps.method = "percentage", perc = 80)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-12-1.png)

**As you would have noticed, there are several differences between this result and the previous one.**

**<font size="3"" face="verdana">interpollen()</font> function** is integrated in this function. Consult the "help" section of this function to use it.

Function <font face="verdana">interpollen()</font>
==================================================

The function **<font size="3" face="verdana">interpollen()</font>** was designed to complete all the missing gaps within a database in just one step. All the gaps of each pollen/spore type are completed simultaneously. **There are different methods to do so which may be more or less appropriate according to the particularities of your database.** The arguments are:

-   **data**: A `data.frame` object. This data.frame should include a first column in "Date" format and the rest of the columns in "numeric" format belonging to each pollen/spore type by column.
-   **method**: A `character` string specifying the method applied to calculate and generate the pollen missing data. The implemented methods that can be used are: **"lineal"**, **"movingmean"**, **"spline"**, **"tseries"** or **"neighbour"**. The method argument will be "lineal" by default.
-   **maxdays**: A `numeric (interger)` value specifying the maximum number of consecutive days with missing data that the algorithm is going to interpolate. If the gap is bigger than the argument value, the gap will not be interpolated. Not valid with <font size="2" face="verdana">"tseries"</font> method. The <font size="2" face="verdana">maxdays</font> argument will be 30 by default. This argument might be very interesting to avoid long interpolated gaps.
-   **plot**: A `logical (interger)` argument. If `TRUE`, graphical previews of the input database will be plot at the end of the interpolation process. All the interpolated gaps will be marked in red. The `plot` argument will be `TRUE` by default.
-   Others...

``` r
interpollen(munich, method="lineal", plot = TRUE)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-13-1.png)

As you can see above, **the missing gaps which have been completed by the algorithm are marked in red.** It displays similar graphs for each pollen/spore type within your database. Furthermore, the **"Interpolated" data.frame has been created in your global environment** containing all your data in "long" format and another new column indicating if each data has been interpolated or not (1 means interpolated and 0 means original data).

``` r
head(Interpolated)
```

    ##         Date     Type Pollen Interpolated
    ## 1 2010-02-24    Alnus      0            0
    ## 2 2010-02-24   Betula      0            0
    ## 3 2010-02-24    Taxus      0            0
    ## 4 2010-02-24 Fraxinus      0            0
    ## 5 2010-02-24  Poaceae      0            0
    ## 6 2010-02-24  Quercus      0            0

If you want to integrate all the new data in your database, **you can assign the function to a new object**:

``` r
CompleteData<-interpollen(munich, method="lineal", plot = FALSE)
```

Now your database without gaps appears as "CompleteData" dataframe in your global environment.

**As you might have noticed, if you add `plot=FALSE`, the graphs are not shown.**

You don't have to do this before applying another function of this package. **<font size="3" face="verdana">"interpollen"</font> function has been integrated in the main functions of the package** so you can choose whether you want your data to be completed or not by a specific argument in each function.E.g.:

``` r
calculate_ps(munich, method="percentage", interpolation=TRUE, int.method = "lineal")
```

**FREQUENT ERROR:** Sometimes when applying interpollen you receive the following error: **"Error in rep(NA, ncasos): invalid 'times' argument"**. This is due to a mistake in your database: **you have some dates repeated or disorganised**. The algorithm of `interpollen()` searches for correlative dates and, when they are not consecutive natural days, it carries out the interpolation. When the second date is an earlier date than the previous one, it reports an error. You have to correct these issues in your database before applying the interpollen function.

Methods of interpolation
------------------------

In this function there are **5 different methods to interpolate missing gaps**. Each method can be more appropriate than the others under some specific circumstances. Interpolated data are not real data, but including such estimations can reduce the error of some calculations such as the main pollen season by percentage or a pollen calendar, i.e.: if you keep the gap, these days would count as 0 for the calculations and this would suppose a bigger error than estimating pollen concentrations for these days. Obviously, this will also suppose a bigger error than having the real data.

### <font face="verdana">"lineal"</font> method

As mentioned above, there are many methods to interpolate your missing data. The simplest one was the showed in the previous example: "lineal". **It traces a straight line between each extreme of the gap.** The pollen/spore concentration of the missing days is calculated according to this line. This method may be appropriate for dates without pollen/spores or for small gaps.

**Nevertheless, there are other method which can be more effective during the pollen season.**

### <font face="verdana">"movingmean"</font> method

This method calculates the moving mean of the pollen daily concentrations. The gaps are fulfilled by **calculating the moving mean of the concentrations for this particular day.** The window of the moving mean is centered on this day and its size is the result of multiplicating the gap size by the **`factor` argument**. E.g.: If you have a gap of 3 days and your factor is 2, the gaps are replaced by the value of a moving mean of 6 days (3 x 2) centered in this particular day and not taking into account the missing days for the mean value. Furthermore, it is a dynamic function: **for each gap of the database the window size of the moving mean changes depending of each gap size.**

``` r
interpollen(munich, method="movingmean", factor = 2, plot = TRUE)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-17-1.png)

### <font face="verdana">"spline"</font> method

This method carries out the interpolation by tracing a **polinomic function to link both extremes of the gap.** The polinomic function is chosen according to the best fitting equation to the previous and posterior days of the gap (second, third, fourth degree...). The number of days of each side of the gap which will be taken into account for calculating the spline regression are specified by **`ndays` argument**. The smoothness of the adjustment can be specified by the **`spar` argument**.

``` r
interpollen(munich, method="spline", ndays=3, spar=0.7, plot = TRUE)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-18-1.png)

**Note**: By changing the `ndays` and `spar` argument, very different results can be obtained. E.g.:

``` r
interpollen(munich, method="spline", ndays=5, spar=0.2, plot = TRUE)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-19-1.png)

### <font face="verdana">"tseries"</font> method

If you have long time series of data, this might be the most suitable method for you. This method **analyses the time series of the pollen/spore database and performs a seasonal-trend decomposition based on LOESS** ([Cleveland et al., 1990](http://www.nniiem.ru/file/news/2016/stl-statistical-model.pdf)). It extracts the **seasonality of the historical database** and uses it to predict the missing data by performing a linear regression with the target year.

``` r
interpollen(munich, method="tseries", plot = TRUE)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-20-1.png)

**Seasonality is represented in grey, real data in black and interpolated data in red.** This method improves with long time series of years. Strange results may be caused by lack of years or small sampling periods.

### <font face="verdana">"neighbour"</font> method

**Other near stations provided by the user are used to interpolate the missing data of the target station.** First of all, a Spearman correlation is performed between the target station and the neighbour stations to discard the neighbour stations with a correlation coefficient smaller than **`mincorr` value**. For each gap, a linear regression is performed between the neighbour stations and the target stations to determine the equation which converts the pollen concentrations of the neighbour stations into the pollen concentration of the target station. Only neighbour stations without any missing data during the gap period are taken into account for each gap.

<p class="comment">
You can include 4 different databases of neighbour stations with the arguments **`data2`, `data3`, `data4` and `data5`.** **The format of these databases must be the same than the original one and the name of the pollen/spore types must be exactly the same.**
</p>
With the **`mincorr` argument** you can specify the minimal correlation coefficient (Spearman correlation) that the neighbour stations must have with the target station to be taken into account for a concrete gap. This process is completely independent for each gap of each pollen type.

Function <font face="verdana">calculate\_ps()</font>
====================================================

The function **<font size="3" face="verdana">calculate\_ps()</font>** is one of the core functions of the AeRobiology package. It was designed to **calculate the main parameters of the pollen season** with regard to phenology and pollen intensity from a historical database of several pollen types. The function can use the most common methods to define the main pollen season.

**Please, be patient:** This function has tons of arguments. Some of them are only useful for specific situations. You can use the function without knowing all of them. Nevertheless, **we decided to include all the possible arguments in order to provide specific tools for the advanced users**.

**This function has several arguments, but don't worry, we are going to perform examples later**. The main arguments are:

-   **data**: A `data.frame` object including the general database where calculation of the pollen season must be applied. This data.frame must include a first column in "Date" format and the rest of columns in "numeric" format belonging to each pollen type by column.
-   **method**: A `character` string specifying the method applied to calculate the pollen season and the main parameters. The implemented methods that can be used are: "percentage", "logistic", "moving", "clinical" or "grains". A more detailed information about the different methods for defining the pollen season may be consulted [here](https://cran.r-project.org/web/packages/AeRobiology/AeRobiology.pdf)) or later in this same document.
-   **th.day**: A `numeric` value. The number of days whose pollen concentration is bigger than this threshold is calculated for each year and pollen type. This value will be obtained in the results of the function. The `th.day` argument will be 100 by default.
-   **perc**: A `numeric` value ranging 0\_100. This argument is valid only for `method = "percentage"`. This value represents the percentage of the total annual pollen included in the pollen season, removing (100\_perc)/2% of the total pollen before and after of the pollen season. The `perc` argument will be 95 by default.
-   **def.season**: A `character` string specifying the method for selecting the best annual period to calculate the pollen season. The pollen seasons may occur within the natural year between two years. The implemented options that can be used are: "natural", "interannual" or "peak". The `def.season` argument will be "natural" by default. A more detailed information about the different methods for selecting the best annual period to calculate the pollen season may be consulted [here](https://cran.r-project.org/web/packages/AeRobiology/AeRobiology.pdf).
-   **reduction**: A `logical` value. This argument is valid only for the "logistic" method. If `FALSE` the reduction of the pollen data is not applicable. If `TRUE` a reduction of the peaks above a certain level (`red.level` argument) will be carried out before the definition of the pollen season. The reduction argument will be `FALSE` by default. A more detailed information about the reduction process may be consulted [here](https://cran.r-project.org/web/packages/AeRobiology/AeRobiology.pdf).
-   **red.level**: A `numeric` value ranging 0\_1 specifying the percentile used as level to reduce the peaks of the pollen series before the definition of the pollen season. This argument is valid only for the "logistic" method. The `red.level` argument will be 0.90 by default, specifying the percentile 90.
-   **derivative**: A `numeric (integer)` value belonging to options of 4, 5 or 6 specifying the derivative that will be applied to calculate the asymptotes which determines the pollen season using the "logistic" method. This argument is valid only for the "logistic" method. The `derivative` argument will be 5 by default. More information may be consulted [here](https://cran.r-project.org/web/packages/AeRobiology/AeRobiology.pdf).
-   **man**: A `numeric (integer)` value specifying the order of the moving average applied to calculate the pollen season using the "moving" method. This argument is valid only for the "moving" method. The `man` argument will be 11 by default.
-   **th.ma**: A `numeric` value specifying the threshold used for the "moving" method for defining the beginning and the end of the pollen season. This argument is valid only for the "moving" method. The `th.ma` argument will be 5 by default.
-   **n.clinical**: A `numeric (integer)` value specifying the number of days which must exceed a given threshold (th.pollen argument) for defining the beginning and the end of the pollen season. This argument is valid only for the "clinical" method. The `n.clinical` argument will be 5 by default.
-   **window.clinical**: A `numeric (integer)` value specifying the time window during which the conditions must be evaluated for defining the beginning and the end of the pollen season using the "clinical" method. This argument is valid only for the "clinical" method. The `window.clinical` argument will be 7 by default.
-   **window.grains**: A `numeric (integer)` value specifying the time window during which the conditions must be evaluated for defining the beginning and the end of the pollen season using the "grains" method. This argument is valid only for the "grains" method. The `window.grains` argument will be 5 by default.
-   **th.pollen**: A `numeric` value specifying the threshold that must be exceeded during a given number of days (n.clinical or window.grains arguments) for defining the beginning and the end of the pollen season using the "clinical" or "grains" methods. This argument is valid only for the "clinical" or "grains" methods. The `th.pollen` argument will be 10 by default.
-   **th.sum**: A `numeric` value specifying the pollen threshold that must be exceeded by the sum of daily pollen during a given number of days (`n.clinical` argument) exceeding a given daily threshold (`th.pollen` argument) for defining the beginning and the end of the pollen season using the "clinical" method. This argument is valid only for the "clinical" method. The `th.sum` argument will be 100 by default.
-   **type**: A `character` string specifying the parameters considered according to a specific pollen type for calculating the pollen season using the "clinical" method. The implemented pollen types that may be used are: "birch", "grasses", "cypress", "olive" or "ragweed". As result for selecting any of these pollen types the parameters `n.clinical`, `window.clinical`, `th.pollen` and `th.sum` will be automatically adjusted for the "clinical" method. If no pollen types are specified (`type = "none"`), these parameters will be considered by the user. This argument is valid only for the "clinical" method. The `type` argument will be "none" by default.
-   **interpolation**: A `logical` value. If `FALSE` the interpolation of the pollen data is not applicable. If `TRUE` an interpolation of the pollen series will be applied to complete the gaps with no data before the calculation of the pollen season. The `interpolation` argument will be `TRUE` by default. A more detailed information about the interpolation method may be consulted [here](https://cran.r-project.org/web/packages/AeRobiology/AeRobiology.pdf).
-   **int.method**: A `character` string specifying the method selected to apply the interpolation method in order to complete the pollen series. The implemented methods that may be used are: "lineal", "movingmean", "spline" or "tseries". The `int.method` argument will be "lineal" by default.
-   **maxdays**: A `numeric` (integer value) specifying the maximum number of consecutive days with missing data that the algorithm is going to interpolate. If the gap is bigger than the argument value, the gap will not be interpolated. Not valid with `int.method = "tseries"`. The `maxdays` argument will be 30 by default.
-   **plot**: A `logical` value specifying if a set of plots based on the definition of the pollen season and saved in the working directory will be required or not. If `FALSE` graphical results will not be saved. If `TRUE` a pdf file for each pollen type showing graphically the definition of the pollen season for each studied year will be saved within the plot\_AeRobiology directory created in the working directory. The `plot` argument will be `FALSE` by default.
-   **export.result**: A `logical` value specifying if a excel file including all parameters for the definition of the pollen season saved in the working directory will be required or not. If `FALSE` the results will not exported. If `TRUE` the results will be exported as xlsx file including all parameters calculated from the definition of the pollen season within the table\_AeRobiology directory created in the working directory. The `export.result` argument will be `TRUE` by default.

**IMPORTANT :** if `plot=TRUE`, the plots are **EXPORTED** in "plot\_AeRobiology" folder, but the plots **ARE NOT DISPLAYED IN R**

Methods of calculating the Main Pollen Season
---------------------------------------------

This function allows to calculate the pollen season using **five different methods** which are described below. After calculating the **start\_date**, **end\_date** and **peak\_date** for the pollen season all rest of **parameters** are calculated:

-   **type**: pollen type
-   **seasons**: year of the beginning of the season
-   **st.dt**: start\_date (date)
-   **st.jd**: start\_date (day of the year)
-   **en.dt**: end\_date (date)
-   **en.jd**: end\_date (day of the year)
-   **ln.ps**: length of the season
-   **sm.tt**: total sum
-   **sm.ps**: pollen integral
-   **pk.val**: peak value
-   **pk.dt**: peak\_date (date)
-   **pk.jd**: peak\_date (day of year)
-   **ln.prpk**: length of the pre\_peak period
-   **sm.prpk**: pollen integral of the pre\_peak period
-   **ln.pspk**: length of the post\_peak period
-   **sm.pspk**: pollen integral of the post\_peak period
-   **daysth**: number of days with more than 100 pollen grains
-   **st.dt.hs**: start\_date of the High pollen season (date, only for clinical method)
-   **st.jd.hs**: start\_date of the High pollen season (day of the year, only for clinical method)
-   **en.dt.hs**: end\_date of the High pollen season (date, only for clinical method)
-   **en.jd.hs**: end\_date of the High pollen season (day of the year, only for clinical method)

**IMPORTANT :** All these parameters are recorded in a list object in your R session. Furthermore, if `export.result=TRUE`, they will be exported as xlsx file within the "table\_AeRobiology" directory created in your working directory. This Excel file will have a sheet for each pollen type and a last sheet with the legend of all the abbreviations and the method selected to calculate them. **We strongly recommend to use `export.result=TRUE`**

Although the results can be extracted in the working directory in a xlsx file, **they can also be assigned to an object, and visualized**, for example (an extract):

``` r
pollen_season <- calculate_ps(munich)
```

``` r
kable(pollen_season[24:31, ])
```

|     | type     |  seasons| st.dt      |  st.jd| en.dt      |  en.jd|  ln.ps|   sm.tt|   sm.ps|  pk.val| pk.dt      |  pk.jd|  ln.prpk|  sm.prpk|  ln.pspk|  sm.pspk|  daysth|
|-----|:---------|--------:|:-----------|------:|:-----------|------:|------:|-------:|-------:|-------:|:-----------|------:|--------:|--------:|--------:|--------:|-------:|
| 24  | Fraxinus |     2015| 2015-03-28 |     87| 2015-04-21 |    111|     25|  4762.5|  4538.5|   910.5| 2015-04-13 |    103|       17|     2960|        8|   1578.5|       8|
| 25  | Poaceae  |     2010| 2010-05-18 |    138| 2010-08-25 |    237|    100|  1861.0|  1773.0|   179.0| 2010-06-11 |    162|       25|      705|       75|   1068.0|       4|
| 26  | Poaceae  |     2011| 2011-05-02 |    122| 2011-08-24 |    236|    115|  2244.0|  2134.0|   163.0| 2011-05-25 |    145|       24|      588|       91|   1546.0|       2|
| 27  | Poaceae  |     2012| 2012-05-07 |    128| 2012-08-17 |    230|    103|  2127.0|  2023.0|    84.0| 2012-05-25 |    146|       19|      349|       84|   1674.0|       0|
| 28  | Poaceae  |     2013| 2013-05-18 |    138| 2013-09-13 |    256|    119|  2481.0|  2367.0|   166.0| 2013-06-13 |    164|       27|      773|       92|   1594.0|       4|
| 29  | Poaceae  |     2014| 2014-05-06 |    126| 2014-08-09 |    221|     96|  2413.0|  2306.0|    97.0| 2014-06-11 |    162|       37|     1218|       59|   1088.0|       0|
| 30  | Poaceae  |     2015| 2015-05-12 |    132| 2015-08-10 |    222|     91|  4175.0|  3998.0|   322.0| 2015-06-04 |    155|       24|     2089|       67|   1909.0|       9|
| 31  | Quercus  |     2010| 2010-04-08 |     98| 2010-05-26 |    146|     49|   737.5|   701.5|   189.0| 2010-04-30 |    120|       23|      416|       26|    285.5|       1|

### "percentage" method

This is a commonly used method for defining the pollen season based on the **elimination of a certain percentage in the beginning and the end of the pollen season** ( [Nilsson and Persson, 1981](https://www.tandfonline.com/doi/abs/10.1080/00173138109427661); [Andersen, 1991](https://www.tandfonline.com/doi/pdf/10.1080/00173139109427810)). For example, if the pollen season is based on the 95% of the total annual pollen (`perc = 95`), the start\_date of the pollen season is marked as the day in which 2.5% of the total pollen is registered and the end\_date of the pollen season is marked as the day in which 97.5% of the total pollen is registered.

``` r
calculate_ps(munich, method = "percentage", perc = 90, export.result = TRUE)
```

In this case we have calculated the main pollen season based on the 90% of the total annual pollen. **Results are stored in the "table\_AeRobiology" folder since `export.result=TRUE`**.

You can select different methods of interpolation or even to not interpolate gaps:

``` r
calculate_ps(munich, method = "percentage", perc = 90, export.result = TRUE, int.method = "spline")
```

``` r
calculate_ps(munich, method = "percentage", perc = 75, export.result = TRUE, interpolation = FALSE)
```

**IMPORTANT :** if `plot=TRUE`, the plots are **EXPORTED** in "plot\_AeRobiology" folder, but the plots **ARE NOT DISPLAYED IN R**

### "logistic" method

This method was developed by Ribeiro et al. (2007) and modified by Cunha et al. (2015). It is based on fitting annually a **non-linear logistic regression model** to the daily accumulated curve for each pollen type. This logistic function and the **different derivatives** were considered to calculate the **start\_date** and **end\_date** of the pollen season, based on the **asymptotes when pollen amounts are stabilized on the beginning and the end of the accumulated curve**. For more information about the method, see [Ribeiro et al. (2007)](https://www.ncbi.nlm.nih.gov/pubmed/18247462) and [Cunha et al. (2015)](https://link.springer.com/article/10.1007%2Fs10453-014-9345-3). **Three different derivatives** may be used (`derivative` argument) 4, 5 or 6 that represent **from higher to lower restrictive criterion** for defining the pollen season.

This method may be **complemented with an optional method for reduction the peaks values** (`reduction = TRUE`), thus avoiding the effect of the great influence of extreme peaks. In this sense, **peaks values will be cut below a certain level** that the user may select based on a percentile analysis of peaks. For example, `red.level = 0.90` will cut all peaks above the percentile 90.

``` r
pollen_season<-calculate_ps(munich, method = "logistic", derivative = 5, reduction=FALSE)
```

``` r
kable(pollen_season[24:31, ])
```

|     | type     |  seasons| st.dt      |  st.jd| en.dt      |  en.jd|  ln.ps|   sm.tt|   sm.ps|  pk.val| pk.dt      |  pk.jd|  ln.prpk|  sm.prpk|  ln.pspk|  sm.pspk|  daysth|
|-----|:---------|--------:|:-----------|------:|:-----------|------:|------:|-------:|-------:|-------:|:-----------|------:|--------:|--------:|--------:|--------:|-------:|
| 24  | Fraxinus |     2015| 2015-04-07 |     97| 2015-04-18 |    108|     12|  4762.5|  4360.5|   910.5| 2015-04-13 |    103|        7|     2844|        5|   1516.5|       8|
| 25  | Poaceae  |     2010| 2010-05-16 |    136| 2010-07-22 |    203|     68|  1861.0|  1666.0|   179.0| 2010-06-11 |    162|       27|      724|       41|    942.0|       4|
| 26  | Poaceae  |     2011| 2011-04-27 |    117| 2011-07-16 |    197|     81|  2244.0|  1994.0|   163.0| 2011-05-25 |    145|       29|      617|       52|   1377.0|       2|
| 27  | Poaceae  |     2012| 2012-04-30 |    121| 2012-07-23 |    205|     85|  2127.0|  1932.0|    84.0| 2012-05-25 |    146|       26|      390|       59|   1542.0|       0|
| 28  | Poaceae  |     2013| 2013-02-04 |     35| NA         |     NA|     NA|  2481.0|      NA|   166.0| 2013-06-13 |    164|       NA|       NA|       NA|       NA|      NA|
| 29  | Poaceae  |     2014| 2014-05-07 |    127| 2014-07-18 |    199|     73|  2413.0|  2149.0|    97.0| 2014-06-11 |    162|       36|     1206|       37|    943.0|       0|
| 30  | Poaceae  |     2015| 2015-05-08 |    128| 2015-07-08 |    189|     62|  4175.0|  3491.5|   322.0| 2015-06-04 |    155|       28|     2132|       34|   1359.5|       9|
| 31  | Quercus  |     2010| 2010-04-13 |    103| 2010-05-20 |    140|     38|   737.5|   628.8|   189.0| 2010-04-30 |    120|       18|      401|       20|    227.8|       1|

In the previous case, the reduction wasn't carried out (`reduction=FALSE`) and all the peaks were conserved. We can cut some peaks and change the `derivative`:

``` r
pollen_season<-calculate_ps(munich, method = "logistic", derivative = 6, reduction=FALSE, red.level = 0.8)
```

``` r
kable(pollen_season[24:31, ])
```

|     | type     |  seasons| st.dt      |  st.jd| en.dt      |  en.jd|  ln.ps|   sm.tt|   sm.ps|  pk.val| pk.dt      |  pk.jd|  ln.prpk|  sm.prpk|  ln.pspk|  sm.pspk|  daysth|
|-----|:---------|--------:|:-----------|------:|:-----------|------:|------:|-------:|-------:|-------:|:-----------|------:|--------:|--------:|--------:|--------:|-------:|
| 24  | Fraxinus |     2015| 2015-04-05 |     95| 2015-04-19 |    109|     15|  4762.5|  4401.5|   910.5| 2015-04-13 |    103|        9|     2864|        6|   1537.5|       8|
| 25  | Poaceae  |     2010| 2010-05-08 |    128| 2010-07-30 |    211|     84|  1861.0|  1731.0|   179.0| 2010-06-11 |    162|       35|      748|       49|    983.0|       4|
| 26  | Poaceae  |     2011| 2011-04-17 |    107| 2011-07-26 |    207|    101|  2244.0|  2073.0|   163.0| 2011-05-25 |    145|       39|      640|       62|   1433.0|       2|
| 27  | Poaceae  |     2012| 2012-04-19 |    110| 2012-08-03 |    216|    107|  2127.0|  2035.0|    84.0| 2012-05-25 |    146|       37|      400|       70|   1635.0|       0|
| 28  | Poaceae  |     2013| 2013-02-06 |     37| NA         |     NA|     NA|  2481.0|      NA|   166.0| 2013-06-13 |    164|       NA|       NA|       NA|       NA|      NA|
| 29  | Poaceae  |     2014| 2014-04-28 |    118| 2014-07-27 |    208|     91|  2413.0|  2270.0|    97.0| 2014-06-11 |    162|       45|     1240|       46|   1030.0|       0|
| 30  | Poaceae  |     2015| 2015-05-01 |    121| 2015-07-15 |    196|     76|  4175.0|  3678.0|   322.0| 2015-06-04 |    155|       35|     2158|       41|   1520.0|       9|
| 31  | Quercus  |     2010| 2010-04-09 |     99| 2010-05-25 |    145|     47|   737.5|   683.5|   189.0| 2010-04-30 |    120|       22|      411|       25|    272.5|       1|

As you can observe, results are a bit different.

**IMPORTANT :** if `plot=TRUE`, the plots are **EXPORTED** in "plot\_AeRobiology" folder, but the plots **ARE NOT DISPLAYED IN R**

### "clinical" method

This method was proposed by [Pfaar et al. (2017)](https://www.ncbi.nlm.nih.gov/pubmed/27874202). It is based on the expert consensus in relation to pollen exposure and the **relationship with allergic symptoms** derived of the literature.

Different periods may be defined by this method: the **pollen season**, the **high pollen season** and the **high pollen days**:

1.  The **start\_date** and **end\_date** of the **pollen season** were defined as a **certain number of days (`n.clinical` argument) within a time window period (`window.clinical` argument) exceeding a certain pollen threshold (`th.pollen` argument) which summation is above a certain pollen sum (`th.sum` argument)**. <br> All these parameters are established for each pollen type according to Pfaar et al. (2017) and using the **`type`** argument these parameters may be automatically adjusted for the specific pollen types ("birch", "grasses", "cypress", "olive" or "ragweed"). Furthermore, **the user may change all parameters to do a customized definition of the pollen season**.

2.  The **start\_date** and **end\_date** of the **high pollen season** were defined as **three consecutive days exceeding a certain pollen threshold (`th.day` argument)**.

3.  The number of **high pollen days** will also be calculated exceeding this pollen threshold (`th.day`). For more information about the method, see [Pfaar et al. (2017)](https://www.ncbi.nlm.nih.gov/pubmed/27874202).

Running the following example, the main pollen season will be established according to the birch requirements: more than 5 days within a week in which more than 10 pollen grains/m3 are registered and whose sum exceeds 100 pollen grains/m3. The high pollen days are those which exceed 100 pollen grains/m3 (`n.clinical=5, window.clinical=7, th.pollen=10, th.sum=100, th.day=100`)

``` r
pollen_season<-calculate_ps(munich, method = "clinical", type = "birch")
```

``` r
kable(pollen_season[24:31, ])
```

|     | type     |  seasons| st.dt      |  st.jd| en.dt      |  en.jd|  ln.ps|   sm.tt|   sm.ps|  pk.val| pk.dt      |  pk.jd|  ln.prpk|  sm.prpk|  ln.pspk|  sm.pspk|  daysth| st.dt.hs   |  st.jd.hs| en.dt.hs   |  en.jd.hs|
|-----|:---------|--------:|:-----------|------:|:-----------|------:|------:|-------:|-------:|-------:|:-----------|------:|--------:|--------:|--------:|--------:|-------:|:-----------|---------:|:-----------|---------:|
| 24  | Fraxinus |     2015| 2015-03-24 |     83| 2015-04-25 |    115|     33|  4762.5|  4738.5|   910.5| 2015-04-13 |    103|       21|     3066|       12|   1672.5|       8| 2015-04-10 |       100| 2015-04-17 |       107|
| 25  | Poaceae  |     2010| 2010-06-04 |    155| 2010-07-15 |    196|     42|  1861.0|  1475.0|   179.0| 2010-06-11 |    162|        8|      598|       34|    877.0|       4| 2010-06-10 |       161| 2010-06-13 |       164|
| 26  | Poaceae  |     2011| 2011-05-17 |    137| 2011-07-04 |    185|     49|  2244.0|  1673.0|   163.0| 2011-05-25 |    145|        9|      436|       40|   1237.0|       2| NA         |        NA| NA         |        NA|
| 27  | Poaceae  |     2012| 2012-05-19 |    140| 2012-07-19 |    201|     62|  2127.0|  1799.0|    84.0| 2012-05-25 |    146|        7|      284|       55|   1515.0|       0| NA         |        NA| NA         |        NA|
| 28  | Poaceae  |     2013| 2013-06-05 |    156| 2013-07-27 |    208|     53|  2481.0|  1910.0|   166.0| 2013-06-13 |    164|        9|      708|       44|   1202.0|       4| NA         |        NA| NA         |        NA|
| 29  | Poaceae  |     2014| 2014-05-19 |    139| 2014-07-07 |    188|     50|  2413.0|  1948.0|    97.0| 2014-06-11 |    162|       24|     1107|       26|    841.0|       0| NA         |        NA| NA         |        NA|
| 30  | Poaceae  |     2015| 2015-05-07 |    127| 2015-07-27 |    208|     82|  4175.0|  3893.0|   322.0| 2015-06-04 |    155|       29|     2144|       53|   1749.0|       9| 2015-05-29 |       149| 2015-06-06 |       157|
| 31  | Quercus  |     2010| 2010-04-25 |    115| 2010-05-11 |    131|     17|   737.5|   529.0|   189.0| 2010-04-30 |    120|        6|      349|       11|    180.0|       1| NA         |        NA| NA         |        NA|

The code above returns the same result than:

``` r
pollen_season<-calculate_ps(munich, method = "clinical", n.clinical = 5, window.clinical = 7, th.pollen = 10, th.sum = 100, th.day = 100)
```

``` r
kable(pollen_season[24:31, ])
```

|     | type     |  seasons| st.dt      |  st.jd| en.dt      |  en.jd|  ln.ps|   sm.tt|   sm.ps|  pk.val| pk.dt      |  pk.jd|  ln.prpk|  sm.prpk|  ln.pspk|  sm.pspk|  daysth| st.dt.hs   |  st.jd.hs| en.dt.hs   |  en.jd.hs|
|-----|:---------|--------:|:-----------|------:|:-----------|------:|------:|-------:|-------:|-------:|:-----------|------:|--------:|--------:|--------:|--------:|-------:|:-----------|---------:|:-----------|---------:|
| 24  | Fraxinus |     2015| 2015-03-24 |     83| 2015-04-25 |    115|     33|  4762.5|  4738.5|   910.5| 2015-04-13 |    103|       21|     3066|       12|   1672.5|       8| 2015-04-10 |       100| 2015-04-17 |       107|
| 25  | Poaceae  |     2010| 2010-06-04 |    155| 2010-07-15 |    196|     42|  1861.0|  1475.0|   179.0| 2010-06-11 |    162|        8|      598|       34|    877.0|       4| 2010-06-10 |       161| 2010-06-13 |       164|
| 26  | Poaceae  |     2011| 2011-05-17 |    137| 2011-07-04 |    185|     49|  2244.0|  1673.0|   163.0| 2011-05-25 |    145|        9|      436|       40|   1237.0|       2| NA         |        NA| NA         |        NA|
| 27  | Poaceae  |     2012| 2012-05-19 |    140| 2012-07-19 |    201|     62|  2127.0|  1799.0|    84.0| 2012-05-25 |    146|        7|      284|       55|   1515.0|       0| NA         |        NA| NA         |        NA|
| 28  | Poaceae  |     2013| 2013-06-05 |    156| 2013-07-27 |    208|     53|  2481.0|  1910.0|   166.0| 2013-06-13 |    164|        9|      708|       44|   1202.0|       4| NA         |        NA| NA         |        NA|
| 29  | Poaceae  |     2014| 2014-05-19 |    139| 2014-07-07 |    188|     50|  2413.0|  1948.0|    97.0| 2014-06-11 |    162|       24|     1107|       26|    841.0|       0| NA         |        NA| NA         |        NA|
| 30  | Poaceae  |     2015| 2015-05-07 |    127| 2015-07-27 |    208|     82|  4175.0|  3893.0|   322.0| 2015-06-04 |    155|       29|     2144|       53|   1749.0|       9| 2015-05-29 |       149| 2015-06-06 |       157|
| 31  | Quercus  |     2010| 2010-04-25 |    115| 2010-05-11 |    131|     17|   737.5|   529.0|   189.0| 2010-04-30 |    120|        6|      349|       11|    180.0|       1| NA         |        NA| NA         |        NA|

We have resumed all these parameters under the argument `type=birch` to facilitate its application.

**IMPORTANT :** if **`plot=TRUE`**, the plots are **EXPORTED** in "plot\_AeRobiology" folder, but the plots **ARE NOT DISPLAYED IN R**

### "grains" method

This method was proposed by Galan et al. (2001) originally in olive pollen but after also applied in other pollen types.

The **start\_date** and **end\_date** of the pollen season were defined as a **certain number of days (`window.grains` argument) exceeding a certain pollen threshold (`th.pollen` argument)**. For more information about the method, see [Galan et al. (2001)](https://link.springer.com/article/10.1007/s004840000081).

We want to establish the main pollen season start in the first day when more than 3 consecutive days have more than 2 pollen grains/m3, and the end in the last day in which these conditions are fulfilled:

``` r
calculate_ps(munich, method = "grains", window.grains = 3, th.pollen = 2 )
```

### "moving" method

**This method is proposed the first time by the authors of this package. We are developing a research paper explaining the method in detail.**

The definition of the pollen season is based on the application of a **moving average** to the pollen series in order to obtain the general seasonality of the pollen curve avoiding the great variability of the daily fluctuations. Thus, **the start\_date and the end\_date will be established when the curve of the moving average reaches a given pollen threshold** (**`th.ma`** argument). Also the order of the moving average may be customized by the user (**`man`** argument). By default, `man = 11` and `th.ma = 5`.

<p class="comment">
The idea of this method is to be able to calculate the start of the main pollen season when the season has not finished yet. Moreover, it allows to establish the start and end even if there is a huge amount of lacking data within the main pollen season. Its similar to the "grains" method but taking into account the moving mean to avoid daily variability.
</p>
You might understand it better by consulting the plots obtained.

**IMPORTANT :** if **`plot=TRUE`**, the plots are **EXPORTED** in "plot\_AeRobiology" folder, but the plots **ARE NOT DISPLAYED IN R**

Let's calculate it with a moving average of 7 days. We are going to establish the start and end of the main pollen season when the moving average reaches 4 pollen grains/m3:

``` r
calculate_ps(munich, method = "moving", man = 7, th.ma = 4)
```

Advanced examples
-----------------

### Souther Hemisphere & interannual types

As you may have noticed, we have been using the function under an "European" point of view: the calculations are from 1st January to 31th December. The researchers of the **Southern Hemisphere** are used to work with **interanual pollen seasons**. Don't worry, we haven't forgotten you!

1.  You can work **from 1st June to 31th May** by means of the argument **`def.season="interannual"`**:

``` r
calculate_ps(munich, method = "moving", man = 7, th.ma = 4, def.season = "interannual")
```

<p class="comment">
In this method, the season belongs to the first year of the pair of years, i.e: from June 2017 to May 2018 -&gt; season "2017".
</p>
1.  You can **center the main pollen season in the average peak day** (182 days before and after the average date of the peak):

``` r
calculate_ps(munich, method = "percentage", perc=95, def.season = "peak")
```

<p class="comment">
In this last method, the season belongs to the year in which the average peak date - 182 days is located, i.e.: if the average peak date is in January 2013, the season is called "2012" in the data.frames.
</p>
### Interpolation in detail

Pollen time series frequently have different gaps with no data and this fact could be a problem for the calculation of specific methods for defining the pollen season even providing incorrect results. In this sense **by default a linear interpolation will be carried out to complete these gaps before to define the pollen season (`interpolation = TRUE`)**. Additionally, the users may select other interpolation methods using the **`int.method` argument**, as "lineal", "movingmean", "spline" or "tseries".

Some advanced users may have noticed that you can't use directly all the arguments of **<font size="3"" face="verdana">interpollen()</font>** through **<font size="3"" face="verdana">calculate\_ps()</font>**. You only are able to select the interpolation method. Nevertheless, it is not impossible to use them:

1.  **Use <font size="3"" face="verdana">interpollen()</font>** with all the arguments you want and **overwrite your interpolated database in an object**:

``` r
CompleteData<-interpollen(munich, method="spline", ndays=3, spar=0.7, plot = TRUE, maxdays = 3)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-38-1.png)

1.  Then, **use "CompleteData" instead of "munich" (or your database) in the following steps**:

``` r
ps_plot(CompleteData, pollen.type="Alnus", year=2013, fill.col = "orange", axisname = "AeRobiology custom units")
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-39-1.png)

<p class="comment">
**Note**: You might select **`interpollation=FALSE`** in the other functions you use after interpolating manually. In some cases it doesn't matter, but if you want to interpolate only gaps with less than 3 days and you did in the first step, if you don't use `interpolation=FALSE` in calculate\_ps function, a second interpolation will be carried out by using the default method (gaps with less than 30 days and lineal interpolation). You will obtain a database with the gaps of less than 3 days interpolated by "spline" method and the rest with the "lineal" method.
</p>
Function <font face="verdana">pollen\_calendar</font>
=====================================================

This function **calculates the pollen calendar from a historical database** of several pollen types using different designs. The main arguments are:

-   **data**: A `data.frame` with the first column in "Date" format and the rest of the columns in "numeric" format (pollen types).
-   **method**: for choosing the method to generate the pollen calendar. The options are "heatplot", "violinplot" and "phenological".
-   **n.types**: indicating the number of the most abundant pollen types showed in the pollen calendar
-   **start.month**: ranging 1-12 indicates the number of the month (January-December) when the beginning of the pollen calendar must be considered, see more details [here](https://cran.r-project.org/web/packages/AeRobiology/AeRobiology.pdf).
-   **export.plot**: specifying if a plot with the pollen calendar saved in the working directory will be required or not. Other arguments have been incorporated in relation to the format to export the plot (`export.format`).

**Data exportation:**

-   If `export.plot = TRUE` this plot displaying the pollen calendar will be exported within the **Plot\_AeRobiology** directory created in the working directory.
-   If `export.plot = TRUE` and `export.format = pdf`, a pdf file with the pollen calendar will be saved within the **plot\_AeRobiology** directory, created in the working directory. Additional characteristics may be incorporated to the exportation as pdf file.
-   If `export.plot = TRUE` and `export.format = png`, a png file with the pollen calendar will be saved within the **plot\_AeRobiology** directory, created in the working directory. Additional characteristics may be incorporated to the exportation as png file.

**Exclusive arguments** for pollen calendars generated as **"heatplot"**:

-   **period**: specifying the interval time considered to generate the pollen calendar. The options are "weekly" and "daily."
-   **method.classes**: indicating the method to define the classes used for classifying the average pollen concentrations. The options are "exponential" and "custom".
-   **n.classes**: specifying the number of classes that will be used when `method.classes = custom`.
-   **classes**: specifying a numeric vector with the desired thresholds to define the different classes.
-   **color**: choosing different options such as "green", "red", "blue", "purple"or "black".

**Exclusive arguments** for pollen calendars generated as **"phenological"**:

-   **perc1, perc2**: both ranging 0-100. These values represent the percentage of the total annual pollen included in the pollen season, removing (100-percentage)/2% of the total pollen before and after the pollen season. Two percentages must be specified to define the "main pollination period" (`perc1`) and the "early/late pollination" (`perc2`) based on the "phenological" method proposed by [Werchan et al. (2018)](https://link.springer.com/article/10.1007/s40629-018-0055-1).
-   **th.pollen**: specifying the minimum threshold of the average pollen concentration which will be used to generate the pollen calendar. Days below this threshold will not be considered and days above this threshold will be considered as "possible occurrence" as proposed by [Werchan et al. (2018)](https://link.springer.com/article/10.1007/s40629-018-0055-1). This argument also works for "violinplot" pollen calendar

By default the database will be interpolated according to the **<font size="3" face="verdana">interpollen</font>** function, and also other arguments for this function are incorporated in the **<font size="3" face="verdana">pollen\_calendar</font>** function.

"heatplot" method
-----------------

This pollen calendar is constructed based on the **daily or weekly average of pollen concentrations** (depending on the preferences of the user, who may select "daily" or "weekly" as **`period` argument**). Then, these averages may be classified in different categories following different methods selected by the user. An example of this pollen calendar may be consulted in [Rojo et al. (2016)](https://link.springer.com/article/10.1007/s10661-016-5129-2). This method to design pollen calendars is an **adaptation from the pollen calendar proposed by Spieksma (1991)**, who considered 10-day periods instead of daily or weekly periods.

First, we are going to generate a pollen calendar based on the **heatplot**, designed with **green** color and constructed with **daily** averages:

``` r
pollen_calendar(munich, method = "heatplot", period = "daily", color = "green", interpolation = FALSE)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-40-1.png)

In all cases, the table used by the pollen calendar with the averaged values will be created. This table can be visualized. For example (an extract):

``` r
kable(average_values[82:90, ])
```

|     |   jd|      Alnus|      Betula|     Taxus|     Fraxinus|    Poaceae|    Quercus|      Ulmus|  Urtica|
|-----|----:|----------:|-----------:|---------:|------------:|----------:|----------:|----------:|-------:|
| 82  |   82|  33.166667|    0.000000|  416.1667|    0.1666667|  0.0000000|  0.0000000|   8.333333|       0|
| 83  |   83|  21.000000|    1.833333|  443.4167|   10.5833333|  0.0000000|  0.0000000|  17.666667|       0|
| 84  |   84|  15.000000|    3.333333|  387.7500|   32.5833333|  0.0000000|  0.8333333|  17.500000|       0|
| 85  |   85|   3.800000|    6.333333|  263.5833|   62.5000000|  0.1666667|  0.0000000|  12.166667|       0|
| 86  |   86|  10.400000|   11.833333|  116.2000|    7.3333333|  0.0000000|  1.3333333|   8.000000|       0|
| 87  |   87|   8.400000|   26.666667|   37.3000|   30.0000000|  0.0000000|  1.1666667|   6.000000|       0|
| 88  |   88|   4.800000|   52.833333|  205.7000|   68.6000000|  0.0000000|  0.6666667|  13.416667|       0|
| 89  |   89|   7.000000|  118.333333|  335.2500|  240.2000000|  0.0000000|  2.6666667|  34.750000|       0|
| 90  |   90|   7.333333|  121.200000|  480.1667|  185.6000000|  0.0000000|  1.1666667|  17.500000|       0|

By default the method for defining the classes for the pollen calendar is according to the **exponential** method. Nevertheless, the classes can be customized by **`classes` argument**:

``` r
pollen_calendar(data = munich, method = "heatplot", period = "daily", color = "red", method.classes = "custom", n.classes = 5, classes = c(5, 25, 50, 200), interpolation = FALSE)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-42-1.png)

In addition, for species with their pollen season occurring between two natural years, the start of the pollen calendar can be selected by the **`start.month` argument**:

``` r
pollen_calendar(data = munich, method = "heatplot", period = "daily", color = "purple", method.classes = "custom", n.classes = 5, classes = c(5, 25, 50, 200), start.month = 11, na.remove = FALSE, interpolation = FALSE)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-43-1.png)

<p class="comment">
**`NA` (no data) can be removed by using `na.remove` argument.**
</p>
Also we can generate a pollen calendar based on the **heatplot** design with **weekly** averages.

``` r
pollen_calendar(data = munich, method = "heatplot", period = "weekly", color = "blue", method.classes = "exponential", n.types = 4, y.start = 2011, y.end = 2014, interpolation = FALSE)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-44-1.png)

In this case, we have included other restrictive arguments as **`n.types`** limiting the number of pollen types and **`y.start`** and **`y.end`**, limiting the period to be considered for the pollen calendar.

"phenological" method
---------------------

This pollen calendar is based on the phenological definition of the pollen season and adapted from the methodology proposed by [Werchan et al. (2018)](https://link.springer.com/article/10.1007/s40629-018-0055-1).

After obtaining the **daily average pollen concentrations** for the most abundant pollen types, different pollination periods are calculated. The **main pollination period** is calculated according to the percentage defined by `perc1` argument (selected by the user, 80% by default; red) of the annual total pollen. For example, if `perc1 = 80`, the beginning of the high season is marked when the 10% of the annual value is reached; the end is selected when 90% is reached. The **early/late pollination period** is defined with the `perc2` argument (selected by the user, 99% of the total annual pollen by default; orange), i.e.: the start of this period will be when the 0.5% is reached and the end when the 99.5% is reached. For this kind of pollen calendar the `th.pollen` argument defines the **possible occurrence** period as adapted by [Werchan et al. (2018)](https://link.springer.com/article/10.1007/s40629-018-0055-1), considering the entire period between the first and the last day when this pollen level is reached (yellow).

``` r
pollen_calendar(data = munich, method = "phenological", n.types = 5, y.start = 2011, y.end = 2014, interpolation = FALSE)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-45-1.png)

Furthermore, different criteria can be customized:

``` r
pollen_calendar(data = munich,  method = "phenological", perc1 = 90, perc2 = 95, th.pollen = 5, interpolation = FALSE)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-46-1.png)

In this last case, the pollen calendar has been generated with more restrictive criteria for `perc1`, `perc2` and `th.pollen`.

"violinplot" method
-------------------

This pollen calendar is based on the **pollen intensity**, and adapted from the pollen calendar published by [ORourke (1990)](https://link.springer.com/article/10.1007/BF02539105). At first, the daily averages of the pollen concentrations are calculated and then, these averages are represented by using the violin plot graph.

The shape of the violin plot represents the pollen intensity of the pollen types in a relative way, i.e.: the values will be calculated as **relative measurements** regarding to the most abundant pollen type in annual amounts. Therefore, this pollen calendar shows a relative comparison between the pollen intensity of the pollen types but **without scales and units**.

``` r
pollen_calendar(data = munich, method = "violinplot", y.start = 2012, y.end = 2015, interpolation = FALSE)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-47-1.png)

In addition, **`th.pollen`** can be established, specifying the minimum pollen concentration considered (E.g.: 10 pollen grains/m3):

``` r
pollen_calendar(data = munich, method = "violinplot", th.pollen = 10, na.rm = FALSE, interpolation = FALSE)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-48-1.png)

Functions <font face="verdana">iplot\_pollen()</font> and <font face="verdana">iplot\_years()</font>
====================================================================================================

These functions have been designed for a **quick view** of your data for discussions or interpretations of them. **Interactive plots are displayed**. It might be interesting for group meetings or real time exposition of results. The functions create a emergent screen in which you can select the pollen/spore type or the years you want to plot.

**IMPORTANT :** To stop the real time visualization and continue using the package you must click in the **"Stop" signal.**

The function **<font size="3" face="verdana">iplot\_pollen()</font>** is to plot the pollen data during one season. The arguments are:

-   **data**: A `data.frame` object. This data.frame should include a first column in "Date" format and the rest of columns in format "numeric" belonging to each pollen type by column.
-   **year:** An `integer` value specifying the year to display. This is a mandatory argument.

The function **<font size="3" face="verdana">iplot\_years()</font>** is to plot the pollen data during one season. The arguments are:

-   **data**: A `data.frame` object. This data.frame should include a first column in "Date" format and the rest of columns in format "numeric" belonging to each pollen type by column.
-   **pollen:** A `character` string with the name of the particle to show. This character must match with the name of a column in the input database. This is a mandatory argument.

``` r
iplot_pollen(munich, year = 2012)
iplot_years(munich, pollen = "Betula")
```

<p class="comment">
**Note:** We are not able to plot interactive figures in this document. Please, run the codes above in your R session.
</p>
Function <font face="verdana">summary\_plot()</font>
====================================================

The function **<font size="3" face="verdana">summary\_plot()</font>** is to plot the pollen data during several seasons. Also plots the averaged pollen season over the study period. It is possible to plot the relative abundance per day and smoothing the pollen season by calculating a moving average. The main arguments are:

-   **data**: A `data.frame` object. This data.frame should include a first column in "Date" format and the rest of columns in "numeric" format belonging to each pollen type by column.
-   **pollen:** A `character` string with the name of the particle to show. This character must match with the name of a column in the input database. This is a mandatory argument.
-   **mave:** An `integer` value specifying the order of the moving average applied to the data. By default, `mave = 1`.
-   **normalized:** A `logical` value specifying if the visualization shows real pollen data (`normalized = FALSE`) or the percentage of every day over the whole pollen season (`normalized = TRUE`). By default, `normalized = FALSE`.
-   **axisname:** A `character` string specifying the title of the y axis. By default, `axisname = "Pollen grains / m3"`
-   Others...

``` r
summary_plot(munich, pollen = "Betula")
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-50-1.png)

In some cases the user may want to reduce the "noise" of the daily values by **calculating moving means** (E.g.: 5 days moving mean; `mave = 5`):

``` r
summary_plot(munich, pollen = "Betula", mave = 5)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-51-1.png)

You might be also interested in representing as background the **percentage** each day suppose to the main pollen season (`normalized = TRUE`):

``` r
summary_plot(munich, pollen = "Betula", mave = 5, normalized = TRUE)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-52-1.png)

**<font size="3"" face="verdana">interpollen()</font> function** is integrated in this function. Consult the "help" section of this function to use it.

Function <font face="verdana">summary\_normplot()</font>
========================================================

The function **<font face="verdana">summary\_normplot()</font>** has been designed to plot the pollen data amplitude during several seasons: daily average pollen concentration over the study period, maximum pollen concentration of each day over the study period and minimum pollen concentration of each day value over the study period. It is possible to plot the relative abundance per day and smoothing the pollen season by calculating a moving average. The main arguments similar to **<font size="3" face="verdana">summary\_plot()</font>**, but as a result **you will obtain the the max-min range of the study period** instead of the values for every year.

``` r
summary_normplot(munich, pollen = "Betula", color.plot = "red")
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-53-1.png)

The **maximum values** are marked in **red** and the **minimum values in white**. The **average** is the **black line**.

Of course, you can change the color (`color.plot` argument):

``` r
summary_normplot(munich, pollen = "Betula", color.plot = "green", mave = 5, normalized = TRUE)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-54-1.png)

**<font size="3"" face="verdana">interpollen()</font> function** is integrated in this function. Consult the "help" section of this function to use it.

Function <font face="verdana">trend\_plot()</font>
==================================================

The function **<font size="3" face="verdana">trend\_plot()</font>** has been created to calculate the main seasonal indexes of the pollen season ("Start Date", "Peak Date", "End Date" and "Pollen Integral"), as well as **trends analysis** of those parameters over the seasons. It is a summary dot plot showing the distribution of the main seasonal indexes over the years.

The results are stored in two folders termed **"plot\_AeRobiology"** and **"table\_AeRobiology"**, which will be created in your working directory (only if `export.result=TRUE` & `export.plot=TRUE`).

The function allow you to decide if you want to interpolate the data or not, by the argument **`interpolation`**, it also allows you to select the interpolation method by the argument **`int.method`**. Furthermore, it allows you to select the pollen season definition method, by the argument **`method`** and additional arguments for the function **<font size="3" face="verdana">calculate\_ps()</font>**. Some arguments about the visualization are:

-   **split**: A `logical` argument. If `split = TRUE`, the plot is separated in two according to the nature of the variables (i.e. dates or pollen concentrations). **This argument was a solution to reduce the scale of the x-axis when "total pollen"" variable has a very high/low slope**. By default, `split = TRUE`.
-   **quantil**: A `numeric` value (between 0 and 1) indicating the quantile of data to be displayed in the graphical output of the function. `quantil = 1` would show all the values, however a lower quantile will exclude the most extreme values of the sample. **This argument was designed after realizing of a common problem: when plotting the results with `split=FALSE`, some "outlier" results increased a lot the scale of the x-axis, making unreadable the rest of results**. Our solution was to create an argument to exclude these outliers results and to be able to observe the main results in an appropriate scale. Furthermore, this argument may be used to split the parameters using a different sampling units (e.g. dates and pollen concentrations) can be used low vs high values of quantil argument (e.g. 0.5 vs 1). Also can be used an extra argument: `split`. By default, `quantil = 0.75`. **This argument only works when `split=FALSE`.**
-   **significant**: A `numeric` value indicating the significant level to be considered in the linear trends analysis. This p level is displayed in the graphical output of the function (as a number in the legend and as a black ring in the graphical representation). By default, `significant = 0.05`.

``` r
trend_plot(munich, interpolation = FALSE, export.result = FALSE, export.plot = FALSE)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-55-1.png)![](tutorial_files/figure-markdown_github/unnamed-chunk-55-2.png)

Lets see what happen if we don't split the graphics:

``` r
trend_plot(munich, interpolation = FALSE, export.result = FALSE, export.plot = FALSE, split = FALSE, quantil = 1)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-56-1.png)

Now the results are less readable, but this kind of graphical representation may be interesting for some people. When plotting all the results together, it might be useful to exclude some outliers:

``` r
trend_plot(munich, interpolation = FALSE, export.result = FALSE, export.plot = FALSE, split=FALSE, quantil = 0.5)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-57-1.png)

As you can appreciate, a lot of points have been omitted.

You can also change the significance level as mentioned above. Why don\`t we try?:

``` r
trend_plot(munich, interpolation = FALSE, export.result = FALSE, export.plot = FALSE, significant = 1)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-58-1.png)![](tutorial_files/figure-markdown_github/unnamed-chunk-58-2.png)

Now everything is significant!

**<font size="3"" face="verdana">interpollen()</font> function** is integrated in this function. Consult the "help" section of this function to use it.

Function <font face="verdana">trends()</font>
=============================================

The function **<font size="3" face="verdana">trends()</font>** has been created to calculate the main seasonal indexes of the pollen season ("Start Date", "Peak Date", "End Date" and "Pollen Integral") and their trends analysis over the seasons. It produces plots showing the distribution of the main seasonal indexes over the years.

The **results are stored in two folders termed "plot\_AeRobiology" and "table\_AeRobiology"** which will be located in your working directory (only if `export.result=TRUE` & `export.plot=TRUE`).

``` r
trends(munich, interpolation = FALSE, export.plot = TRUE, export.result = TRUE)
```

**NOTE:** The plots are not shown in your R environment. Because of the high amount of graphs, they are stored in new folders created in your working directory (search were you have saved the R project and you will find the folders).

<p class="comment">
The **interval of confidence** only appears if more than 6 dots are plotted. If not, a line crossing all the dots is plotted.
</p>
**<font size="3"" face="verdana">interpollen()</font> function** is integrated in this function. Consult the "help" section of this function to use it.

Function <font face="verdana">iplot\_abundance()</font>
=======================================================

The function **<font size="3" face="verdana">iplot\_abundance()</font>** generates a barplot based on the relative abundance in the air (as percentage) of each pollen/spore type with respect to the total amounts.

The main arguments are:

-   **n.types:** A `numeric` (integer) value specifying the number of the most abundant pollen types that must be represented in the plot of the relative abundance. A more detailed information about the selection of the considered pollen types may be consulted [here](https://cran.r-project.org/web/packages/AeRobiology/AeRobiology.pdf). The `n.types` argument will be 15 types by default.
-   **y.start/y.end:** A `numeric` (integer) value specifying the period selected to calculate relative abundances of the pollen types (start year - end year). If `y.start` and `y.end` are not specified (`NULL`), the entire database will be used to generate the pollen calendar. The `y.start` and `y.end` arguments will be `NULL` by default.
-   **col.bar:** A `character` string specifying the color of the bars to generate the graph showing the relative abundances of the pollen types. The `color` argument will be \#E69F00 by default, but any color may be selected.
-   **type.plot: ** A `character` string specifying the type of plot selected to show the plot showing the relative abundance of the pollen types. The implemented types that may be used are: "static" (generates a static ggplot object) and "dynamic" (generates a dynamic plotly object).
-   **exclude:** A character string vector with the names of the pollen types to be excluded from the plot.
-   Others...

``` r
iplot_abundance(munich, interpolation = FALSE, export.plot = FALSE, export.result = FALSE)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-60-1.png)

Now we are going to reduce the number of types:

``` r
iplot_abundance(munich, interpolation = FALSE, export.plot = FALSE, export.result = FALSE, n.types = 3)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-61-1.png)

We can also select the abundance for only one year and change the color:

``` r
iplot_abundance(munich, interpolation = FALSE, export.plot = FALSE, export.result = FALSE, n.types = 3, y.start = 2011, y.end = 2011, col.bar = "#d63131")
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-62-1.png)

Furthermore, we can make it interactive:

``` r
iplot_abundance(munich, interpolation = FALSE, export.plot = FALSE, export.result = FALSE, n.types = 3, y.start = 2011, y.end = 2011, col.bar = "#d63131", type.plot = "dynamic")
```

    ## PhantomJS not found. You can install it with webshot::install_phantomjs(). If it is installed, please make sure the phantomjs executable can be found via the PATH variable.

**<font size="3"" face="verdana">interpollen()</font> function** is integrated in this function. Consult the "help" section of this function to use it.

Function <font face="verdana">iplot\_pheno()</font>
===================================================

The function **<font size="3"" face="verdana">iplot\_pheno()</font>** generates a boxplot based on phenological parameters (start\_dates and end\_dates) which are calculated by the estimation of the main parameters of the pollen season. The main arguments are:

-   **data**: A `data.frame` object including the general database where calculation of the pollen season must be applied in order to generate the phenological plot based on the start\_dates and end\_dates. This data.frame must include a first column in "Date" format and the rest of columns in "numeric" format belonging to each pollen type by column.
-   **method**: A `character` string specifying the method applied to calculate the pollen season and the main parameters. The implemented methods that can be used are: "percentage", "logistic", "moving", "clinical" or "grains". A more detailed information about the different methods for defining the pollen season may be consulted in calculate\_ps function.
-   **n.types**: A `numeric` (integer) value specifying the number of the most abundant pollen types that must be represented. A more detailed information about the selection of the considered pollen types may be consulted [here](https://cran.r-project.org/web/packages/AeRobiology/AeRobiology.pdf). The `n.types` argument will be 15 by default.
-   **type.plot**: A `character` string specifying the type of plot selected to show the phenological plot. The implemented types that may be used are: "static" (generates a static ggplot object) and "dynamic" (generates a dynamic plotly object).
-   Other arguments related to **<font size="3"" face="verdana">calculate\_ps()</font> function**

``` r
iplot_pheno(munich, method= "percentage", perc=80, int.method="spline", n.types = 8)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-64-1.png)

We can change the method to establish the main pollen season and the number of pollen types to show:

``` r
iplot_pheno(munich, method= "clinical", n.clinical = 3, int.method="spline", n.types = 4)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-65-1.png)

Furthermore, we can make the plot interactive to obtain more information by clicking in each object:

``` r
iplot_pheno(munich, method= "clinical", n.clinical = 3, int.method="spline", n.types = 4, type.plot = "dynamic")
```

Function <font face="verdana">ps\_plot()</font>
===============================================

The function **<font size="3"" face="verdana">ps\_plot()</font> function** was designed to plot the main pollen season of a single pollen type and year. Some of the arguments are:

-   **data**: A `data.frame` object including the general database where interpollation must be performed. This data.frame must include a first column in "Date" format and the rest of the columns in "numeric" format. Each column must contain information of one pollen type. It is not necessary to insert missing gaps, the function will automatically detect them.
-   **pollen.type**: A `character` string specifying the name of the pollen type which will be plotted. The name must be exactly the same that appears in the column name. Mandatory argument with no default.
-   **year**: A `numeric` (interger) value specifying the season to be plotted. The season does not necessary fit a natural year. See calculate\_ps for more details. Mandatory argument with no default.
-   **export.plot**: A `logical` argument. If `TRUE`, the graph created will be exported in a new folder ("plot\_AeRobiology") in your working directory. The `export.plot` argument will be `TRUE` by default.
-   **days**: A `numeric` (interger) specifying the number of days beyond each side of the main pollen season that will be represented. The `days` argument will be 30 by default.
-   **fill.col**: A `character` string specifying the name of the color to fill the main pollen season in the plot. See ggplot function for more details. The `fill.col` argument will be "turquoise4" by default. It uses the ggplot color codes.
-   **axisname**:A `character` string specifying the title of the y axis. By default, `axisname = expression(paste("Pollen grains / m"^"3"))`
-   Others...

**<font size="3"" face="verdana">calculate\_ps</font> function** is integrated in this function. Consult [here](https://cran.r-project.org/web/packages/AeRobiology/AeRobiology.pdf) for more information. **Interpolation is mandatory** for this function. Due to technical encoding, you must use interpollen function through calculate\_ps function. Consult calculate\_ps for more information.

``` r
ps_plot(munich, pollen.type="Alnus", year=2011)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-67-1.png)

If you misspell some pollen type name, the function will tell you:

``` r
ps_plot(munich, pollen.type="Alnuscdscscr", year=2011)
```

    ## Error in ps_plot(munich, pollen.type = "Alnuscdscscr", year = 2011): pollen.type: Please, insert only a pollen type which is in your database (type the exact name)

**Let\`s test more arguments:**

``` r
ps_plot(munich, pollen.type="Alnus", year=2013, method= "percentage", perc=95 ,int.method = "lineal")
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-69-1.png)

As you have noticed, the arguments `method`, `perc` and `int.method` are from **<font size="3"" face="verdana">calculate\_ps</font> function**.

What about changing the interpolation method?

``` r
ps_plot(munich, pollen.type="Alnus", year=2013, method= "percentage", perc=95 ,int.method = "movingmean")
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-70-1.png)

Do you want a larger scale?

``` r
ps_plot(munich, pollen.type="Alnus", year=2013, days = 90)
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-71-1.png)

Maybe a different color and y-axis name?

``` r
ps_plot(munich, pollen.type="Alnus", year=2013, fill.col = "orange", axisname = "AeRobiology custom units")
```

![](tutorial_files/figure-markdown_github/unnamed-chunk-72-1.png)

[1] University of Castilla-La Mancha & ZAUM

[2] University of Malaga

[3] Zentrum Allergie und Umwelt (ZAUM)
