# utl-converting-your-sas-datastep-programs-to-r
    %let pgm=utl-converting-your-sas-datastep-programs-to-r;

    Converting your sas datastep program to r

        1 SAS
          Macro to convert a numeric sas  dataset to an sas array on end of message
          Useful macro when prototyping r code using sas code (small arrays for testing)
        2 r
        3 macro to convert a numeric sas dataset to an in memory array

    github
    https://tinyurl.com/3tb5xfmf
    https://github.com/rogerjdeangelis/utl-converting-your-sas-datastep-programs-to-r

    SOAPBOX ON
      Sometines I think R has deviated too far from its roots with
      packages like tidyverse. Below is classic r solution from Jim Chambers at Bell Labs S language?
      I think programmers were over concerned about the perfomance of loops in r and felt all those apply
      functions were mandatory..
      Howver R is not big data?
    SOAPBOX OFF

    /****************************************************************************************************************************/
    /*                                                                  |                                                       */
    /*                   INPUT                                          |            OUTPUT                                     */
    /*                   =====                                          |            ======                                     */
    /*                                                                  |                                                       */
    /*       PIVOT  DATASET SD1.HAVE FAT TO LONG                        |    id= 101  BREAKFAST  cost= 21                       */
    /*                                                                  |    id= 101  LUNCH      cost= 12                       */
    /*                                                                  |    id= 101  DINNER     cost= 21                       */
    /*        BREAKFAST    LUNCH    DINNER                              |                                                       */
    /*                                                                  |    id= 202  BREAKFAST  cost= 31                       */
    /*            21         12       21                                |    id= 202  LUNCH      cost= 23                       */
    /*            31         23       32                                |    id= 202  DINNER     cost= 32                       */
    /*            21         22       22                                |                                                       */
    /*            22         32       23                                |    id= 303  BREAKFAST  cost= 21                       */
    /*            32         23       32                                |    id= 303  LUNCH      cost= 22                       */
    /*            23         22       22                                |    id= 303  DINNER     cost= 22                       */
    /*            11         31       13                                |                                                       */
    /*                                                                  |    id= 404  BREAKFAST  cost= 22                       */
    /*                                                                  |    id= 404  LUNCH      cost= 32                       */
    /*                                                                  |    id= 404  DINNER     cost= 23                       */
    /*                                                                  |                                                       */
    /*                                                                  |    id= 505  BREAKFAST  cost= 32                       */
    /*                                                                  |    id= 505  LUNCH      cost= 23                       */
    /*                                                                  |    id= 505  DINNER     cost= 32                       */
    /*                                                                  |                                                       */
    /*                                                                  |    id= 606  BREAKFAST  cost= 23                       */
    /*                                                                  |    id= 606  LUNCH      cost= 22                       */
    /*                                                                  |    id= 606  DINNER     cost= 22                       */
    /*                                                                  |                                                       */
    /*                                                                  |    id= 707  BREAKFAST  cost= 11                       */
    /*                                                                  |    id= 707  LUNCH      cost= 31                       */
    /*                                                                  |    id= 707  DINNER     cost= 13                       */
    /*                                                                  |                                                       */
    /*------------------------------------------------------------------+-------------------------------------------------------*/
    /*  ___  __ _ ___                                                   |                                                       */
    /* / __|/ _` / __|                                                  |    _ __                                               */
    /* \__ \ (_| \__ \                                                  |   | `__|                                              */
    /* |___/\__,_|___/                                                  |   | |                                                 */
    /*                                                                  |   |_|                                                 */
    /*                                                                  |                                                       */
    /* libname sd1 "d:/sd1";                                            | %utl_rbeginx;                                         */
    /* options validvarname=upcase;                                     | parmcards4;                                           */
    /*                                                                  |                                                       */
    /* data sd1.have;                                                   | have<-read.table(header = TRUE, text = "              */
    /*  input breakast lunch dinner                                     | breakast lunch dinner                                 */
    /* cards4;                                                          | 21 12 21                                              */
    /* 21 12 21                                                         | 31 23 32                                              */
    /* 31 23 32                                                         | 21 22 22                                              */
    /* 21 22 22                                                         | 22 32 23                                              */
    /* 22 32 23                                                         | 32 23 32                                              */
    /* 32 23 32                                                         | 23 22 22                                              */
    /* 23 22 22                                                         | 11 31 13                                              */
    /* 11 31 13                                                         | ")                                                    */
    /* ;;;;                                                             | saveRDS(have, file="d:/rds/have.rds")                 */
    /* run;quit;                                                        | ;;;;                                                  */
    /*                                                                  | %utl_rendx;                                           */
    /*                                                                  |                                                       */
    /*------------------------------------------------------------------+-------------------------------------------------------*/
    /*                                                                  |                                                       */
    /*                                                                  |                                                       */
    /* %symdel _array / nowarn;                                         | %utl_rbeginx;                                         */
    /*                                                                  | parmcards4;                                           */
    /* data want;                                                       |                                                       */
    /*                                                                  |                                                       */
    /*  array cols[7,3] col1-col21 (%utl_numary(sd1.have));             | cols<-readRDS("d:/rds/have.rds")                      */
    /*  array xpos[21]   xpo1-xpo21;                                    | xpos<-as.matrix(1,1:21)                               */
    /*                                                                  |                                                       */
    /*  xporow=1;                                                       | xporow=1                                              */
    /*  do row=1 to 7 by 1;                                             | for ( row in seq(1,7,1) ) {                           */
    /*   id = row*100 + row;                                            |  id = row*100 + row;                                  */
    /*   pt = 'id='!!id!!' breakfast '!!'cost='!! cols[row,1]; put pt;  |  cat('id=',id,' breakfast ','cost=',cols[row,1],'\n') */
    /*   pt = 'id='!!id!!' lunch     '!!'cost='!! cols[row,2]; put pt;  |  cat('id=',id,' lunch     ','cost=',cols[row,2],'\n') */
    /*   pt = 'id='!!id!!' dinner    '!!'cost='!! cols[row,3]; put pt;  |  cat('id=',id,' dinner    ','cost=',cols[row,3],'\n') */
    /*   xporow=xporow+3;                                               |  }                                                    */
    /*  end;                                                            |                                                       */
    /*  stop;                                                           | ;;;;                                                  */
    /*                                                                  | %utl_rendx;                                           */
    /* run;quit;                                                        |                                                       */
    /*                                                                  |                                                       */
    /****************************************************************************************************************************/
    /*
    / |  ___  __ _ ___
    | | / __|/ _` / __|
    | | \__ \ (_| \__ \
    |_| |___/\__,_|___/

    */

    libname sd1 "d:/sd1";
    options validvarname=upcase;

    data sd1.have;
     input breakast lunch dinner;
    cards4;
    21 12 21
    31 23 32
    21 22 22
    22 32 23
    32 23 32
    23 22 22
    11 31 13
    ;;;;
    run;quit;

    %symdel _array / nowarn;

    data want;

      /*----                                                   ----*/
      /*---- convert numeric sas datasets to a numeric arry    ----*/
      /*---- usefull when prototyping r code using sas         ----*/
      /*----                                                   ----*/

      array cols[7,3] col1-col21 (%utl_numary(sd1.have));
      array xpos[21]   xpo1-xpo21;

      * load array inp(7,3) ;

      xporow=1;
      do row=1 to 7 by 1;
         id = row*100 + row;
         pt = 'id='!!strip(id)!!' BREAKFAST '!!'cost='!! left(cols[row,1]); put pt;
         pt = 'id='!!strip(id)!!' LUNCH     '!!'cost='!! left(cols[row,2]); put pt;
         pt = 'id='!!strip(id)!!' DINNER    '!!'cost='!! left(cols[row,3]); put pt;
         xporow=xporow+3;
      end;
      stop;

    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* id=101 BREAKFAST cost=21                                                                                               */
    /* id=101 LUNCH     cost=12                                                                                               */
    /* id=101 DINNER    cost=21                                                                                               */
    /* id=202 BREAKFAST cost=31                                                                                               */
    /* id=202 LUNCH     cost=23                                                                                               */
    /* id=202 DINNER    cost=32                                                                                               */
    /* id=303 BREAKFAST cost=21                                                                                               */
    /* id=303 LUNCH     cost=22                                                                                               */
    /* id=303 DINNER    cost=22                                                                                               */
    /* id=404 BREAKFAST cost=22                                                                                               */
    /* id=404 LUNCH     cost=32                                                                                               */
    /* id=404 DINNER    cost=23                                                                                               */
    /* id=505 BREAKFAST cost=32                                                                                               */
    /* id=505 LUNCH     cost=23                                                                                               */
    /* id=505 DINNER    cost=32                                                                                               */
    /* id=606 BREAKFAST cost=23                                                                                               */
    /* id=606 LUNCH     cost=22                                                                                               */
    /* id=606 DINNER    cost=22                                                                                               */
    /* id=707 BREAKFAST cost=11                                                                                               */
    /* id=707 LUNCH     cost=31                                                                                               */
    /* id=707 DINNER    cost=13                                                                                               */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___
    |___ \   _ __
      __) | | `__|
     / __/  | |
    |_____| |_|

    */

    %utl_rbeginx;
    parmcards4;
    library(dplyr)
    have<-read.table(header = TRUE, text = "
    breakast lunch dinner
    21 12 21
    31 23 32
    21 22 22
    22 32 23
    32 23 32
    23 22 22
    11 31 13
    ")
    saveRDS(have, file="d:/rds/have.rds")
    ;;;;
    %utl_rendx;


    %utl_rbeginx;
    parmcards4;
    library(haven)

    cols<-readRDS("d:/rds/have.rds")
    xpos<-as.matrix(1,1:21)

    xporow=1;

    for ( row in seq(1,7,1) ) {
       id = row*100 + row;
       cat('id=',id,' BREAKFAST ','cost=', cols[row,1],'\n');
       cat('id=',id,' LUNCH     ','cost=', cols[row,2],'\n');
       cat('id=',id,' DINNER    ','cost=', cols[row,3],'\n');
    }
    ;;;;
    %utl_rendx;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  id= 101  BREAKFAST  cost= 21                                                                                          */
    /*  id= 101  LUNCH      cost= 12                                                                                          */
    /*  id= 101  DINNER     cost= 21                                                                                          */
    /*  id= 202  BREAKFAST  cost= 31                                                                                          */
    /*  id= 202  LUNCH      cost= 23                                                                                          */
    /*  id= 202  DINNER     cost= 32                                                                                          */
    /*  id= 303  BREAKFAST  cost= 21                                                                                          */
    /*  id= 303  LUNCH      cost= 22                                                                                          */
    /*  id= 303  DINNER     cost= 22                                                                                          */
    /*  id= 404  BREAKFAST  cost= 22                                                                                          */
    /*  id= 404  LUNCH      cost= 32                                                                                          */
    /*  id= 404  DINNER     cost= 23                                                                                          */
    /*  id= 505  BREAKFAST  cost= 32                                                                                          */
    /*  id= 505  LUNCH      cost= 23                                                                                          */
    /*  id= 505  DINNER     cost= 32                                                                                          */
    /*  id= 606  BREAKFAST  cost= 23                                                                                          */
    /*  id= 606  LUNCH      cost= 22                                                                                          */
    /*  id= 606  DINNER     cost= 22                                                                                          */
    /*  id= 707  BREAKFAST  cost= 11                                                                                          */
    /*  id= 707  LUNCH      cost= 31                                                                                          */
    /*  id= 707  DINNER     cost= 13                                                                                          */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*____         _   _
    |___ /   _   _| |_| |  _ __  _   _ _ __ ___   __ _ _ __ _   _   _ __ ___   __ _  ___ _ __ ___
      |_ \  | | | | __| | | `_ \| | | | `_ ` _ \ / _` | `__| | | | | `_ ` _ \ / _` |/ __| `__/ _ \
     ___) | | |_| | |_| | | | | | |_| | | | | | | (_| | |  | |_| | | | | | | | (_| | (__| | | (_) |
    |____/   \__,_|\__|_|_|_| |_|\__,_|_| |_| |_|\__,_|_|   \__, | |_| |_| |_|\__,_|\___|_|  \___/
                       |___|                                |___/
    */


    %macro utl_numary(_inp);

     %symdel _array / nowarn;

     %dosubl(%nrstr(
         filename clp "d:/txt/all.txt";
         data _null_;
           file clp;
           set &_inp;
           put (_all_) (best.) @@;
         run;quit;
         data _null_;
           infile clp;
           input;
           _infile_=compbl(_infile_);
           _infile_=translate(strip(_infile_),',',' ');
           call symputx('_array',_infile_);
         run;quit;
         ))

         &_array

    %mend utl_numary;

    %symdel _ary / nowarn;

    %put "array cols[7,3] col1-col21 (%utl_numary(sd1.have))";

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  "array cols[7,3] col1-col21 (21,12,21,31,23,32,21,22,22,22,32,23,32,23,32,23,22,22,11,31,13)"                         */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
Converting your sas datastep program to r  
