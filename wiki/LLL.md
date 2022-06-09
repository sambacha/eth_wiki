### LLL este limbajul Ethereum PoC  asemanator cu limbajul Lisp. 

Este asemanator cu Lisp mai degraba in sintaxa decat in privinte ce tin de substraturi si este creat pentru a face mai usoara scrierea unui program in cod EVM 1, aka ES-1. Este compilat automat in seriile PoC ale Ethereum, incluzand partea superioara PoC-3. 
Un contract scris in LLL ia forma unei singure expresii. O expresie poate fi una din:
  * Un sir citat ex. "Hello world!" or "This is \"Great\"".  Asemenea siruri au maxim 32 de caractere si sunt evaluate la valoarea de 32 byte cu codarea ASCII a sirului aliniata in partea stanga(i.e. in bytes de ordin inalt ar trebui interpretate ca numere intregi endiene). 
  * Un numar intreg, cu un prefix optional 0x pentru hex base si cu un sufix care poate fi orice denumire standard Ethereum e.x. 69, 0x42 or 3finney.
  * O expresie executata care ia forma unei liste de expresii parenthesised ex. (add 1 2), cu prima expresie din lista fiind operatia iar restul operanzii. 
  * Toate instructiunile din specificatiile codului EMV-1 sunt valide, desi in general nu este necesara utilizarea stack manipulation si jump operations. Operanzii trebuie atribuiti in ordine din varful stack-ului , descrescator.

Aditional, sunt furnizate mai multe operatiuni de control al fluxului:
  * (if PRED Y N) executa PRED, apare stack-ul si executa Y daca valoarea aparuta este nu este zero, altfel N.
  * (when PRED BODY) executa PRED, apare stack-ul si executa BODY daca valoarea aparuta nu este zero.
  * (unless PRED BODY)executa PRED, apare stack-ul si executa BODY daca valoarea aparuta este zero.
  * (for PRED BODY) executa PRED, apare stack-ul si executa BODY daca valoarea aparuta nu este zero inainte sa repete totul de la inceput pe o durata de timp nelimitata.
  * (seq A B ...) executa expresiile ulterioare in ordine.
Și două forme prescurtate pentru depozitarea și încărcarea in stocarea permanenta:
  * (INT), (STRING) trateaza valoarea ca o adresa si aduce in varful stack-ului valoarea din stocare  (i.e. executa un PUSH & SLOAD)
  * (INT EXPR), (STRING EXPR) trateaza prima valoare ca o adresa la fel ca mai sus si depoziteaza  EXPR in stocare la acea adresa.

Este ineficient, dar in general se pot utiliza ultimele doua ca un fel de inlocuitor pentru variabilele globale. Setati  i la  0 cu ("i" 0) si cititi din nou cu ("i").

Deasemenea exista  and si  or pentru construirea conditiilor booleanfor (sar peste evaluarea ulterioara, argumente care nu sunt necesare in C) si pot fi folosite pentru orice numar diferit de zero de argumente. Operatorii multi-ary +, -, *, / si %  pot fi folositi, impreuna cu operatorii binari <, <=, >,>=, = and != si cu operatorii unary !. e.g. (and 0 (= (+ 2 2 4) 8)) vor evalua la (i.e. leave atop the stack) 0 fara sa evalueze egalitatea. 

Veti dori, in general , sa anexati programele cu (seq ...) astfel incat sa puteti executa mai mult decat o singura expresie.

Finalmente, orice comentarii ar trebui sa inceapa cu a ;, dupa care toate textele vor fi ignorate pana la sfarsitul liniei.

Vedeti  LLL Examples pentru exemple si  LLL Tuturial pentru un tutorial.
