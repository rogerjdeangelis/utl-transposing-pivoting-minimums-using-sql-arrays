# utl-transposing-pivoting-minimums-using-sql-arrays
Tranposing pivoting minimums using sql arrays 
    %let pgm=utl-transposing-pivoting-minimums-using-sql-arrays;

    Transposing pivoting minimums using sql arrays

    GitHub
    https://github.com/rogerjdeangelis/utl-transposing-pivoting-minimums-using-sql-arrays

    I am providing a pure SQL solution for thaose who want a pure sql Solution.
    With a good compiler this shoud be quite fast, Hardware and software like repetitive code.

     _                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|


    data work.have(drop=id);
      retain type;
      INPUT id $ price store $;
      select(id);
        when ("B") type="STOREFRONT  ";
        when ("A") type="ONLINE ";
        when ("C") type="LAYAWAY";
      end; /* no need for otherwise */
    CARDS4;
    A 390 TARGET
    B 468 TARGET
    A 427 TARGET
    B 369 TARGET
    A 499 TARGET
    C 526 TARGET
    C 587 TARGET
    C 534 TARGET
    B 447 COSCO
    B 482 COSCO
    A 511 COSCO
    C 532 COSCO
    C 450 COSCO
    A 569 COSCO
    B 646 KOHLS
    B 652 KOHLS
    C 802 KOHLS
    A 761 KOHLS
    ;;;;
    run;quit;

    Up to 40 obs WORK.HAVE total obs=18

    Obs    TYPE          PRICE    STORE

      1    ONLINE         390     TARGET
      2    STOREFRONT     468     TARGET
      3    ONLINE         427     TARGET
      4    STOREFRONT     369     TARGET
      5    ONLINE         499     TARGET
      6    LAYAWAY        526     TARGET
      7    LAYAWAY        587     TARGET
      8    LAYAWAY        534     TARGET

      9    STOREFRONT     447     COSCO   Tranpose this one it has min price for storefronts
     10    STOREFRONT     482     COSCO
     11    ONLINE         511     COSCO
     12    LAYAWAY        532     COSCO
     13    LAYAWAY        450     COSCO   Transpose this one has min price for layaway
     14    ONLINE         569     COSCO

     15    STOREFRONT     646     KOHLS
     16    STOREFRONT     652     KOHLS
     17    LAYAWAY        802     KOHLS
     18    ONLINE         761     KOHLS
                 _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|


    Up to 40 obs WORK.WANT total obs=3

    Obs    STORE    LAYAWAY     ONLINE    STORE

     1     COSCO        450        511      447
     2     KOHLS        802        761      646
     3     TARGET       526        390      369

     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|

    proc sql;  /* create SQL array */

      /* create array of store types */
      select
         distinct type
      into
         :_type1-
      from
         have
      ;
      %let _typen=&sqlobs;

      /* sql array

      _TYPE1=LAYAWAY
      _TYPE2=ONLINE
      _TYPE3=STOREFRONT
      _TYPEN=3

      */

      %put xxx  &=_type1 &=_type2 &=_type2 &=_typen xxx;

      create
           table want as
      select
         store
        ,%do_over(_type,phrase=%str(min(case (type) when ("?") then price end) as ?),between=comma)
      from
         have
      group
         by store

    ;quit;


    /* if you want the generated code or mprint */

    data _null_;
        %do_over(_type,phrase=%str(put "min(case (type) when ('?') then price end) as ?" /));
    run;quit;

    min(case (type) when ('LAYAWAY') then price end) as LAYAWAY
    min(case (type) when ('ONLINE') then price end) as ONLINE
    min(case (type) when ('STOREFRONT') then price end) as STOREFRONT




                    _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|
