        MOV SP, #0x10000
        MOV R0, #0x600
        MOV R5, #0x2000
        LDR R4, ZADNJI

LOOP    LDR R1, [R0], #4
        LDR R2, [R0], #4
        LDR R3, [R0], #4
        CMP R3, R4
        BEQ KRAJ

        CMP R3, #0x00000000
        SUBEQ R1, R1, R2
        BEQ UPISI

        CMP R3, #0x00000001
        ADDEQ R1, R1, R2
        BEQ UPISI
        
        CMP R3, #0x00000002
        MULEQ R1, R2, R1
        BEQ UPISI

        CMP R3, #0x00000003
        BNE KRAJ
        BL PREDZN
        SUB SP, SP, #4
        STMFD SP!, {R1, R2}
        BL DIJELI
        ADD SP, SP, #8
        LDMFD SP!, {R1}
        TST R7, #0B10000000
        MVNNE R1, R1
        ADDNE R1, R1, #1 
        B UPISI

KRAJ    STR R3, [R5]
        SWI 123456

PREDZN  STMFD SP!, {R6}
        MOV R7, #0
        ANDS R6, R1, #0X80000000
        MVNNE R1, R1
        ADDNE R1, R1, #1
        EOR R7, R7, R6
        ANDS R6, R2, #0X80000000
        MVNNE R2, R2
        ADDNE R2, R2, #1
        EOR R7, R7, R6
        LDMFD SP!, {R6}
        MOV PC, LR

DIJELI  STMFD SP!, {R1, R2, R3}        
        LDR R1, [SP, #12]
        LDR R2, [SP, #16]
        MOV R3, #0
LOOP2   SUB R1, R1, R2
        ADD R3, R3, #1
        CMP R1, R2
        BHS LOOP2
        STR R3, [SP, #20]
        LDMFD SP!, {R1, R2, R3}
        MOV PC, LR 

UPISI   STR R1, [R5], #4
        B LOOP

        ORG 0x600
        DW 0XFFFFFEFF
        DW 0x00000010
        DW 0x00000003

        DW 0x000001F4
        DW 0xFFFFFD44
        DW 0x00000000

        DW 0x00000003
        DW 0xFFFFFFEC
        DW 0x00000001

        DW 0xFFFFFFFE
        DW 0x0000000A
        DW 0x00000002

        DW 0xFFFFF000
        DW 0xFFFFFFC0
        DW 0x00000003

        DW 0x00000001
        DW 0x00000004
        DW 0xFFFFFFFF

ZADNJI  DW 0xFFFFFFFF

        ORG 0x2000
REZ     DW 0
        DW 0
        DW 0
        DW 0
        DW 0
        DW 0