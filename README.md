# utl_github_interface_for_traffic_analysis_using_Python_and_SAS
    Github interface for traffic analysis for the last 14 days.

    github
    https://tinyurl.com/ybzbk7ez
    https://github.com/rogerjdeangelis/utl_github_interface_for_traffic_analysis_using_Python_and_SAS

    *
     _ __  _ __ ___ _ __
    | '_ \| '__/ _ \ '_ \
    | |_) | | |  __/ |_) |
    | .__/|_|  \___| .__/
    |_|            |_|
    ;

       1. Download and install
            https://pygithub.readthedocs.io/en/latest/introduction.html
            https://pygithub.readthedocs.io/en/latest/introduction.html#download-and-install
            pip install PyGithub

       2. Create a folder for intermediate files and final SAS table

          d:/txt
          d:/git/txt (list of macro invovations)


       3. Logon to your profile in github and copy list of repos to windows clipboard

            logon to github with your user and password
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


    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

    Logon into github and copy list of repos to windows clipboard then paste after cards4

    %utlnopts;
    data _null_;
      input;
      repo=scan(_infile_,2,'/ ');
      repo=cats('%trf(',repo,');');
      file "d:/git/txt/log.txt";
      put repo;
    cards4;
    rogerjdeangelis/Calculate-more-accurate-standard-deviation-as-quarters-are-added 5 KB  0 collaborators
    rogerjdeangelis/distinct-counts-for_3200-variables-and_660-thousand-records-using-HASH-SQL-and-proc-freq 24 KB  0 collaborators
    rogerjdeangelis/Dynamic_variable_in_a_DOSUBL_execute_macro_in_SAS 4 KB  0 collaborators
    rogerjdeangelis/Elegant-simple-array-input-from-a-flatfile-by-Novinosrin 3 KB  0 collaborators
    rogerjdeangelis/excel-how-do-I-remove-troublesome-characters-before-importing 13 KB  0 collaborators
    ;;;;
    run;quit;
    %utlopts;

    *            _               _
      ___  _   _| |_ _ __  _   _| |_ ___
     / _ \| | | | __| '_ \| | | | __/ __|
    | (_) | |_| | |_| |_) | |_| | |_\__ \
     \___/ \__,_|\__| .__/ \__,_|\__|___/
                    |_|
    ;

    * code above creates text file below

    d:/git/txt/log.txt

    %trf(Calculate-more-accurate-standard-deviation-as-quarters-are-added);
    %trf(distinct-counts-for_3200-variables-and_660-thousand-records-using-HASH-SQL-and-proc-freq);
    %trf(Dynamic_variable_in_a_DOSUBL_execute_macro_in_SAS);
    %trf(Elegant-simple-array-input-from-a-flatfile-by-Novinosrin);
    %trf(excel-how-do-I-remove-troublesome-characters-before-importing);

    libname git "d:/git";

    GIT.TRG_100HIT_HIT20150519 total obs=1,143

    Obs   REPOSAS                                                                                      VIEWS    UNIQUES

     1    Calculate-more-accurate-standard-deviation-as-quarters-are-added                               3         2
     2    distinct-counts-for_3200-variables-and_660-thousand-records-using-HASH-SQL-and-proc-freq       1         1
     3    Dynamic_variable_in_a_DOSUBL_execute_macro_in_SAS                                              1         1
     4    Elegant-simple-array-input-from-a-flatfile-by-Novinosrin                                       1         1
     5    excel-how-do-I-remove-troublesome-characters-before-importing                                  1         1

    *
     _ __  _ __ ___   ___ ___  ___ ___
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    ;

    /*
      It took me a long time to figure out how to export the 'contents' object to a SAS table.
      The issue I have with R and Python is that they are legends in thier own minds.

      Object contents appears to be a complex 'in memory ditionary view'?.
      It took me about 4hrs to convert it to a pandas dataframe and then a json object.

      R and python are ok as long as you don't encounter some of these strange obects.
      Unfortunately most packages and modules create 'strange' output.

      As far as I know there are only two types of dat needed for 99% of the procedural programming problems.

       Character
       Numeric

       Less is More

    */


    %macro trf(repo);

       /* for testin without macro
       %let repo=utl-generate-random-positive-integers-that-sum-to-a-particular-value;
       */

       proc datasets lib=work;  * delete intermediate table;
        delete repo;
       run;quit;

       %utlfkil(d:/json/dict.json);  * delete if exist;

       %utl_submit_py37("
          import pandas as pd;
          import numpy as np;
          import json;
          import xport;
          from github import Github;
          g = Github('rogerjdeangelis@gmail.com', 'xxxxxxxxxxxxxx');
          repo = g.get_repo('rogerjdeangelis/&repo');
          contents = repo.get_views_traffic();
          contents = repo.get_views_traffic(per='week');
          print(contents);
          want=pd.DataFrame.from_dict(contents,orient='index');
          want.columns = ['stats'];
          want = want.append({'stats': '&repo'}, ignore_index=True );
          print(want);
          df = want.to_json();
          f = open('d:/json/dict.json','w');
          f.write(df);
          f.close();
       ");

       filename resp 'd:/json/dict.json';
       libname jsn JSON fileref=resp;

       data repo;
         length repopy reposas $200 ;
         set jsn.stats(drop=
               ORDINAL_ROOT
               ORDINAL_STATS
               rename=(_=views  _2=uniques _3=repo));
         reposas="&repo";
         repopy=repo;
         drop repo;
       run;quit;

       proc append base=git.trf_hit data=repo ;
       run;quit;

    %mend trf;

    libname git "d:/git";

    proc datasets lib=git;
     delete trf_hit;
    run;quit;

    %trf(Dynamic_variable_in_a_DOSUBL_execute_macro_in_SAS);
    %trf(Elegant-simple-array-input-from-a-flatfile-by-Novinosrin);

    proc print data=git.trf_hit(drop=repopy) width=min;
    run;quit;

    %*inc "d:/git/txt/log.txt";

