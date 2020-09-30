# cliparser - proxmark3-rrg

## cliparser setup and use

Note: the parser will format and color as needed.

### setup the parser data structure
    CLIParserContext *ctx;

\#include "cliparser.h"

### define the text
CLIParserInit (\<context\>, \<description\>, \<notes\n examples ... \>);

use -> to seperate example and example comment and \\n to seperate examples.
e.g. lf indala clone -r a0000000a0002021 -> this uses .....

    CLIParserInit(&ctx, "lf indala clone",                          
                  "clone INDALA UID to T55x7 or Q5/T5555 tag",      
                  "lf indala clone --heden 888\n"                   
                  "lf indala clone --fc 123 --cn 1337\n"
                  "lf indala clone -r a0000000a0002021\n"
                  "lf indala clone -l -r 80000001b23523a6c2e31eba3cbee4afb3c6ad1fcf649393928c14e5");

### define the options

    void *argtable[] = {
        arg_param_begin,                                                        
        arg_lit0("lL", "long",  "optional - long UID 224 bits"),                
        arg_int0("cC", "heden", "<decimal>", "Cardnumber for Heden 2L format"),  
        arg_strx0("rR", "raw",  "<hex>", "raw bytes"),                          
        arg_lit0("qQ", "Q5",    "optional - specify writing to Q5/T5555 tag"),
        arg_int0("", "fc",      "<decimal>", "Facility Code (26 bit format)"),
        arg_int0("", "cn",      "<decimal>", "Cardnumber (26 bit format)"),
        arg_param_end                                                           
    };
    
**Notes:**  
booleen : arg_lit0 ("\<short option\>", "\<long option\>", \["\<format\>",\] \<"description"\>);  
optional integer : arg_int0 ("\<short option\>", "\<long option\>", \["\<format\>",\] \<"description"\>);  
required integer : arg_int1 ("\<short option\>", "\<long option\>", \["\<format\>",\] \<"description"\>);  
optional string : arg_strx0 ("\<short option\>", "\<long option\>", \["\<format\>",\] \<"description"\>);  
required string : arg_strx1 ("\<short option\>", "\<long option\>", \["\<format\>",\] \<"description"\>);

If an option does not have a short or long option, use NULL
        
### show the menu
CLIExecWithReturn(\<context\>, \<command line to parse\>, \<arg/opt table\>, \<return on error\>);

    CLIExecWithReturn(ctx, Cmd, argtable, false);

### clean up
Once you have extracted the options, cleanup the context.

   CLIParserFree(ctx);

### retreiving options
**bool option**  
arg_get_lit(\<context\>, \<opt index\>);

    is_long_uid = arg_get_lit(ctx, 1);

**int option**  
arg_get_int_def(\<context\>, \<opt index\>, \<default value\>);

    cardnumber = arg_get_int_def(ctx, 2, -1);

**hex option**  
CLIGetHexWithReturn(\<context\>, \<opt index\>, \<store variable\>, \<ptr to stored length\>);
    
    uint8_t aid[2] = {0};
    int aidlen;
    CLIGetHexWithReturn(ctx, 2, aid, &aidlen);

**string option**  
CLIGetStrWithReturn(\<context\>,\<opt index\>, \<unsigned char \*\>, \<int \*\>);

    uint8_t Buffer[100];
    int BufLen;
    CLIGetStrWithReturn(ctx,7, Buffer, &BufLen);

