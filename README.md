# utl-given-a-fact-table-of-codes-lookup-grade-level-and-gender-in-a-dimension-table
Given a fact table of codes lookup grade level and gender in a dimension table
    Given a fact table of codes lookup grade level and gender in a dimension table                                              
                                                                                                                                
      Seven  Solutions                                                                                                          
                                                                                                                                
            1. HASH(modified a little)  https://stackoverflow.com/users/4965549/tom                                             
            2. Proc Format (https://stackoverflow.com/users/1623007/joe)                                                        
            3. MACRO lookup (faster than a format? */                                                                           
            4. SQL                                                                                                              
            5. Datastep index lookup                                                                                            
            6. DOSUBL (someday may be the fastest if shared memory,                                                             
                       macros vars are in memory, need smart compiler -not for this coding)                                     
            7. Arrays (not shown)                                                                                               
                                                                                                                                
                                                                                                                                
    github                                                                                                                      
    https://tinyurl.com/y3ret9cr                                                                                                
    https://github.com/rogerjdeangelis/utl-given-a-fact-table-of-codes-lookup-grade-level-and-gender-in-a-dimension-table       
                                                                                                                                
    https://tinyurl.com/y2yzt87o                                                                                                
    https://stackoverflow.com/questions/56675317/sas-lookup-on-per-variable-basis                                               
                                                                                                                                
    *_                   _                                                                                                      
    (_)_ __  _ __  _   _| |_                                                                                                    
    | | '_ \| '_ \| | | | __|                                                                                                   
    | | | | | |_) | |_| | |_                                                                                                    
    |_|_| |_| .__/ \__,_|\__|                                                                                                   
            |_|                                                                                                                 
    ;                                                                                                                           
                                                                                                                                
    data fact ;                                                                                                                 
    input student $ grade_cd gendr_cd;                                                                                          
    cards4;                                                                                                                     
    Mary 3 2                                                                                                                    
    Mike 1 1                                                                                                                    
    Jeff 2 1                                                                                                                    
    John 4 1                                                                                                                    
    Kate 1 2                                                                                                                    
    ;;;;                                                                                                                        
    run;quit;                                                                                                                   
                                                                                                                                
    data dimension ;                                                                                                            
    informat var decode $9.;                                                                                                    
    input var code decode;                                                                                                      
    cards4;                                                                                                                     
    grade_cd 1 Freshman                                                                                                         
    grade_cd 2 Sophmore                                                                                                         
    grade_cd 3 Junior                                                                                                           
    grade_cd 4 Senior                                                                                                           
    gendr_cd 1 Male                                                                                                             
    gendr_cd 2 Female                                                                                                           
    ;;;;                                                                                                                        
    run;quit;                                                                                                                   
                                                                                                                                
    WORK.DIMENSION total obs=6 (very small table)                                                                               
                                                                                                                                
    Obs      VAR     CODE  DECODE                                                                                               
                                                                                                                                
     1     grade_cd    1   Freshman                                                                                             
     2     grade_cd    2   Sophmore                                                                                             
     3     grade_cd    3   Junior                                                                                               
     4     grade_cd    4   Senior                                                                                               
                                                                                                                                
     5     gendr_cd    1   Male                                                                                                 
     6     gendr_cd    2   Female                                                                                               
                                                                                                                                
                                                                                                                                
    WORK.FACT total obs=5               | RULES                                                                                 
                                                                                                                                
                                        |   DECODE_                                                                             
    Obs    STUDENT  GRADE_CD  GENDR_CD  |   GENDR                                                                               
                                        |                                                                                       
     1      Mary        3         2     |   Female ==> GENDER_CD is 2 in fact table                                             
                                                       and maps to Female in the dimension table                                
                                                                                                                                
     2      Mike        1         1     |   Male                                                                                
     3      Jeff        2         1     |   Male                                                                                
     4      John        4         1     |   Male                                                                                
     5      Kate        1         2     |   Female                                                                              
                                                                                                                                
    *            _             _                                                                                                
      ___  _   _| |_ _ __  _   _| |_                                                                                            
     / _ \| | | | __| '_ \| | | | __|                                                                                           
    | (_) | |_| | |_| |_) | |_| | |_                                                                                            
     \___/ \__,_|\__| .__/ \__,_|\__|                                                                                           
                    |_|                                                                                                         
    ;                                                                                                                           
                                                                                                                                
    WORK.WANT total obs=5                                                                                                       
                                                                                                                                
                               DECODE_                  DECODE_                                                                 
    Obs    STUDENT  GRADE_CD    GRADE      GENDR_CD     GENDER                                                                  
                                                                                                                                
     1      Mary        3      Junior          2        Female                                                                  
     2      Mike        1      Freshman        1        Male                                                                    
     3      Jeff        2      Sophmore        1        Male                                                                    
     4      John        4      Senior          1        Male                                                                    
     5      Kate        1      Freshman        2        Female                                                                  
                                                                                                                                
    *          _       _   _                                                                                                    
     ___  ___ | |_   _| |_(_) ___  _ __  ___                                                                                    
    / __|/ _ \| | | | | __| |/ _ \| '_ \/ __|                                                                                   
    \__ \ (_) | | |_| | |_| | (_) | | | \__ \                                                                                   
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|___/                                                                                   
                                                                                                                                
    ;                                                                                                                           
                                                                                                                                
    *_      _               _                                                                                                   
    / |    | |__   __ _ ___| |__                                                                                                
    | |    | '_ \ / _` / __| '_ \                                                                                               
    | |_   | | | | (_| \__ \ | | |                                                                                              
    |_(_)  |_| |_|\__,_|___/_| |_|                                                                                              
                                                                                                                                
    ;                                                                                                                           
                                                                                                                                
    data want;                                                                                                                  
                                                                                                                                
      if 0 then set dimension;                                                                                                  
                                                                                                                                
      * dimemsion hash (lookup table) ;                                                                                         
      if _n_=1 then do;                                                                                                         
         dcl hash h(dataset: 'dimension');                                                                                      
         h.definekey('var','code');                                                                                             
         h.definedata('decode');                                                                                                
         h.definedone();                                                                                                        
      end;                                                                                                                      
                                                                                                                                
      set fact;                                                                                                                 
                                                                                                                                
      h.find(key:'grade_cd',key:grade_cd); /* interesting */                                                                    
      decode_grade=decode;                                                                                                      
                                                                                                                                
      h.find(key:'gendr_cd',key:gendr_cd); /* two sets of keys */                                                               
      decode_gender=decode;                                                                                                     
                                                                                                                                
      drop var code decode;                                                                                                     
                                                                                                                                
    run;quit;                                                                                                                   
                                                                                                                                
    * __                            _                                                                                           
     / _| ___  _ __ _ __ ___   __ _| |_                                                                                         
    | |_ / _ \| '__| '_ ` _ \ / _` | __|                                                                                        
    |  _| (_) | |  | | | | | | (_| | |_                                                                                         
    |_|  \___/|_|  |_| |_| |_|\__,_|\__|                                                                                        
                                                                                                                                
    ;                                                                                                                           
                                                                                                                                
    data havFmt;                                                                                                                
      set dimension;                                                                                                            
      rename var=fmtname                                                                                                        
             code=start                                                                                                         
             decode=label                                                                                                       
      ;                                                                                                                         
    run;                                                                                                                        
    proc format cntlin=havFmt;  *read in the format;                                                                            
    quit;                                                                                                                       
                                                                                                                                
    *now use the formats;                                                                                                       
    data want;                                                                                                                  
      set fact;                                                                                                                 
      gendr_decode = put(gendr_cd,gendr_cd.);                                                                                   
      grade_decode = put(grade_cd,grade_cd.);                                                                                   
    run;                                                                                                                        
                                                                                                                                
    *                                                                                                                           
     _ __ ___   __ _  ___ _ __ ___                                                                                              
    | '_ ` _ \ / _` |/ __| '__/ _ \                                                                                             
    | | | | | | (_| | (__| | | (_) |                                                                                            
    |_| |_| |_|\__,_|\___|_|  \___/                                                                                             
                                                                                                                                
    ;                                                                                                                           
    * macro look can be very fast macros are ememory resident;                                                                  
                                                                                                                                
    data want;                                                                                                                  
                                                                                                                                
       if _n_=0 then do; %let rc=%sysfunc(dosubl('                                                                              
           * load lookup into macro variables;                                                                                  
           data _null_;                                                                                                         
              set dimension;                                                                                                    
              if var="gendr_cd" then call symputx(cats("GEN",code),decode);                                                     
              else call symputx(cats("GRA",code),decode);                                                                       
           run;quit;                                                                                                            
          '));                                                                                                                  
        end;                                                                                                                    
                                                                                                                                
        set fact;                                                                                                               
                                                                                                                                
        grade_decode=symget(cats("GRA",grade_cd));                                                                              
        gendr_decode=symget(cats("GEN",gendr_cd));                                                                              
                                                                                                                                
    run;quit;                                                                                                                   
                                                                                                                                
    *          _                                                                                                                
     ___  __ _| |                                                                                                               
    / __|/ _` | |                                                                                                               
    \__ \ (_| | |                                                                                                               
    |___/\__, |_|                                                                                                               
            |_|                                                                                                                 
    ;                                                                                                                           
                                                                                                                                
    proc sql;                                                                                                                   
                                                                                                                                
     create                                                                                                                     
        table want as                                                                                                           
     select                                                                                                                     
        l.*                                                                                                                     
       ,c.decode as gendr_decode                                                                                                
       ,r.decode as grade_decode                                                                                                
     from                                                                                                                       
       fact as l                                                                                                                
       left join dimension(where=(var="grade_cd")) as c on l.grade_cd=c.code                                                    
       left join dimension(where=(var="gendr_cd")) as r on l.gendr_cd=r.code                                                    
                                                                                                                                
    ;quit;                                                                                                                      
                                                                                                                                
    *    _       _            _                                                                                                 
      __| | __ _| |_ __ _ ___| |_ ___ _ __                                                                                      
     / _` |/ _` | __/ _` / __| __/ _ \ '_ \                                                                                     
    | (_| | (_| | || (_| \__ \ ||  __/ |_) |                                                                                    
     \__,_|\__,_|\__\__,_|___/\__\___| .__/                                                                                     
                                     |_|                                                                                        
    ;                                                                                                                           
                                                                                                                                
    * no need to sort both tables for a datastep solution;                                                                      
    * A merge would require both datasets to be sorted or indexed?;                                                             
    * probably sould do some errir checking _IORC_;                                                                             
                                                                                                                                
    data want;                                                                                                                  
                                                                                                                                
       if _n_=0 then do; %let rc=%sysfunc(dosubl('                                                                              
           proc sql;                                                                                                            
             create unique index varcd on dimension (var, code)                                                                 
           ;quit;                                                                                                               
           '));                                                                                                                 
       end;                                                                                                                     
                                                                                                                                
         length decode var $9;                                                                                                  
                                                                                                                                
         set fact;                                                                                                              
                                                                                                                                
           code=grade_cd;                                                                                                       
           var="grade_cd";                                                                                                      
           set dimension key=varcd;                                                                                             
           grade_decode=decode;                                                                                                 
                                                                                                                                
           code=gendr_cd;                                                                                                       
           var="gendr_cd";                                                                                                      
           set dimension key=varcd;                                                                                             
           gendr_decode=decode;                                                                                                 
           output;                                                                                                              
                                                                                                                                
     run;quit;                                                                                                                  
                                                                                                                                
    *    _                 _     _                                                                                              
      __| | ___  ___ _   _| |__ | |                                                                                             
     / _` |/ _ \/ __| | | | '_ \| |                                                                                             
    | (_| | (_) \__ \ |_| | |_) | |                                                                                             
     \__,_|\___/|___/\__,_|_.__/|_|                                                                                             
                                                                                                                                
    ;                                                                                                                           
                                                                                                                                
    * might be a contender some day?;                                                                                           
    %symdel grade_cd gendr_cd grade_decode gendr_decode;                                                                        
                                                                                                                                
    data want;                                                                                                                  
                                                                                                                                
      set fact;                                                                                                                 
                                                                                                                                
      call symputx("grade_cd",grade_cd);                                                                                        
      call symputx("gendr_cd",gendr_cd);                                                                                        
                                                                                                                                
      rc=dosubl('                                                                                                               
                 proc sql;                                                                                                      
                   select decode into :grade_decode from dimension                                                              
                     where var="grade_cd" and code=&grade_cd;                                                                   
                   select decode into :gendr_decode from dimension                                                              
                     where var="gendr_cd" and code=&gendr_cd;quit                                                               
               ');                                                                                                              
                                                                                                                                
      grade_decode=symget("grade_decode");                                                                                      
      gendr_decode=symget("gendr_decode");                                                                                      
                                                                                                                                
    run;quit;                                                                                                                   
                                                                                                                                
    /*                                                                                                                          
     WANT total obs=5                                                                                                           
                                                                                                                                
                                                GRADE_     GENDR_                                                               
      STUDENT    GRADE_CD    GENDR_CD    RC     DECODE     DECODE                                                               
                                                                                                                                
       Mary          3           2        0    Junior      Female                                                               
       Mike          1           1        0    Freshman    Male                                                                 
       Jeff          2           1        0    Sophmore    Male                                                                 
       John          4           1        0    Senior      Male                                                                 
       Kate          1           2        0    Freshman    Female                                                               
    */                                                                                                                          
                                                                                                                                
