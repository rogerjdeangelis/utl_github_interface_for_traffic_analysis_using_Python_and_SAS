# utl_github_interface_for_traffic_analysis_using_Python_and_SAS
Github interface for traffic analysis for last 14 days. Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    Github interface for traffic analysis for last 14 days

    github
    https://tinyurl.com/ybzbk7ez
    https://github.com/rogerjdeangelis/utl_github_interface_for_traffic_analysis_using_Python_and_SAS

    Only works in Python 2.7.
    One reason is the 'print x' statement is not supported in Python 3.
    Look like POython has taken a page from SAS (classic editor support?)
    I wish SAS and WPS supported 2.7.

    I was unable to scrape the github reporsitory page.
    Tried, SAS, Python and R, protected page?
    Cut and paste is needed.


    INPUTS AND PREPARATION
    ======================

       1. Download and install
            https://github.com/seladb/github-traffic-stats
            pip install github_traffic_stats2

       2. Create a folder for intermediate files from python
          And make folder the present working directory(PWD)
          ie
             SASweave.db   pickle database
             SASweave.csv

       3. Copy python script to PWD
             github_traffic_stats.py
             usually located at

             {python}\Lib\site-packages\github_traffic_stats2
             substitute ylocation of python for '{python}'
             or copy from https://github.com/seladb/github-traffic-stats

       4. Logon to your profile in github

            go to
            https://github.com/settings/profile
            click on repositories
            Highlight and copy

            or
            logon to github
            click on settings (top left next to your github icon)
            click on repositories
            if logged on you can go to settings directly
               https://github.com/settings/profile

            Highlight your repositories and copy

       5. Paste in classic SAS editor after a cards4 statement (see process)

          cards4;
          rogerjdeangelis/sasbcat 5 KB
          rogerjdeangelis/SASweave 2.94 MB

       6. Use
          SAS python code below

    EXAMPLE OUTPUT

      WORK.TRAFFIC TOTAL OBS=12

         Obs      REPO           TIMESTAMP          DATESAS    COUNT    UNIQUES

           1    SASweave    2018-04-13T00:00:00Z     21287        4        1
           2    SASweave    2018-04-15T00:00:00Z     21289        3        1
           3    SASweave    2018-04-16T00:00:00Z     21290        6        2
           4    SASweave    2018-04-17T00:00:00Z     21291        1        1
           5    SASweave    2018-04-18T00:00:00Z     21292        2        1
           6    SASweave    2018-04-19T00:00:00Z     21293        6        3
           7    SASweave    2018-04-20T00:00:00Z     21294       13        1
           8    SASweave    2018-04-21T00:00:00Z     21295        1        1

           9    voodoo      2018-04-10T00:00:00Z     21284        1        1
          10    voodoo      2018-04-11T00:00:00Z     21285        5        1
          11    voodoo      2018-04-19T00:00:00Z     21293        1        1
          12    voodoo      2018-04-20T00:00:00Z     21294        3        1



    My Stats for the repos that had some activity

                                                             Upper
    Variable      N             Sum            Mean        Quartile         Maximum
    -------------------------------------------------------------------------------
    COUNT       137     345.0000000       2.5182482       3.0000000      15.0000000
    UNIQUES     137     182.0000000       1.3284672       1.0000000       9.0000000
    -------------------------------------------------------------------------------


    WORK.MOSTVISITED total obs=96

     REPO                                                                                   TOTCNT    TOTUNQ

     SASweave                                                                                 36        11
     utl_simple_one_to_many_join_using_SQL_and_datastep_hashes                                25         6
     utl_efficient_elimination_of_duplicate_data_using_the_modify_statement_Paul_Dorfman      24        13
     utl_transpose_rows_to_column_identifying_type_of_data                                    15         9
     utl_arrays_in_sql_or_count_variable_states_across_multiple_columns                       14         3
     utl_exce_combining_sheets_without_common_names_types_lengths                             14         9
     utl_graphics_400_SAS_and_R_graphics_with_code_and_datasets                               14         5
     voodoo                                                                                   10         4
     utl_create_your_own_dashboard_using_classic_base_sas                                      9         3

    * code for most visited;
    proc sql;
      create
        table mostVisited as
      select
        repo
       ,sum(count)  as totCnt
       ,sum(uniques) as totUnq
      from
        traffic
      group
        by repo
      order
        by totCnt descending
    ;quit;


    PROCESS
    =======

    
    data files;
      retain repo timestamp datesas count uniques;
      length cmd cmd1 $200 repo $384;
      input;

      * you need to set the working directory to where you want the intermediate file from python;
      if _n_=1 then call system("cd d:/csv");
      repo=scan(_infile_,2,'/ ');

      * build pickle database;
      cmd=catx(" ","py github_traffic_stats.py collect -r",repo,"-u rogerjdeangelis -p sas28rlx");
      call system(cmd);
      *call sleep(1);

      * export pickle database to csv;
      cmd1=catx(" ","py github_traffic_stats.py exportcsv -r", repo);
      call system(cmd1);
      drop cmd cmd1;
    cards4;
    rogerjdeangelis/Calculate-more-accurate-standard-deviation-as-quarters-are-added 5 KB
    rogerjdeangelis/Dynamic_variable_in_a_DOSUBL_execute_macro_in_SAS 4 KB
    rogerjdeangelis/Fastest-method-to-determine-if-a-column-contains-any-duplicates 8 KB
    rogerjdeangelis/generate_50_million_alphanumeric_unique_keys 20 KB
    rogerjdeangelis/How-to-create-a-table-to-hold-the-missing-value-count-for-multiple-columns 5 KB
    rogerjdeangelis/Median-of-all--maximum-payments-over-all-my-clients 6 KB
    rogerjdeangelis/ods_excel_does_not_always_honor_start_at--bug 4 KB
    rogerjdeangelis/ods_rtf_mutiple_justifications_within_one_compute_block 4 KB
    ;;;;
    run;quit;


    data traffic;
      set files;
      * process output from python;
      fylVarInp=cats("d:/csv/",repo,".csv");
      infile dummy filevar=fylVarInp dlm=',' end=dne;

      * cycle throug records;
      do until (dne);
         informat timestamp $22.;
         input timestamp count uniques;
         dateSas=input(substr(timestamp,1,10),yymmdd10.);
         if timestamp ne 'timestamp' then output;
      end;

    run;quit;


    OUTPUTS
    =======

    WORK.TRAFFIC total obs=12

    Obs      REPO           TIMESTAMP          DATESAS    COUNT    UNIQUES

      1    SASweave    2018-04-13T00:00:00Z     21287        4        1
      2    SASweave    2018-04-15T00:00:00Z     21289        3        1
      3    SASweave    2018-04-16T00:00:00Z     21290        6        2
      4    SASweave    2018-04-17T00:00:00Z     21291        1        1
      5    SASweave    2018-04-18T00:00:00Z     21292        2        1
      6    SASweave    2018-04-19T00:00:00Z     21293        6        3
      7    SASweave    2018-04-20T00:00:00Z     21294       13        1
      8    SASweave    2018-04-21T00:00:00Z     21295        1        1
      9    voodoo      2018-04-10T00:00:00Z     21284        1        1
     10    voodoo      2018-04-11T00:00:00Z     21285        5        1
     11    voodoo      2018-04-19T00:00:00Z     21293        1        1
     12    voodoo      2018-04-20T00:00:00Z     21294        3        1

      d:/SASweave.csv

       timestamp,count,uniques
       2018-04-13T00:00:00Z,4,1
       2018-04-15T00:00:00Z,3,1
       2018-04-16T00:00:00Z,6,2
       2018-04-17T00:00:00Z,1,1
       2018-04-18T00:00:00Z,2,1
       2018-04-19T00:00:00Z,6,3
       2018-04-20T00:00:00Z,13,1
       2018-04-21T00:00:00Z,1,1

      d:/SASweave_views.db (unwrapped for display)

       {
       "2018-04-16T00:00:00Z": "{\"count\": 6, \"uniques\": 2}",
       "2018-04-19T00:00:00Z": "{\"count\": 6, \"uniques\": 3}",
       "2018-04-18T00:00:00Z": "{\"count\": 2, \"uniques\": 1}",
       "2018-04-13T00:00:00Z": "{\"count\": 4, \"uniques\": 1}",
       "2018-04-17T00:00:00Z": "{\"count\": 1, \"uniques\": 1}",
       "2018-04-20T00:00:00Z": "{\"count\": 13, \"uniques\": 1}",
       "2018-04-21T00:00:00Z": "{\"count\": 1, \"uniques\": 1}",
       "2018-04-15T00:00:00Z": "{\"count\": 3, \"uniques\": 1}"
       }
    */
    *                _               _       _
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|

    ;

     see INPUTS

     The list od repositorys is copied from the repository web page

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    options noxwait noxsync xmin;
    data files;
      retain repo timestamp datesas count uniques;
      length cmd cmd1 $200 repo $384;
      input;

      * you need to set the working directory to where you want the intermediate file from python;
      if _n_=1 then call system("cd d:/csv");
      repo=scan(_infile_,2,'/ ');

      * build pickle database;
      cmd=catx(" ","py github_traffic_stats.py collect -r",repo,"-u rogerjdeangelis -p xxxxxxx");
      call system(cmd);
      *call sleep(1);

      * export pickle database to csv;
      cmd1=catx(" ","py github_traffic_stats.py exportcsv -r", repo);
      call system(cmd1);
      drop cmd cmd1;
    cards4;
    rogerjdeangelis/Calculate-more-accurate-standard-deviation-as-quarters-are-added 5 KB
    rogerjdeangelis/Dynamic_variable_in_a_DOSUBL_execute_macro_in_SAS 4 KB
    rogerjdeangelis/Fastest-method-to-determine-if-a-column-contains-any-duplicates 8 KB
    rogerjdeangelis/generate_50_million_alphanumeric_unique_keys 20 KB
    rogerjdeangelis/How-to-create-a-table-to-hold-the-missing-value-count-for-multiple-columns 5 KB
    rogerjdeangelis/Median-of-all--maximum-payments-over-all-my-clients 6 KB
    rogerjdeangelis/ods_excel_does_not_always_honor_start_at--bug 4 KB
    rogerjdeangelis/ods_rtf_mutiple_justifications_within_one_compute_block 4 KB
    ;;;;
    run;quit;


    data traffic;
      set files;
      * process output from python;
      fylVarInp=cats("d:/csv/",repo,".csv");
      infile dummy filevar=fylVarInp dlm=',' end=dne;

      * cycle throug records;
      do until (dne);
         informat timestamp $22.;
         input timestamp count uniques;
         dateSas=input(substr(timestamp,1,10),yymmdd10.);
         if timestamp ne 'timestamp' then output;
      end;

    run;quit;

