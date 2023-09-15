
_Nell' assembly utilizziamo i registri, piccole componenti, estramamente veloci ma che contengono pochi bytes, i quali sono:_

-rax,rbx,rcx,rdx,r8,r9,r10 chiamati pure data registers a 64bit , di solito immagazzinano istruzioni e argomenti di syscalls.

_il registro a 64 bit a sotto di lui altri 3 più piccoli:_
  - rax 64 bit

    - eax 32 bit

      - ax 16 bit

        - al 8bit

- rbp,rsp,rip chiamati registri puntatori, rbp punta alla base dello stack, rsp punta alla locazione corrente nello stack e rip punta all'indirizzo della prossima istruzione

## Tipi di accessi alla memoria e endianness:

- Immediato = add 3
- Tramite registro = add rax
- Diretto con l'indirizzo specifico di memoria = call 0xffffffaa83ff 
- Indiretto utilizzano un registro = call 0x44d000, call[rax]
- Stack = add rsp

Indirizzo Esempio: 
0x0011223344556677

alla posizione 77 abbiamo il least significant byte
                       
alla posizione 00 abbiamo il most significant byte
  
___Little-Endian__ ci riferiamo al modo di immagazzianre o recuperare i bytes, se abbiamo un indirizzo come 0x0011223344556677 
alla fine con il little sarà cosi 0x7766554433221100, seguendo la logica primo a destra per continuare a sinistra cioè __dal least significant to il most significant___

___Big-Endian__ invece nel big l'indirizzo rimarrà uguale cioè 0x0011223344556677 perchè va da sinistra a destrà cioè dal __most significant byte al least significant byte___

## Data types:

La grandenza dei dati cambia a seconda dell'architettura però nella x86 segue quanto:

  - byte = 8 bits = 0xa

  - word = 16 bits = 0xab

  - dword (double-word) = 32 bits = 0x1a3fcs

  - qword (quad-word) = 64 bits = 0xabcdef1234567890

Da una determinata grandezza in bits o byte è anche corretto utilizzare i registri corretti:

- byte = al

- word = ax

- dword = eax

- qword = rax

## Struttura di un file assembly:

   global  _start ---> __inizio del codice da dove deve eseguirsi__

   section .data ----> __dove sono contenuto le variabili che poi verrano caricate nel data segment, è read and write__
   
message: db      "Hello Word!" ---> __Abbiamo il _message_ che è una semplice etichetta, _DB_ che definisce il tipo di varibile e indfine il contenuto: _"Hello World"___

   section .text ----> __dove è contenuto il codice da eseguire, nella memoria è solo read only__
_start:
   mov     rax, 1
   mov     rdi, 1
   mov     rsi, message
   mov     rdx, 18
   syscall

   mov     rax, 60
   mov     rdi, 0
   syscall

_Approfondimenti o appunti:_

Nel codice sopra possiamo notare che è stato dichiarato db (data bytes) è utilizzato per dichiarare una lista di bytes, che prendono la grandezza a seconda del valore scritto, inoltre abbiamo pure dw (data words) che dichiara una lista di words, dd (data digits) che dichiara una lista di cifre e infine equ (equates) per definire una costante.

## Assembling, linking e Disasssembling

_I file assembly hanno estensione .s o .asm, dopo averli creati vanno assemblati e fatto il linking delle dipendenze con i seguenti commandi:_
<!-->
       nasm -f elf64 helloWorld.s #comando per assemblare un elf64
<!-->
       ld -o helloWorld helloWorld.o #comadno per avere l'eseguibile
<!-->
_Il dissasembling è la pratica di smontare ELF appena creato e verrà utilizzato objdump con i seguenti comandi:_
<!-->
        objdump -M intel -d helloWorld #comando per disassemblare, con -m specifichiamo la sintassi intel
<!-->
        bjdump -M intel --no-show-raw-insn --no-addresses -d helloWorld #comando con cui vediamo solo il codice assembly
<!-->
        objdump -sj .data helloWorld #comando che estrae le strings datta .data section
<!-->
_Commandi GDB-gef:_
<!-->
       sudo apt-get update
       sudo apt-get install gdb
       wget -O ~/.gdbinit-gef.py -q https://gef.blah.cat/py
       echo source ~/.gdbinit-gef.py >> ~/.gdbinit #comandi per installare e settupare gdb con plugin di gef
<!-->
        gdb -q <nome eseguibile> #comando per disassemblare
<!-->
        info functions #comando per vedere le funzioni dichiarate
<!-->
        info variables #comando per avere info sulle variabili
<!-->
        disas <nome funzione>  #comando per disassemblare la funzione
<!-->
        b <nome funzione o indirizzo di memoria> #possiamo settare il breakpoint
<!-->
        b *<nome funzione>+<offset> #setting del breakpoint partendo dalla funzione + offset l'asterisco è il break
<!-->
        registers #comando per stampare valori dei registri
<!-->
ES:
b *_start+18
<!-->
        b *<indirizzo di memoria> #setting del breakpoint a uno specifico indirizzo
<!-->
ES:
b *0x40130b
<!-->
        r o run #per avviare il debugger
<!-->
        stepi o si / si + <num istruzioni da muoversi>> #comando per muoversi una linea per volta o aggiungendo un numero per più istruzioni
<!-->
        step o s  #comando che si muove fino alla fine
<!-->
        c o continue #per quando si settuano più breakpoint uno dietro l'altro
<!-->
        info breakpoint #commando per avere una lista e delle info sui breakpoint
<!-->
        enable/disable/delete <numero breakpoint> #comandi possibili da eseguire sui breakpoint
<!-->
_Comandi per esaminare:_

_In gdb per esaminare viene utilizzato il comando __x__ nella notazione __"x/FMT"__, in poche parole gli argomenti che prende in ordine dopo lo slash sono Count,Format e Size:

- Count =  quante volte ripetere aka quante istruzioni o altri dati vuoi vedere

- Format = cosa vuoi vedere, tipo __i__ per le istruzioni o __x__ per i valori esadecimali

- Size = grandezza della memoria da esaminare

ES:
<!-->
    x/s <indirizzo memoria> #comando per leggere una stringa
<!-->
    x/7ig $rip #comando che partendo dal rip legge le prossime 7 istruzioni
<!-->
    x/wx #comando per esaminare valore dei registri o indirizzi
<!-->

_Comandi per modificare i valori:_ 
<!-->
      patch <tipo di dato o grandezza> <indirizzo di memoria< <valore> 
<!-->
      set $<registro>=>valore> #comando per modificare il registro
<!-->
__N.B se notiamo indirizzi come 0x0000'40010 invece del solito 0xfffff'121fa è perchè nell'eseguibile è attivo il Position-Independent Executables, in cui gli indirizzi di memoria cambiano ogni volta in quanto verrà caricato casualmente._

# Istruzioni per il movimento dei dati

- MOV = muove o carica un valore = mov rax, 4 rax = 4

- LEA (Load Effective Address) = carica un'indirizzo = lea rax, [rsp + 6]

- XCHG = muovi i dati tra due registri o indirizzi = XCHG rax, rbx

_Moving pointer values:_

avendo un registro rsp con questo valore:

$rsp   : 0x00007fffffffe490  →  0x0000000000000001 

possiamo dire che sta facenco il puntatore a un indirizzo che contiene il valore __1__ piuttosto che averlo effettivamente, se vogliamo per qualsiasi motivo accedere a quel valore o all'indirizzo sono due gli accorgimenti:
<!-->
        mov rax, rsp #cosi otteniamo l'indirizzo contenuto cioè 0x00007fffffffe490
<!-->
        mov rax, [rsp] #chiamato anche load value at address caricheremo in rax il valore 1, con le quadre di solito va specificato la size es word o dword
<!-->
        mov rcx , [rsp + 10] #mi sposto di 10 byte quindi 10 indirizzi verso il basso nello stack, in questo caso stiamo facendo un'address offset
<!-->
        lea rax, [rsp] #comando che copiava l'indirizzo puntato da rsp in rax
<!-->
# Aritmetica e bitwise

_rax e rbx supponiamo che siano settati ad 1_

- INC = incrementa di 1 = inc rax = rax++

- DEC = decrementa di 1 = dec rax = rax--

- add = fa la somma = add rax, rbx quindi rax = 1+1 = 2

- sub = fa la differenza (rax = rax-rbx) = 0

- imul = fa la moltiplicazione = imul rax, rbx quindi rax = 1+1 = 1

_Ora supponiamo di avere il registro rax = 1 e rbx = 2_

- not = inverte tutti i bit (0 diventa 1 e 1 diventa 0) = not rax = 00000001 = 11111110

- and = (se tutti i bit sono 1 allora = 1 sennò il resto è 0) = and rax, rbx = 00000001 and 00000010 = 00000000

- or = (se un bit è 1 allora il resto è 1 il resto rimane 0) = or rax, rbx = 00000001 or 00000010 = 00000011

- xor = (se i bit sono uguali allora 0 se diversi allora 1) = xor rax, rbx = 00000001 xor 00000010 = 00000011

# Loop:

_I Loop sono utili per la ripetizione di un'istruzione per un numero di volte_

- _Comandi:_
<!-->
      mov rcx, 3 #comando per muovere il valore 2 al registro rcx che fa da contatore
<!-->
      loop <nome funzione o num indirizzo da ripetere> #comando per loopare
<!-->
# Istruzioni di salto senza condizione e con:

_Le istruzioni di salto permettono di andare in qualsiasi punto del nostro codice che vogliamo, questi salti possono essere liberi cioè avvengono per qualsiasi circostanza oppure avvengono dopo una determinata condizione da soddisfare_

- _Comandi:_

      jmp <funzione o indirizzo di memoria> #comando che server per fare un jump incodizionato cioè che il salto avviene in ogni caso
