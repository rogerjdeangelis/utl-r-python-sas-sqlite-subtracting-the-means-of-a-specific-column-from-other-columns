# utl-r-python-sas-sqlite-subtracting-the-means-of-a-specific-column-from-other-columns
R python sas sqlite subtracting the means of a specific column from other columns 
    %let pgm=utl-r-python-sas-sqlite-subtracting-the-means-of-a-specific-column-from-other-columns;

    R python sas sqlite subtracting the means of a specific column from other columns

    %stop_submission;

       Three Solutions
          1 SAS SQL
          2 R SQL (exact same code as SAS)
          3 Python SQL

       SOAPBOX ON
          4 R non sql (issues with datatypes and data structure?)
            https://stackoverflow.com/users/903061/gregor-thomas
            I had some minor issues
                Did not work using a imported sas dataset (numeric type issue?)
                Had to convert the outout to  a data table  in order to export
                SQLlite does not have all the data structure and data type issues?
                Less is more
                R and especially Python are legends in there own minds?
       SOAPBOX OFF

          5 SAS SQL arrays (general solution) (you can gen the code and eliminate the %do_overs)
            Depending on the compiler SQL can be very fast.

    related repos on end

    github
    https://tinyurl.com/mr2e3jf8
    https://github.com/rogerjdeangelis/utl-r-python-sas-sqlite-subtracting-the-means-of-a-specific-column-from-other-columns

    stackoverflow
    https://tinyurl.com/4nss6jwh
    https://stackoverflow.com/questions/78374453/r-subtracting-the-means-of-a-specific-column-from-other-columns

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*                          SUBTRACT B1 MEAN FROM PREVIOUS 3 COLUMNS  (interate over B2 and B3)                           */
    /*                                                                                                                        */
    /*     INPUT       |                         PROCESS                                      OUTPUT                          */
    /*                 |                                                            |                                         */
    /*  DA D2 DD   B1  |  DA D2 DD   B1  B1Mean  DA-B1mean    D2-B1mean  DD-B1mean  |    DA      D2      DD      B1           */
    /*                 |                                                            |                                         */
    /*   1  6  2  0.1  |   1  6  2  0.1   0.18      0.82=1-.18  5.82      1.82      |  0.82    5.82    1.82    0.1            */
    /*   2  7  3  0.1  |   2  7  3  0.1   0.18      1.82=2-.18  6.82      2.82      |  1.82    6.82    2.82    0.1            */
    /*   3  8  4  0.4  |   3  8  4  0.4   0.18      2.82        7.82      3.82      |  2.82    7.82    3.82    0.4            */
    /*   4  9  5  0.2  |   4  9  5  0.2   0.18      3.82        8.82      4.82      |  3.82    8.82    4.82    0.2            */
    /*   5 10  6  0.1  |   5 10  6  0.1   0.18      4.82        9.82      5.82      |  4.82    9.82    5.82    0.1            */
    /*                 |            ===                                             |                                         */
    /*                 |            0.90 Sum                                        |                                         */
    /*                 |            0,18 Mean                                       |                                         */
    /*                 |                                                            |                                         */
    /**************************************************************************************************************************/

    /*
     _ __ ___  ___ __ _ _ __
    | `__/ _ \/ __/ _` | `_ \
    | | |  __/ (_| (_| | |_) |
    |_|  \___|\___\__,_| .__/
                       |_|
    */


    /**************************************************************************************************************************************************/
    /*                                                                                                                                                */
    /*                                                                                                                                                */
    /*                    INPUT                              PROCESS                                    OUTPUT                                        */
    /*  1 SAS SQL         =====                              =======                                     ======                                       */
    /*  =========                                                                                                                                     */
    /*                                                                                                                                                */
    /*  data sd.have;                                 libname sd1 "d:/sd1";                                                                           */
    /*  input                                                                                                                                         */
    /*      DA D2 DD B1 D5 DG DI B2 DM H4 D7 B3;      proc sql;                                                                                       */
    /*  cards4;                                        create                         DA    D2   DD  B1  D5   DG  D   B2   DM   H4   D7  B3           */
    /*  1 6 2 0.1 2 7 3 0.2 21 5 26 0.1                  table want as               ==== ====  ==== === ===  === === === ==== ==== ==== ===          */
    /*  2 7 3 0.1 3 8 4 0.2 22 6 27 0.3                select                        0.82 5.82 1.82 0.1 1.8  6.8 2.8 0.2 20.7 4.74 25.7 0.1           */
    /*  3 8 4 0.4 4 9 5 0.4 23 7 28 0.4                   l.da - r.bm1 as da         1.82 6.82 2.82 0.1 2.8  7.8 3.8 0.2 21.7 5.74 26.7 0.3           */
    /*  4 9 5 0.2 5 10 6 0.1 24 8 29 0.4                 ,l.d2 - r.bm1 as d2         2.82 7.82 3.82 0.4 3.8  8.8 4.8 0.4 22.7 6.74 27.7 0.4           */
    /*  5 10 6 0.1 6 11 7 0.1 25 9 30 0.1                ,l.dd - r.bm1 as dd         3.82 8.82 4.82 0.2 4.8  9.8 5.8 0.1 23.7 7.74 28.7 0.4           */
    /*  ;;;;                                             ,l.b1                       4.82 9.82 5.82 0.1 5.8 10.8 6.8 0.1 24.7 8.74 29.7 0.1           */
    /*  run;quit;                                        ,l.d5 - r.bm2 as d5                                                                          */
    /*                                                   ,l.dg - r.bm2 as dg                                                                          */
    /*                                                   ,l.di - r.bm2 as di                                                                          */
    /*  DA D2 DD  B1 D5 DG DI  B2  DM H4  D7  B3         ,l.b2                                                                                        */
    /*   1  6  2 0.1  2  7  3 0.2  21  5  26 0.1         ,l.dm - r.bm3 as dm                                                                          */
    /*   2  7  3 0.1  3  8  4 0.2  22  6  27 0.3         ,l.h4 - r.bm3 as h4                                                                          */
    /*   3  8  4 0.4  4  9  5 0.4  23  7  28 0.4         ,l.d7 - r.bm3 as d7                                                                          */
    /*   4  9  5 0.2  5 10  6 0.1  24  8  29 0.4         ,l.b3                                                                                        */
    /*   5 10  6 0.1  6 11  7 0.1  25  9  30 0.1       from                                                                                           */
    /*                                                   sd1.have as l inner join                                                                     */
    /*                                                     (select                                                                                    */
    /*                                                         avg(b1) as bm1                                                                         */
    /*                                                        ,avg(b2) as bm2                                                                         */
    /*                                                        ,avg(b3) as bm3                                                                         */
    /*                                                        from                                                                                    */
    /*                                                           sd1.have                                                                             */
    /*                                                       )  as r                                                                                  */
    /*                                                 on                                                                                             */
    /*                                                     1=1                                                                                        */
    /*                                                ;quit;                                                                                          */
    /*                                                                                                                                                */
    /*------------------------------------------------------------------------------------------------------------------------------------------------*/
    /*                                                                                                                                                */
    /* 2 R SQL (EXACT SAME CODE AS SAS)                                                                                                               */
    /* =================================                                                                                                              */
    /*                                                                                                                                                */
    /*   Same as sas input                            %utl_rbegin;                    DA    D2   DD  B1  D5   DG  D   B2   DM   H4   D7  B3           */
    /*                                                parmcards4;                    ==== ====  ==== === ===  === === === ==== ==== ==== ===          */
    /*                                                library(sqldf)                 0.82 5.82 1.82 0.1 1.8  6.8 2.8 0.2 20.7 4.74 25.7 0.1           */
    /*                                                library(haven)                 1.82 6.82 2.82 0.1 2.8  7.8 3.8 0.2 21.7 5.74 26.7 0.3           */
    /*                                                have<-read_sas(                2.82 7.82 3.82 0.4 3.8  8.8 4.8 0.4 22.7 6.74 27.7 0.4           */
    /*                                                 "d:/sd1/have.sas7bdat")       3.82 8.82 4.82 0.2 4.8  9.8 5.8 0.1 23.7 7.74 28.7 0.4           */
    /*                                                have;                          4.82 9.82 5.82 0.1 5.8 10.8 6.8 0.1 24.7 8.74 29.7 0.1           */
    /*                                                want<-sqldf('                                                                                   */
    /*                                                  select                                                                                        */
    /*                                                     l.da - r.bm1 as da                                                                         */
    /*                                                    ,l.d2 - r.bm1 as d2                                                                         */
    /*                                                    ,l.dd - r.bm1 as dd                                                                         */
    /*                                                    ,l.b1                                                                                       */
    /*                                                    ,l.d5 - r.bm2 as d5                                                                         */
    /*                                                    ,l.dg - r.bm2 as dg                                                                         */
    /*                                                    ,l.di - r.bm2 as di                                                                         */
    /*                                                    ,l.b2                                                                                       */
    /*                                                    ,l.dm - r.bm3 as dm                                                                         */
    /*                                                    ,l.h4 - r.bm3 as h4                                                                         */
    /*                                                    ,l.d7 - r.bm3 as d7                                                                         */
    /*                                                    ,l.b3                                                                                       */
    /*                                                  from                                                                                          */
    /*                                                    have as l inner join                                                                        */
    /*                                                      (select                                                                                   */
    /*                                                           avg(b1) as bm1                                                                       */
    /*                                                          ,avg(b2) as bm2                                                                       */
    /*                                                          ,avg(b3) as bm3                                                                       */
    /*                                                       from                                                                                     */
    /*                                                          have                                                                                  */
    /*                                                      )  as r                                                                                   */
    /*                                                   on                                                                                           */
    /*                                                      1=1                                                                                       */
    /*                                                ');                                                                                             */
    /*                                                want;                                                                                           */
    /*                                                source("c:/temp/fn_tosas9.R");                                                                  */
    /*                                                fn_tosas9(dataf=want);                                                                          */
    /*                                                ;;;;                                                                                            */
    /*                                                %utl_rend;                                                                                      */
    /*                                                                                                                                                */
    /*                                                libname tmp "c:/temp";                                                                          */
    /*                                                proc print data=                                                                                */
    /*                                                 tmp.want(drop=rownames);                                                                       */
    /*                                                run;quit;                                                                                       */
    /*                                                                                                                                                */
    /*------------------------------------------------------------------------------------------------------------------------------------------------*/
    /*                                                                                                                                                */
    /* 3 PYTHON SQL                                                                                                                                   */
    /* ============                                                                                                                                   */
    /*                                                                                                                                                */
    /*   Same as sas input                           %utlfkil(c:/temp/want.sastbdat);             Same as 2                                           */
    /*                                               %utlfkil( C:/temp/want.sav);                                                                     */
    /*                                                                                                                                                */
    /*                                               %utl_pybegin;                                                                                    */
    /*                                               parmcards;                                                                                       */
    /*                                               import os                                                                                        */
    /*                                               from os import path                                                                              */
    /*                                               import sys                                                                                       */
    /*                                               import subprocess                                                                                */
    /*                                               import time                                                                                      */
    /*                                               import pandas as pd                                                                              */
    /*                                               import numpy as np                                                                               */
    /*                                               from pandasql import sqldf                                                                       */
    /*                                               mysql = lambda q: sqldf(q, globals())                                                            */
    /*                                               from pandasql import PandaSQL                                                                    */
    /*                                               pdsql = PandaSQL(persist=True)                                                                   */
    /*                                               sqlite3conn = next(pdsql.conn.gen).connection.connection                                         */
    /*                                               sqlite3conn.enable_load_extension(True)                                                          */
    /*                                               sqlite3conn.load_extension('c:/temp/libsqlitefunctions.dll')                                     */
    /*                                               mysql = lambda q: sqldf(q, globals())                                                            */
    /*                                               import pyreadstat as ps                                                                          */
    /*                                               have, meta = ps.read_sas7bdat('d:/sd1/have.sas7bdat')                                            */
    /*                                               print(have)                                                                                      */
    /*                                               want = pdsql('''                                                                                 */
    /*                                                 select                                                                                         */
    /*                                                    l.da - r.bm1 as da                                                                          */
    /*                                                   ,l.d2 - r.bm1 as d2                                                                          */
    /*                                                   ,l.dd - r.bm1 as dd                                                                          */
    /*                                                   ,l.b1                                                                                        */
    /*                                                   ,l.d5 - r.bm2 as d5                                                                          */
    /*                                                   ,l.dg - r.bm2 as dg                                                                          */
    /*                                                   ,l.di - r.bm2 as di                                                                          */
    /*                                                   ,l.b2                                                                                        */
    /*                                                   ,l.dm - r.bm3 as dm                                                                          */
    /*                                                   ,l.h4 - r.bm3 as h4                                                                          */
    /*                                                   ,l.d7 - r.bm3 as d7                                                                          */
    /*                                                   ,l.b3                                                                                        */
    /*                                                 from                                                                                           */
    /*                                                   have as l inner join                                                                         */
    /*                                                     (select                                                                                    */
    /*                                                          avg(b1) as bm1                                                                        */
    /*                                                         ,avg(b2) as bm2                                                                        */
    /*                                                         ,avg(b3) as bm3                                                                        */
    /*                                                      from                                                                                      */
    /*                                                         have                                                                                   */
    /*                                                       )  as r                                                                                  */
    /*                                                  on                                                                                            */
    /*                                                     1=1                                                                                        */
    /*                                               ''')                                                                                             */
    /*                                               print(want)                                                                                      */
    /*                                               exec(open('c:/temp/fn_tosas9.py').read())                                                        */
    /*                                               fn_tosas9(                                                                                       */
    /*                                                  want                                                                                          */
    /*                                                  ,dfstr="want"                                                                                 */
    /*                                                  ,timeest=3                                                                                    */
    /*                                                  )                                                                                             */
    /*                                               ;;;;                                                                                             */
    /*                                               %utlpyend;                                                                                       */
    /*                                                                                                                                                */
    /*                                               libname tmp "c:/temp";                                                                           */
    /*                                               proc print data=tmp.want;                                                                        */
    /*                                               run;quit;                                                                                        */
    /*                                                                                                                                                */
    /*------------------------------------------------------------------------------------------------------------------------------------------------*/
    /*                                                                                                                                                */
    /*  4 R NON SQL (VERY NICE)                                                                                                                       */
    /*  =======================                                                                              B                B                 B     */
    /*                                               * just incase not deleted in call;                      L                L                 L     */
    /*  Because of data structure and                %utlfkil(c:/temp/want.sastbdat);                        A                A                 A     */
    /*  type issues the input is inline              %utlfkil( C:/temp/rdsdat.rds);                          N                N                 N     */
    /*  in the r code?                                                                        DA    D2   DD  K1  D5   DG  D   K2   DM   H4   D7 K3    */
    /*                                               %utl_rbegin;                            ==== ====  ==== === ===  === === === ==== ==== ==== ===  */
    /*                                               parmcards4;                             0.82 5.82 1.82 0.1 1.8  6.8 2.8 0.2 20.7 4.74 25.7 0.1   */
    /*                                               library(data.table)                     1.82 6.82 2.82 0.1 2.8  7.8 3.8 0.2 21.7 5.74 26.7 0.3   */
    /*                                               df<- data.frame(da = 1:5,               2.82 7.82 3.82 0.4 3.8  8.8 4.8 0.4 22.7 6.74 27.7 0.4   */
    /*                                                d2 = 6:10,                             3.82 8.82 4.82 0.2 4.8  9.8 5.8 0.1 23.7 7.74 28.7 0.4   */
    /*                                                dd = 2:6,                              4.82 9.82 5.82 0.1 5.8 10.8 6.8 0.1 24.7 8.74 29.7 0.1   */
    /*                                                blank1 = c(0.1,0.1,0.4,0.2,0.1),                                                                */
    /*                                                d5 = 2:6,                                                                                       */
    /*                                                dg = 7:11,                                                                                      */
    /*                                                di = 3:7,                                                                                       */
    /*                                                blank2 = c(0.2,0.2,0.4,0.1,0.1),                                                                */
    /*                                                dm = 21:25,                                                                                     */
    /*                                                h4 = 5:9,                                                                                       */
    /*                                                d7 = 26:30,                                                                                     */
    /*                                                blank3=c(0.1,0.3,0.4,0.4,0.1)                                                                   */
    /*                                                );                                                                                              */
    /*                                               blanks = grep("blank", names(df))                                                                */
    /*                                               blank_means = colMeans(df[blanks])                                                               */
    /*                                               df[-blanks] = Map("-"                                                                            */
    /*                                                 ,df[-blanks]                                                                                   */
    /*                                                 ,rep(blank_means, each = 3))                                                                   */
    /*                                               df                                                                                               */
    /*                                               want<-as.data.table(df);                                                                         */
    /*                                               want;                                                                                            */
    /*                                               source("c:/temp/fn_tosas9.R");                                                                   */
    /*                                               fn_tosas9(dataf=want);                                                                           */
    /*                                               ;;;;                                                                                             */
    /*                                               %utl_rend;                                                                                       */
    /*                                                                                                                                                */
    /*                                               libname tmp "c:/temp";                                                                           */
    /*                                               proc print data=tmp.want                                                                         */
    /*                                                (drop=rownames);                                                                                */
    /*                                               run;quit;                                                                                        */
    /*                                                                                                                                                */
    /*------------------------------------------------------------------------------------------------------------------------------------------------*/
    /*                                                                                                                                                */
    /* 5 SAS SQL ARRAYS (GENERAL SOLUTION)                                                                                                            */
    /* ===================================                                                                                                            */
    /*                                               %array(bms,values=b1m b1m b1m b2m b2m b2m b3m b3m b3m);          SAME as 1 SAS SQL               */
    /*  SAME as 1 SAS SQL                            %array(bs,values=%utl_varlist(sd1.have,keep=b:));                                                */
    /*                                               %array(nb,values=%utl_varlist(sd1.have,drop=b:));                                                */
    /*                                                                                                                                                */
    /*                                               proc sql;                                                                                        */
    /*                                                 create                                                                                         */
    /*                                                    table want as                                                                               */
    /*                                                 select                                                                                         */
    /*                                                    %do_over(nb bms,phrase=l.?nb-r.?bms as ?nb,between=comma)                                   */
    /*                                                   ,%do_over(bs,phrase=l.?bs,between=comma)                                                     */
    /*                                                 from                                                                                           */
    /*                                                    sd1.have as l,                                                                              */
    /*                                                       (select                                                                                  */
    /*                                                          %do_over(bs,phrase=avg(?bs) as ?bsm,between=comma)                                    */
    /*                                                       from                                                                                     */
    /*                                                         sd1.have) as r                                                                         */
    /*                                                 where                                                                                          */
    /*                                                    1=1                                                                                         */
    /*                                               ;quit;                                                                                           */
    /*                                                                                                                                                */
    /**************************************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    libname sd1 "d:/sd1";

    data sd1.have;
    input
        DA D2 DD B1 D5 DG DI B2 DM H4 D7 B3;
    cards4;
    1 6 2 0.1 2 7 3 0.2 21 5 26 0.1
    2 7 3 0.1 3 8 4 0.2 22 6 27 0.3
    3 8 4 0.4 4 9 5 0.4 23 7 28 0.4
    4 9 5 0.2 5 10 6 0.1 24 8 29 0.4
    5 10 6 0.1 6 11 7 0.1 25 9 30 0.1
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* Obs    DA    D2    DD     B1    D5    DG    DI    B2    DM    H4    D7     B3                                          */
    /*                                                                                                                        */
    /* 1      1     6     2    0.1     2     7     3    0.2    21     5    26    0.1                                          */
    /* 2      2     7     3    0.1     3     8     4    0.2    22     6    27    0.3                                          */
    /* 3      3     8     4    0.4     4     9     5    0.4    23     7    28    0.4                                          */
    /* 4      4     9     5    0.2     5    10     6    0.1    24     8    29    0.4                                          */
    /* 5      5    10     6    0.1     6    11     7    0.1    25     9    30    0.1                                          */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                             _
    / |  ___  __ _ ___   ___  __ _| |
    | | / __|/ _` / __| / __|/ _` | |
    | | \__ \ (_| \__ \ \__ \ (_| | |
    |_| |___/\__,_|___/ |___/\__, |_|
                                |_|
    */

    /*----                                                                   ----*/
    /*----  for a large number of columns use sql arrays see #5              ----*/
    /*----                                                                   ----*/

    libname sd1 "d:/sd1";

    proc sql;
      create
        table sd1.want as
      select
         l.da - r.bm1 as da
        ,l.d2 - r.bm1 as d2
        ,l.dd - r.bm1 as dd
        ,l.b1
        ,l.d5 - r.bm2 as d5
        ,l.dg - r.bm2 as dg
        ,l.di - r.bm2 as di
        ,l.b2
        ,l.dm - r.bm3 as dm
        ,l.h4 - r.bm3 as h4
        ,l.d7 - r.bm3 as d7
        ,l.b3
      from
        sd1.have as l inner join
          (select
               avg(b1) as bm1
              ,avg(b2) as bm2
              ,avg(b3) as bm3
           from
              sd1.have
          )  as r
       on
          1=1
    ;quit;

    proc print data=sd1.want;
    run;quit;


    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* WORK.WANT total obs=5                                                                                                  */
    /*                                                                                                                        */
    /*   DA      D2      DD      B1     D5     DG      DI     B2      DM      H4       D7      B3                             */
    /*                                                                                                                        */
    /*  0.82    5.82    1.82    0.1    1.8     6.8    2.8    0.2    20.74    4.74    25.74    0.1                             */
    /*  1.82    6.82    2.82    0.1    2.8     7.8    3.8    0.2    21.74    5.74    26.74    0.3                             */
    /*  2.82    7.82    3.82    0.4    3.8     8.8    4.8    0.4    22.74    6.74    27.74    0.4                             */
    /*  3.82    8.82    4.82    0.2    4.8     9.8    5.8    0.1    23.74    7.74    28.74    0.4                             */
    /*  4.82    9.82    5.82    0.1    5.8    10.8    6.8    0.1    24.74    8.74    29.74    0.1                             */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                     _
    |___ \   _ __   ___  __ _| |
      __) | | `__| / __|/ _` | |
     / __/  | |    \__ \ (_| | |
    |_____| |_|    |___/\__, |_|
                           |_|
    */

    * just incase not deleted in call;
    %utlfkil(c:/temp/want.sastbdat);
    %utlfkil( C:/temp/rdsdat.rds);

    %utl_rbegin;
    parmcards4;
    library(sqldf)
    library(haven)
    have<-read_sas("d:/sd1/have.sas7bdat")
    have;
    want<-sqldf('
      select
         l.da - r.bm1 as da
        ,l.d2 - r.bm1 as d2
        ,l.dd - r.bm1 as dd
        ,l.b1
        ,l.d5 - r.bm2 as d5
        ,l.dg - r.bm2 as dg
        ,l.di - r.bm2 as di
        ,l.b2
        ,l.dm - r.bm3 as dm
        ,l.h4 - r.bm3 as h4
        ,l.d7 - r.bm3 as d7
        ,l.b3
      from
        have as l inner join
          (select
               avg(b1) as bm1
              ,avg(b2) as bm2
              ,avg(b3) as bm3
           from
              have
          )  as r
       on
          1 = 1
    ')
    want
    source("c:/temp/fn_tosas9.R")
    fn_tosas9(dataf=want)
    ;;;;
    %utl_rend;

    libname tmp "c:/temp";
    proc print data=
     tmp.want(drop=rownames);
    run;quit;


    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* R                                                                                                                      */
    /*                                                                                                                        */
    /*    DA    D2    DD    B1    D5    DG    DI    B2    DM    H4    D7    B3    rn                                          */
    /*                                                                                                                        */
    /*  0.82  5.82  1.82   0.1   1.8   6.8   2.8   0.2  20.7  4.74  25.7   0.1     1                                          */
    /*  1.82  6.82  2.82   0.1   2.8   7.8   3.8   0.2  21.7  5.74  26.7   0.3     2                                          */
    /*  2.82  7.82  3.82   0.4   3.8   8.8   4.8   0.4  22.7  6.74  27.7   0.4     3                                          */
    /*  3.82  8.82  4.82   0.2   4.8   9.8   5.8   0.1  23.7  7.74  28.7   0.4     4                                          */
    /*  4.82  9.82  5.82   0.1   5.8  10.8   6.8   0.1  24.7  8.74  29.7   0.1     5                                          */
    /*                                                                                                                        */
    /* SAS                                                                                                                    */
    /*                                                                                                                        */
    /*  DA    D2   DD  B1  D5   DG  D   B2   DM   H4   D7  B3                                                                 */
    /* ==== ====  ==== === ===  === === === ==== ==== ==== ===                                                                */
    /* 0.82 5.82 1.82 0.1 1.8  6.8 2.8 0.2 20.7 4.74 25.7 0.1                                                                 */
    /* 1.82 6.82 2.82 0.1 2.8  7.8 3.8 0.2 21.7 5.74 26.7 0.3                                                                 */
    /* 2.82 7.82 3.82 0.4 3.8  8.8 4.8 0.4 22.7 6.74 27.7 0.4                                                                 */
    /* 3.82 8.82 4.82 0.2 4.8  9.8 5.8 0.1 23.7 7.74 28.7 0.4                                                                 */
    /* 4.82 9.82 5.82 0.1 5.8 10.8 6.8 0.1 24.7 8.74 29.7 0.1                                                                 */
    /* SAS                                                                                                                    */
    /*                                                                                                                        */
    /* Obs     DA      D2      DD      B1     D5     DG      DI     B2      DM      H4       D7      B3                       */
    /*                                                                                                                        */
    /*  1     0.64    5.64    1.64    0.1    1.6     6.6    2.6    0.2    20.48    4.48    25.48    0.1                       */
    /*  2     1.64    6.64    2.64    0.1    2.6     7.6    3.6    0.2    21.48    5.48    26.48    0.3                       */
    /*  3     2.64    7.64    3.64    0.4    3.6     8.6    4.6    0.4    22.48    6.48    27.48    0.4                       */
    /*  4     3.64    8.64    4.64    0.2    4.6     9.6    5.6    0.1    23.48    7.48    28.48    0.4                       */
    /*  5     4.64    9.64    5.64    0.1    5.6    10.6    6.6    0.1    24.48    8.48    29.48    0.1                       */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*____               _   _                             _
    |___ /   _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
      |_ \  | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
     ___) | | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
    |____/  | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
            |_|    |___/                                |_|
    */

    %utlfkil(c:/temp/want.sastbdat);
    %utlfkil( C:/temp/want.sav);

    %utl_pybegin;
    parmcards;
    import os
    from os import path
    import sys
    import subprocess
    import time
    import pandas as pd
    import numpy as np
    from pandasql import sqldf
    mysql = lambda q: sqldf(q, globals())
    from pandasql import PandaSQL
    pdsql = PandaSQL(persist=True)
    sqlite3conn = next(pdsql.conn.gen).connection.connection
    sqlite3conn.enable_load_extension(True)
    sqlite3conn.load_extension('c:/temp/libsqlitefunctions.dll')
    mysql = lambda q: sqldf(q, globals())
    import pyreadstat as ps
    have, meta = ps.read_sas7bdat('d:/sd1/have.sas7bdat')
    print(have)
    want = pdsql('''
      select
         l.da - r.bm1 as da
        ,l.d2 - r.bm1 as d2
        ,l.dd - r.bm1 as dd
        ,l.b1
        ,l.d5 - r.bm2 as d5
        ,l.dg - r.bm2 as dg
        ,l.di - r.bm2 as di
        ,l.b2
        ,l.dm - r.bm3 as dm
        ,l.h4 - r.bm3 as h4
        ,l.d7 - r.bm3 as d7
        ,l.b3
      from
        have as l inner join
          (select
               avg(b1) as bm1
              ,avg(b2) as bm2
              ,avg(b3) as bm3
           from
              have
          )  as r
       on
          1=1
    ''')
    print(want)
    exec(open('c:/temp/fn_tosas9.py').read())
    fn_tosas9(
       want
       ,dfstr="want"
       ,timeest=3
       )
    ;;;;
    %utl_pyend;


    libname tmp "c:/temp";
    proc print data=tmp.want;
    run;quit;


    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* Pythpn                                                                                                                 */
    /*                                                                                                                        */
    /*       da    d2    dd   B1   d5    dg   di   B2     dm    h4     d7   B3                                                */
    /*  0  0.82  5.82  1.82  0.1  1.8   6.8  2.8  0.2  20.74  4.74  25.74  0.1                                                */
    /*  1  1.82  6.82  2.82  0.1  2.8   7.8  3.8  0.2  21.74  5.74  26.74  0.3                                                */
    /*  2  2.82  7.82  3.82  0.4  3.8   8.8  4.8  0.4  22.74  6.74  27.74  0.4                                                */
    /*  3  3.82  8.82  4.82  0.2  4.8   9.8  5.8  0.1  23.74  7.74  28.74  0.4                                                */
    /*  4  4.82  9.82  5.82  0.1  5.8  10.8  6.8  0.1  24.74  8.74  29.74  0.1                                                */
    /*                                                                                                                        */
    /* SAS                                                                                                                    */
    /*                                                                                                                        */
    /*   DA      D2      DD      B1     D5     DG      DI     B2      DM      H4       D7      B3                             */
    /*                                                                                                                        */
    /*  0.82    5.82    1.82    0.1    1.8     6.8    2.8    0.2    20.74    4.74    25.74    0.1                             */
    /*  1.82    6.82    2.82    0.1    2.8     7.8    3.8    0.2    21.74    5.74    26.74    0.3                             */
    /*  2.82    7.82    3.82    0.4    3.8     8.8    4.8    0.4    22.74    6.74    27.74    0.4                             */
    /*  3.82    8.82    4.82    0.2    4.8     9.8    5.8    0.1    23.74    7.74    28.74    0.4                             */
    /*  4.82    9.82    5.82    0.1    5.8    10.8    6.8    0.1    24.74    8.74    29.74    0.1                             */
    /*                                                                                                                        */
    /**************************************************************************************************************************/
    /*  _                                    _
    | || |    _ __   _ __   ___    ___  __ _| |
    | || |_  | `__| | `_ \ / _ \  / __|/ _` | |
    |__   _| | |    | | | | (_) | \__ \ (_| | |
       |_|   |_|    |_| |_|\___/  |___/\__, |_|
                                          |_|
    */

    /*----                                                                   ----*/
    /*----  https://stackoverflow.com/users/903061/gregor-thomas             ----*/
    /*----                                                                   ----*/

    * just incase not deleted in call;
    %utlfkil(c:/temp/want.sas7bdat);
    %utlfkil( C:/temp/rdsdat.rds);

    %utl_rbegin;
    parmcards4;
    library(data.table)
    df<- data.frame(da = 1:5,
     d2 = 6:10,
     dd = 2:6,
     blank1 = c(0.1,0.1,0.4,0.2,0.1),
     d5 = 2:6,
     dg = 7:11,
     di = 3:7,
     blank2 = c(0.2,0.2,0.4,0.1,0.1),
     dm = 21:25,
     h4 = 5:9,
     d7 = 26:30,
     blank3=c(0.1,0.3,0.4,0.4,0.1)
     );
    blanks = grep("blank", names(df))
    blank_means = colMeans(df[blanks])
    df[-blanks] = Map("-"
      ,df[-blanks]
      ,rep(blank_means, each = 3))
    df
    want<-as.data.table(df);
    want;
    source("c:/temp/fn_tosas9.R");
    fn_tosas9(dataf=want);
    ;;;;
    %utl_rend;

    libname tmp "c:/temp";
    proc print data=tmp.want
     (drop=rownames);
    run;quit;


    %array(bms,values=b1m b1m b1m b2m b2m b2m b3m b3m b3m);
    %array(bs,values=%utl_varlist(sd1.have,keep=b:));
    %array(nb,values=%utl_varlist(sd1.have,drop=b:));

    proc sql;
      create
         table want as
      select
         %do_over(nb bms,phrase=l.?nb-r.?bms as ?nb,between=comma)
        ,%do_over(bs,phrase=l.?bs,between=comma)
      from
         sd1.have as l,
            (select
               %do_over(bs,phrase=avg(?bs) as ?bsm,between=comma)
            from
              sd1.have) as r
      where
         1=1
    ;quit;

    /*___                              _
    | ___|   ___  __ _ ___   ___  __ _| |   __ _ _ __ _ __ __ _ _   _ ___
    |___ \  / __|/ _` / __| / __|/ _` | |  / _` | `__| `__/ _` | | | / __|
     ___) | \__ \ (_| \__ \ \__ \ (_| | | | (_| | |  | | | (_| | |_| \__ \
    |____/  |___/\__,_|___/ |___/\__, |_|  \__,_|_|  |_|  \__,_|\__, |___/
                                    |_|                         |___/
    */


    %array(bms,values=b1m b1m b1m b2m b2m b2m b3m b3m b3m);
    %array(bs,values=%utl_varlist(sd1.have,keep=b:));
    %array(nb,values=%utl_varlist(sd1.have,drop=b:));

    proc sql;
      create
         table want as
      select
         %do_over(nb bms,phrase=l.?nb-r.?bms as ?nb,between=comma)
        ,%do_over(bs,phrase=l.?bs,between=comma)
      from
         sd1.have as l,
            (select
               %do_over(bs,phrase=avg(?bs) as ?bsm,between=comma)
            from
              sd1.have) as r
      where
         1=1
    ;quit;



    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* WORK.WANT total obs=5                                                                                                  */
    /*                                                                                                                        */
    /*   DA      D2      DD      B1     D5     DG      DI     B2      DM      H4       D7      B3                             */
    /*                                                                                                                        */
    /*  0.82    5.82    1.82    0.1    1.8     6.8    2.8    0.2    20.74    4.74    25.74    0.1                             */
    /*  1.82    6.82    2.82    0.1    2.8     7.8    3.8    0.2    21.74    5.74    26.74    0.3                             */
    /*  2.82    7.82    3.82    0.4    3.8     8.8    4.8    0.4    22.74    6.74    27.74    0.4                             */
    /*  3.82    8.82    4.82    0.2    4.8     9.8    5.8    0.1    23.74    7.74    28.74    0.4                             */
    /*  4.82    9.82    5.82    0.1    5.8    10.8    6.8    0.1    24.74    8.74    29.74    0.1                             */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*
     _ __ ___ _ __   ___  ___
    | `__/ _ \ `_ \ / _ \/ __|
    | | |  __/ |_) | (_) \__ \
    |_|  \___| .__/ \___/|___/
             |_|
    */

    https://github.com/rogerjdeangelis/utl-adding-the-missing-math-stat-and-string-functions-to-python-sql-pandasql-sqllite3
    https://github.com/rogerjdeangelis/utl-exporting-python-panda-dataframes-to-wps-r-using-a-shared-sqllite-database
    https://github.com/rogerjdeangelis/utl-missing-basic-math-and-stat-functions-in-python-sqlite3-sql
    https://github.com/rogerjdeangelis/utl-sqlite-processing-in-python-with-added-math-and-stat-functions
    https://github.com/rogerjdeangelis/utl-sqllite-working-with-dates-in-r-and-python


    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
