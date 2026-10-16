# jnr-jenner-analytics-part-III-inline-drop-down-to-open-source-matlab-summarize-data
Jenner Analytics part III inline drop down to open source matlab summarize data
     /*---
     c:/utl/jnr-jenner-analytics-part-III-inline-drop-down-to-open-source-matlab-summarize-data.sas;
     ---*/
    
    Jenner Analytics part III inline drop down to open source matlab summarize data;
    Third in a series of 10 languages.
    
    Too long to post: see
    https://github.com/rogerjdeangelis/jnr-jenner-analytics-part-III-inline-drop-down-to-open-source-matlab-summarize-data
    
    CONTENTS
    
      1 Install package database and windows, provides postgresql interface and windows functions like clipboard
      2 How the drop down provides macro variable output
        Matlab code to write to windows clipboard and create a macro variable
        from the clibboard
      3 Create input postgres table psdbClass
      4 Matlab create table psdbClass_means as mean age, height and weight by sex
      5 Jenner print postgres table psdbClass
      6 Drop down inline sandwich macros
      7 related repositories
    
    related
    https://github.com/rogerjdeangelis/jnr-jenner-analytics-part-I-inline-drop-down-to-duckdb-sql
    https://github.com/rogerjdeangelis/jnr-jenner-analytics-part-II-drop-down-to-open-source-spss-run-regression
    
    How to install matlab
    https://mirror.us-midwest-1.nexcess.net/gnu/octave/windows/
    octave-11.3.0-w64-installer.exe
    
    /******************************************************************************************************************/
    /* 1 INSTALL PACKAGE DATABASE, PROVIDES POSTGRESQL DATABASE AND WINDOWS - PROVIDES CLIPBOARD                      */
    /******************************************************************************************************************/
    
    /*--- therea ra a lot of packages, ie odbc, sqlite... ---*/
    %jnr_matlabBegin;
    cards4;
    pkg install -forge database
    pkg install -forge windows
    load database
    ;;;;
    %jnr_matlabEnd;
    
    /******************************************************************************************************************/
    /*  2 HOW THE DROP DOWN PROVIDES MACRO VARIABLE OUTPUT                                                            */
    /*    MATLAB CODE TO WRITE TO WINDOWS CLIPBOARD AND CREATE A MACRO VARIABLE                                       */
    /*    FROM THE CLIBBOARD                                                                                          */
    /******************************************************************************************************************/
    
    MATLAB WRITE TO CLIPBOARD AND CREATE MACRO VARIABLE GREETINGS WITH "Hello from MATLAD"
    --------------------------------------------------------------------------------------
    
    %symdel greetings / nowarn;
    
    %jnr_matlabBegin;
    cards4;
    pkg load windows
    txt = "Hello from MATLAB";
    clipboard('copy', txt);
    ;;;;
    %jnr_matlabEnd(return=greetings);
    
    %put --- &=greetings ---;
    
    --- GREETINGS=Hello from MATLAB ---
    
    /*--- COPDE IN MACRO %hnr_matlabEnd to load macro variable     ---*/
    /*--- TRANSFER CLIPBOARD TEXT INTO THE SAS MACRO VARIABLE TXT  ---*/
    filename clp clipbrd;
    
    data _null_;
      infile clp truncover;
      input;
      call symputx(""&return", _infile_, 'G');
      stop;
    run;
    
    POSTGRESQL WRITE TO WINDOWS CLIPBOARD (SUBSTITUTE FOR CLIPBOARD('COPY', TXT) IN YOUR PROGRAM
    ---------------------------------------------------------------------------------------------
    \copy (SELECT max(name) FROM psdbclass) TO PROGRAM 'clip.exe' WITH (FORMAT CSV, HEADER true, DELIMITER E'\t
    
    /******************************************************************************************************************/
    /* 3 CREATE INPUT POSTGRES TABLE PSDBCLASS                                                                        */
    /******************************************************************************************************************/
    
    libname psdb postgres
      database="template1"
      server="localhost"
      port=5432
      user="postgres"
      preserve_tab_names=YES;
    
    proc delete data=psdb.psdbclass;
    run;
    
    data psdb.psdbclass;
      set sashelp.class;
    run;
    
    options nocenter;
    proc print data=psdb.psdbclass(obs=3);
    title "Input postgres psdbclass table";
    run;
    
    libname psdb clear;
    
    
    LIST: 12:47:51
    ==============
    
    Input postgres psdbclass table
    
      Obs  Name     Sex  Age  Height  Weight
    
        1  Amir     M     13    61.2      95
        2  Bethany  F     14    63.8     105
        3  Carlos   M     12    58.5      88
    
    LOG:  12:47:51
    ==============
    
    NOTE: Copyright (c) 2026 Jenner Analytics Ltd., London, England.
    NOTE: Jenner v1.5.101 (build v1.5.101+21ec0ac00f.20261016T072007Z)
          Licensed to Roger DeAngelis, Serial 3BF7B3E6.
    NOTE: DATA _null_
    
    autexec started.
    
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    NOTE: Library WORKX assigned path=d:\wpswrkx.
    NOTE: DATA workx.class
    
    NOTE: Processing inline DATALINES (19 lines)
    
    NOTE: Read 19 rows from DATALINES.
    NOTE: The data set WORKX.CLASS has 19 observations and 5 variables.
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    NOTE: DATA _null_
    
    
    NOTE: DATA _null_ completed. Output written to FILE PRINT
    NOTE: Option SASAUTOS changed to c:/otojnr.
    NOTE: DATA _null_
    
    NOTE: Reading from fileref c:/jnr/runsas_selection.sas (c:/jnr/runsas_selection.sas)
        1
        2 libname psdb postgres
        3   database="template1"
        4   server="localhost"
        5   port=5432
        6   user="postgres"
        7   preserve_tab_names=YES;
        8
        9 proc delete data=psdb.psdbclass;
       10 run;
       11
       12 data psdb.psdbclass;
       13   set sashelp.class;
       14 run;
       15
       16 options nocenter;
       17 proc print data=psdb.psdbclass(obs=3);
       18 title "Input postgres psdbclass table";
       19 run;
       20
       21 libname psdb clear;
    
    NOTE: Read 21 rows from c:/jnr/runsas_selection.sas.
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    NOTE: DATA _null_
    
    autoexec completed  12:47:51
    
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    NOTE: PROC DELETE datasets=psdb.psdbclass
    
    WARNING: Dataset PSDB.PSDBCLASS does not exist.
    NOTE: DATA psdb.psdbclass
    
    
    NOTE: Read 19 rows from sashelp.class.
    NOTE: The data set PSDB.PSDBCLASS has 19 observations and 5 variables.
    NOTE: DATA elapsed:
      wall  0.07 seconds
      cpu   0.00 seconds
    NOTE: PROC PRINT data=psdb.psdbclass
    
    NOTE: PROC PRINT completed: 3 observations printed, 5 variables
    NOTE: Library PSDB cleared.
    
    /******************************************************************************************************************/
    /* 4 MATLAB CREATE TABLE PSDBCLASS_MEANS AS MEAN AGE, HEIGHT AND WEIGHT BY SEX                                    */
    /******************************************************************************************************************/
    
    %jnr_matlabBegin;
    cards4;
    pkg load database
    
    conn = pq_connect (setdbopts ( ...
        "host",     "localhost", ...
        "port",     "5432", ...
        "dbname",   "template1", ...
        "user",     "postgres"));
    
    unwind_protect
    
        pq_exec_params (conn, ...
            'DROP TABLE IF EXISTS psdbclass_means');
    
        pq_exec_params (conn, ...
            ['CREATE TABLE psdbclass_means AS ' ...
             'SELECT "Sex" AS sex, ' ...
             '       AVG("Age")    AS mean_age, ' ...
             '       AVG("Height") AS mean_height, ' ...
             '       AVG("Weight") AS mean_weight ' ...
             'FROM psdbclass ' ...
             'GROUP BY "Sex"']);
    
        result = pq_exec_params (conn, ...
            'SELECT sex, mean_age, mean_height, mean_weight FROM psdbclass_means ORDER BY sex');
    
        fprintf ("%-3s %10s %12s %12s\n", ...
                 "Sex", "Mean_age", "Mean_height", "Mean_weight");
    
        for i = 1:rows (result.data)
            fprintf ("%-3s %10.4f %12.4f %12.4f\n", ...
                     result.data{i, 1}, result.data{i, 2}, ...
                     result.data{i, 3}, result.data{i, 4});
        endfor
    
    unwind_protect_cleanup
        pq_close (conn);
    end_unwind_protect
    ;;;;
    %jnr_matlabEnd;
    
    
    LIST: 13:01:05
    ==============
    
    Sex   Mean_age  Mean_height  Mean_weight
    F      12.7778      58.9000      85.4444
    M      13.5000      63.5000     107.8000
    
    
    LOG:  12:47:51
    ==============
    
    NOTE: Copyright (c) 2026 Jenner Analytics Ltd., London, England.
    NOTE: Jenner v1.5.101 (build v1.5.101+21ec0ac00f.20261016T072007Z)
          Licensed to Roger DeAngelis, Serial 3BF7B3E6.
    NOTE: DATA _null_
    
    autexec started.
    
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    NOTE: Library WORKX assigned path=d:\wpswrkx.
    NOTE: DATA workx.class
    
    NOTE: Processing inline DATALINES (19 lines)
    
    NOTE: Read 19 rows from DATALINES.
    NOTE: The data set WORKX.CLASS has 19 observations and 5 variables.
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    NOTE: DATA _null_
    
    LOG:  13:01:05
    NOTE: DATA _null_ completed. Output written to FILE PRINT
    NOTE: Option SASAUTOS changed to c:/otojnr.
    NOTE: DATA _null_
    
    NOTE: Reading from fileref c:/jnr/runsas_selection.sas (c:/jnr/runsas_selection.sas)
        1 %jnr_matlabBegin;
        2 cards4;
        3 pkg load database
        4
        5 conn = pq_connect (setdbopts ( ...
        6     "host",     "localhost", ...
        7     "port",     "5432", ...
        8     "dbname",   "template1", ...
        9     "user",     "postgres"));
       10
       11 unwind_protect
       12
       13     pq_exec_params (conn, ...
       14         'DROP TABLE IF EXISTS psdbclass_means');
       15
       16     pq_exec_params (conn, ...
       17         ['CREATE TABLE psdbclass_means AS ' ...
       18          'SELECT "Sex" AS sex, ' ...
       19          '       AVG("Age")    AS mean_age, ' ...
       20          '       AVG("Height") AS mean_height, ' ...
       21          '       AVG("Weight") AS mean_weight ' ...
       22          'FROM psdbclass ' ...
       23          'GROUP BY "Sex"']);
       24
       25     result = pq_exec_params (conn, ...
       26         'SELECT sex, mean_age, mean_height, mean_weight FROM psdbclass_means ORDER BY sex');
       27
       28     fprintf ("%-3s %10s %12s %12s\n", ...
       29              "Sex", "Mean_age", "Mean_height", "Mean_weight");
       30
       31     for i = 1:rows (result.data)
       32         fprintf ("%-3s %10.4f %12.4f %12.4f\n", ...
       33                  result.data{i, 1}, result.data{i, 2}, ...
       34                  result.data{i, 3}, result.data{i, 4});
       35     endfor
       36
       37 unwind_protect_cleanup
       38     pq_close (conn);
       39 end_unwind_protect
       40 ;;;;
       41 %jnr_matlabEnd;
    
    NOTE: Read 41 rows from c:/jnr/runsas_selection.sas.
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    NOTE: DATA _null_
    
    autoexec completed  13:01:05
    
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    NOTE: DATA _null_
    
    NOTE: Writing to fileref D:\wpswrk/matlab_resolve.m (D:\wpswrk/matlab_resolve.m)
    NOTE: Processing inline DATALINES (37 lines)
    pkg load database
    
    conn = pq_connect (setdbopts ( ...
        "host",     "localhost", ...
        "port",     "5432", ...
        "dbname",   "template1", ...
        "user",     "postgres"));
    
    unwind_protect
    
        pq_exec_params (conn, ...
            'DROP TABLE IF EXISTS psdbclass_means');
    
        pq_exec_params (conn, ...
            ['CREATE TABLE psdbclass_means AS ' ...
             'SELECT "Sex" AS sex, ' ...
             '       AVG("Age")    AS mean_age, ' ...
             '       AVG("Height") AS mean_height, ' ...
             '       AVG("Weight") AS mean_weight ' ...
             'FROM psdbclass ' ...
             'GROUP BY "Sex"']);
    
        result = pq_exec_params (conn, ...
            'SELECT sex, mean_age, mean_height, mean_weight FROM psdbclass_means ORDER BY sex');
    
        fprintf ("%-3s %10s %12s %12s\n", ...
                 "Sex", "Mean_age", "Mean_height", "Mean_weight");
    
        for i = 1:rows (result.data)
            fprintf ("%-3s %10.4f %12.4f %12.4f\n", ...
                     result.data{i, 1}, result.data{i, 2}, ...
                     result.data{i, 3}, result.data{i, 4});
        endfor
    
    unwind_protect_cleanup
        pq_close (conn);
    end_unwind_protect
    
    NOTE: Read 37 rows from DATALINES.
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    
    NOTE: Fileref RUT assigned to device PIPE (payload: C:\Progra~1\GNUOCT~1\Octave-11.3.0\mingw64\bin\octave-cli D:\wpswrk/matlab_resolve.m ).
    NOTE: DATA _null_
    
    NOTE: Reading from fileref rut (D:\wpswrk\jenner_pipe_RUT_bb948c581caf40ee9004fa56aa6ac76a.tmp)
    Sex   Mean_age  Mean_height  Mean_weight
    F      12.7778      58.9000      85.4444
    M      13.5000      63.5000     107.8000
    
    NOTE: Read 3 rows from rut.
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    NOTE: Fileref RUT cleared.
    
    /*****************************************************************************************************************/
    /* 5 JENNER PRINT POSTGRES TABLE PSDBCLASS                                                                       */
    /*****************************************************************************************************************/
    
    libname psdb postgres
      database="template1"
      server="localhost"
      port=5432
      user="postgres"
      preserve_tab_names=YES;
    
    options nocenter;
    proc print data=psdb.psdbclass_means;
    title "Means from Matlab";
    run;
    
    libname psdb clear;
    
    
    LIST: 12:16:28
    ==============
    
    Means from Matlab
    
      Obs  sex       mean_age  mean_height    mean_weight
    
        1  M             13.5         63.5          107.8
        2  F    12.7777777778         58.9  85.4444444444
    
    
    LOG:  13:01:05
    ==============
    
    NOTE: Copyright (c) 2026 Jenner Analytics Ltd., London, England.
    NOTE: Jenner v1.5.101 (build v1.5.101+21ec0ac00f.20261016T072007Z)
          Licensed to Roger DeAngelis, Serial 3BF7B3E6.
    NOTE: DATA _null_
    
    autexec started.
    
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    NOTE: Library WORKX assigned path=d:\wpswrkx.
    NOTE: DATA workx.class
    
    NOTE: Processing inline DATALINES (19 lines)
    
    NOTE: Read 19 rows from DATALINES.
    NOTE: The data set WORKX.CLASS has 19 observations and 5 variables.
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    NOTE: DATA _null_
    
    
    NOTE: DATA _null_ completed. Output written to FILE PRINT
    NOTE: Option SASAUTOS changed to c:/otojnr.
    NOTE: DATA _null_
    
    NOTE: Reading from fileref c:/jnr/runsas_selection.sas (c:/jnr/runsas_selection.sas)
        1 %jnr_matlabBegin;
        2 cards4;
        3 pkg load database
        4
        5 conn = pq_connect (setdbopts ( ...
        6     "host",     "localhost", ...
        7     "port",     "5432", ...
        8     "dbname",   "template1", ...
        9     "user",     "postgres"));
       10
       11 unwind_protect
       12
       13     pq_exec_params (conn, ...
       14         'DROP TABLE IF EXISTS psdbclass_means');
       15
       16     pq_exec_params (conn, ...
       17         ['CREATE TABLE psdbclass_means AS ' ...
       18          'SELECT "Sex" AS sex, ' ...
       19          '       AVG("Age")    AS mean_age, ' ...
       20          '       AVG("Height") AS mean_height, ' ...
       21          '       AVG("Weight") AS mean_weight ' ...
       22          'FROM psdbclass ' ...
       23          'GROUP BY "Sex"']);
       24
       25     result = pq_exec_params (conn, ...
       26         'SELECT sex, mean_age, mean_height, mean_weight FROM psdbclass_means ORDER BY sex');
       27
       28     fprintf ("%-3s %10s %12s %12s\n", ...
       29              "Sex", "Mean_age", "Mean_height", "Mean_weight");
       30
       31     for i = 1:rows (result.data)
       32         fprintf ("%-3s %10.4f %12.4f %12.4f\n", ...
       33                  result.data{i, 1}, result.data{i, 2}, ...
       34                  result.data{i, 3}, result.data{i, 4});
       35     endfor
       36
       37 unwind_protect_cleanup
       38     pq_close (conn);
       39 end_unwind_protect
       40 ;;;;
       41 %jnr_matlabEnd;
    
    NOTE: Read 41 rows from c:/jnr/runsas_selection.sas.
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    NOTE: DATA _null_
    
    autoexec completed  13:01:05
    
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    NOTE: DATA _null_
    
    NOTE: Writing to fileref D:\wpswrk/matlab_resolve.m (D:\wpswrk/matlab_resolve.m)
    NOTE: Processing inline DATALINES (37 lines)
    pkg load database
    
    conn = pq_connect (setdbopts ( ...
        "host",     "localhost", ...
        "port",     "5432", ...
        "dbname",   "template1", ...
        "user",     "postgres"));
    
    unwind_protect
    
        pq_exec_params (conn, ...
            'DROP TABLE IF EXISTS psdbclass_means');
    
        pq_exec_params (conn, ...
            ['CREATE TABLE psdbclass_means AS ' ...
             'SELECT "Sex" AS sex, ' ...
             '       AVG("Age")    AS mean_age, ' ...
             '       AVG("Height") AS mean_height, ' ...
             '       AVG("Weight") AS mean_weight ' ...
             'FROM psdbclass ' ...
             'GROUP BY "Sex"']);
    
        result = pq_exec_params (conn, ...
            'SELECT sex, mean_age, mean_height, mean_weight FROM psdbclass_means ORDER BY sex');
    
        fprintf ("%-3s %10s %12s %12s\n", ...
                 "Sex", "Mean_age", "Mean_height", "Mean_weight");
    
        for i = 1:rows (result.data)
            fprintf ("%-3s %10.4f %12.4f %12.4f\n", ...
                     result.data{i, 1}, result.data{i, 2}, ...
                     result.data{i, 3}, result.data{i, 4});
        endfor
    
    unwind_protect_cleanup
        pq_close (conn);
    end_unwind_protect
    
    NOTE: Read 37 rows from DATALINES.
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    WARNING: The NOXWAIT system option does not apply to Jenner and has no effect.
    WARNING 30-12: The NOXSYNC system option is valid only at startup and was ignored.
    NOTE: Fileref RUT assigned to device PIPE (payload: C:\Progra~1\GNUOCT~1\Octave-11.3.0\mingw64\bin\octave-cli D:\wpswrk/matlab_resolve.m ).
    NOTE: DATA _null_
    
    NOTE: Reading from fileref rut (D:\wpswrk\jenner_pipe_RUT_bb948c581caf40ee9004fa56aa6ac76a.tmp)
    Sex   Mean_age  Mean_height  Mean_weight
    F      12.7778      58.9000      85.4444
    M      13.5000      63.5000     107.8000
    
    NOTE: Read 3 rows from rut.
    NOTE: DATA elapsed:
      wall  0.00 seconds
      cpu   0.00 seconds
    NOTE: Fileref RUT cleared.
    
    /******************************************************************************************************************/
    /* 6 INLINE DROP DOWN SANSWICH MACROS                                                                             */
    /******************************************************************************************************************/
    
    filename ft15f001 "c:/otojnr/jnr_matlabbegin.sas";
    parmcards4;
    %macro jnr_matlabBegin(
        resolve=Y
        )/des="Allow additional quote(backtic) and provide for macro resolution";
    
      %local pth;
      %let pth=%sysfunc(pathname(work));
    
      %utlfkil(&pth/matlab_resolve.m);
    
      data _null_;
        length _infile_ $255;
        file "&pth/matlab_resolve.m";
        input;
        if upcase("&resolve")=:"Y" then _infile_=resolve(_infile_);
        if index(_infile_,"`") then
            _infile_=translate(_infile_,"27"x,"`");
        put _infile_;
        putlog _infile_;
    
    %mend jnr_matlabBegin;
    ;;;;
    run;
    
    filename ft15f001 "c:/otojnr/jnr_matlabEnd.sas";
    parmcards4;
    %macro jnr_matlabEnd(
        return=
       )/des="execute matlab script";
    run;
      %local pth;
      %let pth=%sysfunc(pathname(work));
    
      options noxwait noxsync;
      filename rut pipe  "C:\Progra~1\GNUOCT~1\Octave-11.3.0\mingw64\bin\octave-cli &pth/matlab_resolve.m ";
      run;quit;
      data _null_;
        infile rut;
        input;
        putlog _infile_;
        file print;
        put _infile_;
      run;
    
      filename rut clear;
    
      * use the clipboard to create macro variable;
      %if "&return" ^= "" %then %do;
        filename clip clipbrd;
        data _null_;
         infile clip;  /*--- created from the SPSS script ---*/
         input;
         putlog "*******  " _infile_ "  ********";
         call symputx("&return",_infile_,"G");
        run;quit;
      %end;
    
    %mend jnr_matlabEnd;
    ;;;;
    run;
    
    /******************************************************************************************************************/
    /* 7  RELATED REOSITORIES                                                                                         */
    /******************************************************************************************************************/
    
    repo
    ----------------------------------------------------------------------------------------------------------------------------------
    https://github.com/rogerjdeangelis/utl-octave-matlab-deleting-rows-where-age-is-zero
    https://github.com/rogerjdeangelis/utl-personal-altair-slc-with-matlab-sympy-and-r-finite-element-cold-plate-heat-transfer
    https://github.com/rogerjdeangelis/utl-altair-slc-language2-drop-down-to-open-source-matlab-and-execute-sqlite-with-extensions
    https://github.com/rogerjdeangelis/utl-altair-slc-reading-and-writing-hdf5-files-with-open-source-matlab-python-and-r
    https://github.com/rogerjdeangelis/utl-matlab-combine-every-two-rows-ito-one-and-and-add-a-prefix-to-duplicate-column-names
    https://github.com/rogerjdeangelis/utl-altair-slc-using-matlab-to-calculate-Hysteresis-loss-K1-for-a-non-symetric-b-h-loop
    https://github.com/rogerjdeangelis/utl-bringing-matlab-into-the-larger-family-of-computer-languages
    https://github.com/rogerjdeangelis/utl-how-to-store-octave-matlab-objects-in-external-files-for-later-use-with-octave-r-and-python
    https://github.com/rogerjdeangelis/utl-matlab-select-all-possible-pairs-and-and-use-octave-sqlwrite-to-return-values-to-sas
    https://github.com/rogerjdeangelis/utl-octave-matlab-check-if-all-of-the-rows-of-a-dbtable-are-the-same
    https://github.com/rogerjdeangelis/utl-randomly-pick-one-player-from-the-heat-and-suns-for-captains-sql-sas-r-python-matlab
    https://github.com/rogerjdeangelis/utl-runing-a-regression-using-matlab-syntax-using-the-open-source-r-octave-package
    https://github.com/rogerjdeangelis/utl-sas-utility-functions-and-the-lack-of-support-for-utility-functions-r-python-and-matlab
    https://github.com/rogerjdeangelis/utl-convert-a-sqlite-numeric-table-to-a-matrix-for-octave-matlab-processing
    https://github.com/rogerjdeangelis/utl-running-your-matlab-script-in-the-altair-slc-motion-damped-spring
    
    /******************************************************************************************************************/
    /* END                                                                                                            */
    /******************************************************************************************************************/
    
