# utl_import_excel_column_names_that_contain_a_dollar_sign_and_rename_without
Import excel column names that contain a dollar sign and rename without. Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    Import excel column names that contain a dollar sign and rename without

    set options validvarname=any

    see
    https://goo.gl/CPh1gi
    https://github.com/rogerjdeangelis/utl_import_excel_column_names_that_contain_a_dollar_sign_and_rename_without

    Same results with WPS and  options validvarname=any
    However you have to split up the rename code because WPS does
    not support DOSUBL yet.

    see
    https://goo.gl/oc5cVB
    https://stackoverflow.com/questions/48739508/sas-proc-import-column-name-has-dollar-sign


    INPUT ( Note dollar signns in column names)
    ===========================================

    SHEET CLASS IN WORKBOOK D:/XLS/CLASS.XLSX


      +----------------------------------------------------------------+
      |     A      |    B       |     C      |    D       |    E       |
      +----------------------------------------------------------------+
    1 |  NAME$     |   SEX$     |    AGE$    |  HEIGHT$   |  WEIGHT$   |
      +------------+------------+------------+------------+------------+
    2 | ALFRED     |    M       |    14      |    69      |  112.5     |
      +------------+------------+------------+------------+------------+
       ...
      +------------+------------+------------+------------+------------+
    19| WILLIAM    |    M       |    15      |   66.5     |  112       |
      +------------+------------+------------+------------+------------+


    PROCESS ( all the code)
    =======================

        libname xel "d:/xls/dollar.xlsx";
        data class;
          set xel.class;
        run;quit;
        libname xel clear;

        RENAME WITHOUT DOLLAR SIGN

        data want;
          if _n_=0 then do;
            %let rc=%sysfunc(dosubl('
              proc transpose data=class(obs=1) out=clsXpo;var _all_;run;quit;
              proc sql;
                 select cats(quote(strip(_name_)),"n=",compress(_name_,"$"))
                 into :ren separated by " " from clsXpo;
              ;quit;
            '));
          end;

          set class(rename=(&ren));

        run;quit;

    OUTPUT
    ======

     WORK.WANT total obs=19

        NAME       SEX    AGE    HEIGHT    WEIGHT

        Alfred      M      14     69.0      112.5
        Alice       F      13     56.5       84.0
        Barbara     F      13     65.3       98.0
        Carol       F      14     62.8      102.5
        Henry       M      14     63.5      102.5
        James       M      12     57.3       83.0
        Jane        F      12     59.8       84.5
        Janet       F      15     62.5      112.5
        ...

    *                _                            _
     _ __ ___   __ _| | _____    _____  _____ ___| |
    | '_ ` _ \ / _` | |/ / _ \  / _ \ \/ / __/ _ \ |
    | | | | | | (_| |   <  __/ |  __/>  < (_|  __/ |
    |_| |_| |_|\__,_|_|\_\___|  \___/_/\_\___\___|_|

    ;

    options validvarname=any;
    libname xel "d:/xls/dollar.xlsx";
    data xel.class;
      set sashelp.class(rename=(
            NAME   = 'NAME$'n
            SEX    = 'SEX$'n
            AGE    = 'AGE$'n
            HEIGHT = 'HEIGHT$'n
            WEIGHT = 'WEIGHT$'n
         ));
    run;quit;
    libname xel clear;


    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    libname xel "d:/xls/dollar.xlsx";
    data class;
      set xel.class;
    run;quit;
    libname xel clear;

    /*
    Up to 40 obs from class total obs=19

    Obs    NAME$      SEX$    AGE$    HEIGHT$    WEIGHT$

      1    Alfred      M       14       69.0      112.5
      2    Alice       F       13       56.5       84.0
      3    Barbara     F       13       65.3       98.0
      4    Carol       F       14       62.8      102.5
      5    Henry       M       14       63.5      102.5
      6    James       M       12       57.3       83.0
      7    Jane        F       12       59.8       84.5
      8    Janet       F       15       62.5      112.5
    */


    * WPS same as SAS above;
    %utl_submit_wps64('
    options validvarname=any;
    libname wrk sas7bdat "%sysfunc(pathname(work))";
    libname xel excel "d:/xls/dollar.xlsx";
    data wrk.class;
      set xel.class;
    run;quit;
    libname xel clear;
    run;quit;
    ');

    *SAS split for wps;
    data want;
      if _n_=0 then do;
        %let rc=%sysfunc(dosubl('
          proc transpose data=class(obs=1) out=clsXpo;var _all_;run;quit;
          proc sql;
             select cats(quote(strip(_name_)),"n=",compress(_name_,"$"))
             into :ren separated by " " from clsXpo;
          ;quit;
        '));
      end;

      set class(rename=(&ren));

    run;quit;


     WORK.WANT total obs=19

        NAME       SEX    AGE    HEIGHT    WEIGHT

        Alfred      M      14     69.0      112.5
        Alice       F      13     56.5       84.0
        Barbara     F      13     65.3       98.0
        Carol       F      14     62.8      102.5
        Henry       M      14     63.5      102.5
        James       M      12     57.3       83.0
        Jane        F      12     59.8       84.5
        Janet       F      15     62.5      112.5
        ...

