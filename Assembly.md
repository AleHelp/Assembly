
_Nell' assembly utilizziamo i registri, piccole componenti, estramamente veloci ma che contengono pochi bytes, i quali sono:_

-rax,rbx,rcx,rdx,r8,r9,r10 chiamati pure data registers a 64bit , di solito immagazzinano istruzioni e argomenti di syscalls.

_il registro a 64 bit a sotto di lui altri 3 più piccoli:_
  - rax 64 bit

    - eax 32 bit

      - ax 16 bit

        - al 8bit

-rbp,rsp,rip chiamati registri puntatori, rbp punta alla base dello stack, rsp punta alla locazione corrente nello stack e rip punta all'indirizzo della prossima istruzione

## Tipi di accessi alla memoria e endianness:

- Immediato = add 3
- Tramite registro = add rax
- Diretto con l'indirizzo specifico di memoria = call 0xffffffaa83ff 
- Indiretto utilizzano un registro = call 0x44d000, call[rax]
- Stack = add rsp

Indirizzo Esempio: 0x0011223344556677---->alla pozione 77 abbiamo il least significant byte
                        |
                        |-->alla posizione 00 abbiamo il most significant byte
  
___Little-Endian__ ci riferiamo al modo di immagazzianre o recuperare i bytes, se abbiamo un indirizzo come 0x0011223344556677 
alla fine con il little sarà cosi 0x7766554433221100, seguendo la logica primo a destra per continuare a sinistra cioè __dal least significant to il most significant___

___Big-Endian__ invece nel big l'indirizzo rimarrà uguale cioè 0x0011223344556677 perchè va da sinistra a destrà cioè dal __most significant byte al least significant byte___

## Data types:

La grandenza dei dati cambia a seconda dell'architettura però nella x86 segue quanto:

  - byte = 8 bits = 0xv

  - word = 16 bits = 0xvbf

  - dword (double-word) = 32 bits = 0x1perlsjf

  - qword (quad-word) = 64 bits = 0xabcdef1234567890

Da una determinata grandezza in bits o byte è anche corretto utilizzare i registri corretti:

- byte = al

- word = ax

- dword = eax

- qword = rax

## Struttura di un file assembly:

   global  _start ---> __inizio del codice da dove deve eseguirsi__

   section .data ----> __dove sono contenuto le variabili che poi verrano caricate nel data segment, è read and write__
   
message: db      "Hello Word!" ---> ___Abbiamo il _message_ che è una semplice etichetta, _DB_ che definisce il tipo di varibile e indfine il contenuto: _"Hello World"_ __

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

Nel codice sopra possiamo notare che è stato dichiarato db (data bytes) è utilizzato per dichiarare una lista di bytes, che prendono la grandezza a seconda del valore scritto, inoltre abbiamo
pure dw (data words) che dichiara una lista di words, dd (data digits) che dichiara una lista di cifre e infine equ (equates) per 
