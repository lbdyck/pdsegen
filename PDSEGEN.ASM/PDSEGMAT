PDSEGMAT TITLE 'MATCH Wildcard source/pattern Rexx Function'            00010007
* ------------------------------------------------- *                   00020007
* Routine wirtten and provided by Salvador Carrasco *                   00030007
* under the name IRXFMAT2 and renamed to PDSEGMAT   *                   00040007
* for use with PDSEGEN. No other changes were made. *                   00050007
*                                                   *                   00060007
* Recommendation: Place this into LPA for better    *                   00070007
*                 performance.                      *                   00080007
*                                                   *                   00090007
* Linkedit with RENT, REUS                          *                   00100007
*                                                   *                   00110007
* Updated 11/13/2017 by Salvador to enhance checking*                   00120007
* ------------------------------------------------- *                   00130007
PDSEGMAT RSECT                                                          00140007
PDSEGMAT AMODE 31                                                       00150007
PDSEGMAT RMODE ANY                                                      00160007
         YREGS                                                          00170007
*---------------------------------------------------------------------- 00180007
* HOUSEKEEPING                                                          00190007
*        R15 - Work                                                     00200007
*        R14 - Rexx EvalBlock                                           00210007
*        R13 - Saver chain                                              00220007
*        R12 - Base                                                     00230007
*        R11 - Rexx EFPL                                                00240007
*        R10 - Rexx EFPLARG                                             00250007
*        R9  - Work                                                     00260007
*        R8  - Work                                                     00270007
*        R7  - MP                                                       00280007
*        R6  - CP                                                       00290007
*        R5  - Pattern                                                  00300007
*        R4  - Pattern length                                           00310007
*        R3  - Source                                                   00320007
*        R2  - Source Length                                            00330007
*        R1  - n/a                                                      00340007
*        R0  - n/a                                                      00350007
*---------------------------------------------------------------------- 00360007
         SAVE  (14,12),,'PDSEGMAT MATCH SOURCE/PATTERN'                 00370007
         LR    R12,R15                                                  00380007
         USING PDSEGMAT,R12                                             00390007
         LR    R11,R1                                                   00400007
         USING EFPL,R11                                                 00410007
         L     R10,EFPLARG                                              00420007
         USING ARGTABLE_ENTRY,R10                                       00430007
         L     R14,EFPLEVAL                                             00440007
         L     R14,0(R14)                                               00450007
         USING EVALBLOCK,R14                                            00460007
*---------------------------------------------------------------------- 00470007
* arg(1) Source                                                         00480007
*---------------------------------------------------------------------- 00490007
         L     R3,ARGTABLE_ARGSTRING_PTR           Source Pointer       00500007
         LTR   R3,R3                                                    00510007
         JM    EXITBAD                                                  00520007
         L     R2,ARGTABLE_ARGSTRING_LENGTH        Source length        00530007
*---------------------------------------------------------------------- 00540007
* arg(2) Pattern                                                        00550007
*---------------------------------------------------------------------- 00560007
         LA    R10,ARGTABLE_NEXT                                        00570007
         L     R5,ARGTABLE_ARGSTRING_PTR           Pattern Pointer      00580007
         LTR   R5,R5                                                    00590007
         JM    EXITBAD                                                  00600007
         L     R4,ARGTABLE_ARGSTRING_LENGTH        Pattern length       00610007
*-Pseudocode----------------------------------------------------------- 00620007
* ARG STRING,PATTERN                                                    00630007
* WILD = 1                                                              00640007
* STR = 1                                                               00650007
* CP = 1                                                                00660007
* MP = 1                                                                00670007
*---------------------------------------------------------------------- 00680007
         LR    R6,R3               CP                                   00690007
         LR    R7,R5               MP                                   00700007
         LR    R8,R3                                                    00710007
         AR    R8,R2               END OF STRING                        00720007
         LR    R9,R5                                                    00730007
         AR    R9,R4               END OF PATTERN                       00740007
*-Pseudocode----------------------------------------------------------- 00750007
* DO WHILE(STR <= LENGTH(STRING) & SUBSTR(PATTERN,WILD,1) *= '*')       00760007
*    IF SUBSTR(PATTERN,WILD,1) *= SUBSTR(STRING,STR,1) &,               00770007
*       SUBSTR(PATTERN,WILD,1) *= '%' THEN RETURN -1                    00780007
*    WILD = WILD + 1                                                    00790007
*    STR = STR + 1                                                      00800007
*    END                                                                00810007
*---------------------------------------------------------------------- 00820007
LOOP1    CR    R3,R8                                                    00830007
         JNL   LOOP2                                                    00840007
         CLI   0(R5),C'*'                                               00850007
         JE    LOOP2                                                    00860007
         CLC   0(1,R5),0(R3)                                            00870007
         JE    LOOP1C                                                   00880007
         CLI   0(R5),C'%'                                               00890007
         JNE   FALSE                                                    00900007
LOOP1C   LA    R5,1(R5)                                                 00910007
         LA    R3,1(R3)                                                 00920007
         CR    R5,R9                                                    00930007
         JL    LOOP1                                                    00940007
         CR    R3,R8                                                    00950007
         JNL   TRUE                                                     00960007
         J     FALSE                                                    00970007
*-Pseudocode----------------------------------------------------------- 00980007
* DO WHILE(STR <= LENGTH(STRING))                                       00990007
*---------------------------------------------------------------------- 01000007
LOOP2    CR    R3,R8                                                    01010007
         JNL   LOOP3                                                    01020007
*-Pseudocode----------------------------------------------------------- 01030007
*   IF SUBSTR(PATTERN,WILD,1) = '*' THEN DO                             01040007
*      WILD = WILD+1                                                    01050007
*      IF WILD > LENGTH(PATTERN) THEN RETURN 1                          01060007
*      MP = WILD                                                        01070007
*      CP = STR                                                         01080007
*      END                                                              01090007
*---------------------------------------------------------------------- 01100007
         CR    R5,R9                                                    01110007
         JNL   LOOP22                                                   01120007
         CLI   0(R5),C'*'                                               01130007
         JNE   LOOP21                                                   01140007
         LA    R5,1(R5)                                                 01150007
         CR    R5,R9                                                    01160007
         JNL   TRUE                                                     01170007
         LR    R7,R5                                                    01180007
         LR    R6,R3                                                    01190007
         J     LOOP2                                                    01200007
*-Pseudocode----------------------------------------------------------- 01210007
*   ELSE IF SUBSTR(PATTERN,WILD,1)=SUBSTR(STRING,STR,1) |,              01220007
*           SUBSTR(PATTERN,WILD,1)='%' THEN DO                          01230007
*        WILD = WILD + 1                                                01240007
*        STR = STR + 1                                                  01250007
*        END                                                            01260007
*---------------------------------------------------------------------- 01270007
LOOP21   CLC   0(1,R5),0(R3)                                            01280007
         JE    LOOP211                                                  01290007
         CLI   0(R5),C'%'                                               01300007
         JNE   LOOP22                                                   01310007
LOOP211  LA    R5,1(R5)                                                 01320007
         LA    R3,1(R3)                                                 01330007
         J     LOOP2                                                    01340007
*-Pseudocode----------------------------------------------------------- 01350007
*   ELSE DO                                                             01360007
*      WILD = MP                                                        01370007
*      CP = CP+1                                                        01380007
*      STR = CP                                                         01390007
*      END                                                              01400007
*   END                                                                 01410007
*---------------------------------------------------------------------- 01420007
LOOP22   LR    R5,R7                                                    01430007
         LA    R6,1(R6)                                                 01440007
         LR    R3,R6                                                    01450007
         J     LOOP2                                                    01460007
*-Pseudocode----------------------------------------------------------- 01470007
* DO WHILE(SUBSTR(PATTERN,WILD,1)='*')                                  01480007
*    WILD = WILD + 1                                                    01490007
*    END                                                                01500007
*---------------------------------------------------------------------- 01510007
LOOP3    CLI   0(R5),C'*'                                               01520007
         JNE   END                                                      01530007
         LA    R5,1(R5)                                                 01540007
         J     LOOP3                                                    01550007
*-Pseudocode----------------------------------------------------------- 01560007
* IF WILD > LENGTH(PATTERN) THEN RETURN 1                               01570007
* ELSE RETURN -1                                                        01580007
*---------------------------------------------------------------------- 01590007
END      CR    R5,R9                                                    01600007
         JNL   TRUE                                                     01610007
FALSE    MVI   EVALBLOCK_EVDATA,C'0'                                    01620007
         J     RETURN                                                   01630007
TRUE     MVI   EVALBLOCK_EVDATA,C'1'                                    01640007
RETURN   MVC   EVALBLOCK_EVLEN,=F'1'                                    01650007
         SR    R15,R15                                                  01660007
RETURN2  ST    R15,16(R13)                                              01670007
         LM    R14,R12,12(R13)                                          01680007
         BSM   0,R14                                                    01690007
EXITBAD  LA    R15,16                                                   01700007
         J     RETURN2                                                  01710007
*---------------------------------------------------------------------- 01720007
* Used Maps                                                             01730007
*---------------------------------------------------------------------- 01740007
DUMMY    DSECT                                                          01750007
         IRXEFPL                                                        01760007
         IRXARGTB                                                       01770007
         IRXEVALB                                                       01780007
         END   PDSEGMAT                                                 01790007
