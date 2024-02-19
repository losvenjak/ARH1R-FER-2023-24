        ORG 0
        B MAIN ;RESET

        ORG 0X18
        B PREKID

MAIN 
        MSR CPSR, #0B11010010 ;IRQ NACIN
        MOV SP, #0X10000

        MSR CPSR, #0B11010011 ;SVC NACIN
        MOV SP, #0XFC00

        ;INICIJALIZACIJA GPIO

        LDR R1, GPIO1
        MOV R0, #0B00000000 ;B VRATA, SVI IZLAZNI
        STR R0, [R1, #12] ;SMJER U PB_DDR

        LDR R2, GPIO2
        MOV R0, #0B11100000 ;A VRATA, DIODE IZLAZNE, TIPKA ULAZNA
        STR R0, [R2, #8] ;SMJER U PA_DDR

        ;INICIJALIZACIJA RTC
        LDR R3, RTC
        MOV R0, #0
        STR R0, [R3, #12] ;LR 0 
        MOV R0, #30
        STR R0, [R3, #4] ;MR 30

        ;DOPUSTI PREKDI U CPSR
        MRS R0, CPSR
        BIC R0, R0, #0B10000000
        MSR CPSR, R0 

STANJE1
        MOV R7, #0
        STR R7, [R3, #16]       ;ZABRANI PREKID U CR
        MOV R0, #0B00000000
        STR R0, [R2, #0] ;SVE DIODE 0 NA GPIO2 VRATA A
        MOV R5, #SPR
        BL ISPIS
        MOV R0, #1
        STR R0, ST      ;POHRANI STANJE 1
POCETAK 
        LDR R0, [R2]
        TST R0, #0B00000001 
        BEQ POCETAK ;CEKAJ TIPKU

ST2     MOV R0, #0B00100000 ;CRVENA - STANJE 2
        STR R0, [R2, #0]
        MOV R5, #PR
        BL ISPIS
        MOV R0, #2
        STR R0, ST

        MOV R6, #0
        STR R6, [R3, #12] ;LR 0 
        STR R6, [R3, #8]        ;MAKNI PREKID
        MOV R0, #1
        STR R0, [R3, #16]       ;DOPUSTI PREKID U CR
CEK 
        LDR R0, [R2] ; TESTIRAJ TIPKU
        TST R0, #0B00000001 
        MOVNE R6, #1 
        LDR R7, ST
        LDR R0, [R3, #12]
        CMP R0, #0
        BEQ VAN
        B CEK

VAN     CMP R6, #0 
        BNE ST6 
        CMP R7, #7
        BEQ STANJE1
        B CEK

ST6
        MOV R6, #0
        STR R6, [R3, #12] ;LR 0 
        STR R6, [R3, #8]        ;MAKNI PREKID
        MOV R0, #1
        STR R0, [R3, #16]       ;DOPUSTI PREKID U CR
        MOV R0, #6
        STR R0, ST      ;POHRANI STANJE 6

        MOV R0, #0B00100000 ;CRVENA - STANJE 6
        STR R0, [R2, #0]
        MOV R5, #GRES
        BL ISPIS
        MOV R4, #2
CEK6        
        LDR R7, ST
        LDR R0, [R3, #12]
        CMP R0, #0
        BEQ VAN6
        B CEK6
VAN6     
        CMP R7, #8
        BEQ STANJE1
        B CEK6

ISPIS   STMFD SP!, {LR, R0}
        MOV R0, #0X0D ;BRISI ULAZNI REGISTAR
        BL LCDWR
LOOP    LDRB R0, [R5], #1 ;UCITAJ SLOVO IZ STRINGA
        CMP R0, #0
        BLNE LCDWR
        BNE LOOP
        MOV R0, #0X0A
        BL LCDWR
        LDMFD SP!, {LR, R0}
        MOV PC, LR

LCDWR   STMFD SP!, {R0}

        AND R0, R0, #0B01111111 ;WR 0
        STRB R0, [R1, #4]
        ORR R0, R0, #0B10000000 ;IMPULS
        STRB R0, [R1, #4]
        ORR R0, R0, #0B00000000
        STRB R0, [R1, #4]
        LDMFD SP!, {R0}
        MOV PC, LR

GPIO1   DW 0XFFFF0F00
GPIO2   DW 0XFFFF0B00
RTC     DW 0XFFFF0E00

PREKID  STMFD SP!, {LR, R0}
        MOV R0, #0
        STR R0, [R3, #8] ;BRISI SPREMNOST
        STR R0, [R3, #12]

        LDR R0, ST
        CMP R0, #2
        BEQ ST3
        CMP R0, #3
        BEQ ST4
        CMP R0, #4
        BEQ ST5
        CMP R0, #5
        BEQ BLNEP
        CMP R0, #6
        BEQ BL6

ST3     MOV R0, #0B01100000 ;CRVENA I ZUTA - STANJE 3
        STR R0, [R2, #0]
        MOV R0, #3
        STR R0, ST
        B KRAJ

ST4     MOV R0, #0B11100000 ;CRVENA I ZUTA I ZELENA - STANJE 4
        STR R0, [R2, #0]
        MOV R0, #4
        STR R0, ST
        B KRAJ

ST5     MOV R4, #1 ;BROJAC BLINKA
        MOV R5, #GOT
        BL ISPIS
        MOV R0, #5
        STR R0, ST
        B BLNEP

BLNEP   TST R4, #0B00000001
        BEQ BLPAR
        CMP R4, #7
        MOVEQ R0, #7
        STREQ R0, ST
        BEQ KRAJ
        ADD R4, R4, #1        
        MOV R0, #0B11100000 ;CRVENA I ZUTA I ZELENA 
        STR R0, [R2, #0]
        B KRAJ

BLPAR   MOV R0, #0B00000000 ;NISTA 
        STR R0, [R2, #0]
        ADD R4, R4, #1
        B KRAJ

BL6     TST R4, #0B00000001
        BEQ BLPAR
        CMP R4, #7
        MOVEQ R0, #8
        STREQ R0, ST
        BEQ KRAJ
        ADD R4, R4, #1      
        MOV R0, #0B00100000 ;CRVENA
        STR R0, [R2, #0]
        B KRAJ

KRAJ    LDMFD SP!, {LR, R0}
        SUBS PC, LR, #4

ST      DW 0    ;TRENUTNO STANJE

SPR     DSTR "SPREMNO"
PR      DSTR "PRIPREMA"
        DW 0
GOT     DSTR "GOTOVO"
        DW 0
GRES    DSTR "GRESKA"