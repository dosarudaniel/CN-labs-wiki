Laboratorul 01 - Introducere în Verilog
---------------------------------------

În acest laborator vom învăța primele noțiuni de Verilog, un limbaj de descriere hardware. Îl vom folosi pe parcursul laboratorului pentru a exemplifica și implementa noțiuni legate de arhitectura calculatoarelor.

### Ce este Verilog?

**Verilog** este un **HDL** (Hardware Description Language) folosit pentru specificarea formală a circuitelor electronice digitale. Alte limbaje similare sunt:

*   VHDL
    
*   AHPL
    
*   SystemVerilog
    

Un asemenea limbaj este utilizat pentru descrierea sistemelor digitale, de exemplu un calculator sau o componentă a acestuia.

#### Verilog is not C!

…nor is it Pascal, C#, Java, (any kind of) Assembly, Lisp, Haskell, Prolog, PHP, etc.

Din punct de vedere sintactic, Verilog se aseamănă cu limbaje procedurale precum C, dar trebuie să ținem cont de faptul că instrucțiunile **nu sunt executate secvențial**, ca pe un procesor. În timp ce target-ul unui limbaj de programare uzual este un procesor, target-ul unui cod scris în Verilog poate fi un **FPGA** sau un **ASIC** (Application Specific Integrated Circuit).

Un procesor execută o secvență de instrucțiuni, distribuind semnale de control și date diferitelor componente aritmetice și logice. Spre deosebire, un FPGA va cupla diferite componente interne pentru a simula o componentă aritmetică sau logică. În esență, un FPGA este pur și simplu o matrice de porți logice, legăturile dintre ele fiind configurabile. FPGA-urile reprezintă o platformă de dezvoltare pentru cei care doresc să construiască un circuit integrat de la zero. Prin reconfigurarea porților logice, pe un FPGA se poate construi orice în domeniul circuitelor digitale, de la drivere de LED-uri si LCD-uri, până la întregi chipset-uri și chiar procesoare. Mai multe detalii despre FPGA-uri vom învăța într-un laborator viitor.

#### De ce Verilog?

Un limbaj precum Verilog conține o serie de abstractizări sau moduri de a genera, prin intermediul codului, porți logice. În comparație cu a proiecta _“de mână”_ circuitele integrate, tocmai aceste abstractizări sunt cele care au permis electronicii digitale să se dezvolte în ritm rapid odată cu progresul tehnologiei de fabricație. Cu ajutorul lor putem descrie relativ ușor structuri complexe, divizându-le în componentele lor comune și de bază.

Însă apare întrebarea naturală: _Ce aș putea face cu un FPGA și nu aș putea face cu un procesor?_ Pe scurt, există trei răspunsuri:

*   Un FPGA poate fi reconfigurat într-un timp foarte scurt. Asta înseamnă că, dacă am greșit ceva în design-ul nostru, dacă dorim să îl modificăm sau să îl extindem, timpul și costul acestei acțiuni sunt foarte mici.
    
*   Un FPGA, prin construcția lui, oferă un grad extrem de ridicat de paralelism, lucru pe care codul scris pentru un procesor (deci cod secvențial) nu îl oferă (cel puțin nu la fel de usor și controlabil).
    
*   Un FPGA este de preferat oricând se dorește interfațarea unui dispozitiv (un senzor, un dispozitiv de afișare, etc.) care are nevoie de timpi foarte stricți in protocolul de comunicație (exemplu: așteaptă 15 nanosecunde înainte să comuți linia de ceas, apoi activează linia de enable pentru 25 de nanosecunde, apoi pune datele pe linia de date și ține-le cel putin 50 de nanosecunde, etc). Pe un procesor acest lucru este iarăși dificil de controlat, fiindcă majoritatea instrucțiunilor se execută într-un număr diferit de cicli de ceas.
    

### Structura limbajului Verilog

Atunci când proiectăm un circuit digital folosind un HDL, începem prin a face o descriere textuală a circuitului, adică scriem cod. Acesta este compilat, iar în urma procesului va rezulta un model al circuitului care poate fi apoi rulat într-un **simulator** cu scopul de a verifica funcționalitatea descrierii. O alternativă la simulare este folosirea unui utilitar de **sintetizare**, care preia codul HDL și generează fișiere de configurare pentru FPGA.

#### Module

Unitatea de bază a limbajului Verilog este **modulul**, ce conține descrierea interfeței și a comportamentului unui circuit electronic. Un modul este cel mai apropiat de conceptul de “black box” ce constă în cunoașterea intrărilor și a ieșirilor, fără a ști însă detaliile de implementare și modul în care el funcționează.

În Verilog nu putem să “apelăm” un modul pentru a executa o acțiune. Spre exemplu, nu putem să apelăm un modul sumator pentru a face suma a două numere. Ce putem face însă este să instanțiem un sumator, să îi legăm ca și intrări cele două numere și vom ști că la ieșirea sumatorului vom avea suma lor. **Mereu**.

##### Declarație

Declararea unui modul se face, generic, în felul următor:

module <nume\_modul\>(
    <tip\_port\_1\> <nume\_port\_1\>,
    <tip\_port\_2\> <nume\_port\_2\>,
    ...
    <tip\_port\_n\> <nume\_port\_n\>
    );
 
// Implementarea modulului.
 
endmodule

Această declarație constă în:

*   Cuvântul cheie `module`
    
*   Numele modulului
    
*   Lista de porturi (elementele declarate aici sunt folosite pentru interfațarea cu exteriorul și pot fi):
    
    *   De intrare (`input`)
        
    *   De ieșire (`output`)
        
    *   Bidirecționale (`inout`)
        
*   `;` (punct și virgulă)
    
*   Implementarea modulului
    
*   Cuvântul cheie `endmodule`
    

Și un exemplu concret:

module test(
    output out,
    input in
    );
 
buf(out, in);
 
endmodule

**Interfața** unui modul este formată din:

*   Numele modulului: poate conține litere, cifre, `$` și `_`. Primul caracter trebuie să fie o literă sau `_`.
    
*   Lista de porturi: zero sau mai multe porturi, fiecare dintre acestea având o direcție (input, output sau inout) și un nume. Convenția este ca în această listă să fie declarate mai întâi ieșirile și apoi intrările.
    

module test\_interface(output a, input b);
 
    // Implementare modul.
 
endmodule

În cazul modulului de mai sus interfața este `test_interface(output a, input b);`.

Implementarea modulului constă în descrierea funcționării acestuia utilizând:

*   Declarații
    
    *   Fire (`wire`): reprezintă conexiuni fizice între componente. Se folosesc pentru transmisia semnalelor (doar în descrierea logicii combinaționale) și nu au capacitate de reținere a informației (nu au o stare)
        
    *   Registre (`reg`): sunt folosite pentru a stoca date, care persistă chiar dacă registrul este deconectat. Registrul este echivalentul unei variabile interne dintr-un limbaj de programare. Reține o valoare și i se poate atribui o valoare
        
*   Construcții
    
    *   Instanțieri de module
        
    *   Blocuri `initial`: ne permit să definim o stare inițială. Acest bloc va fi declanșat o singură dată, la inițializarea modulului. Este nerecomandată folosirea lor înafara simulărilor
        

Intrările, ieșirile și porturile bidirecționale ale unui modul sunt implicit considerate ca având tipul `wire`. Putem să specificăm, dacă dorim, ca ieșirile să aibă tipul `reg`.

Toate variabilele de tip `wire` și `reg` (implicit și cele de tip `input`, `output` și `inout`) au, în mod implicit, lățimea de 1 bit. Putem specifica, dacă dorim, o lățime mai mare de atât folosind construcția `[i:j]`. Atenție, _i_ și _j_ pot să nu fie niciuna 0 și pot fi _i >= j_ sau _j >= i_.

input a;        // Variabilă fir de intrare, pe 1 bit.
output b;       // Variabilă fir de ieșire, pe 1 bit.
output reg c;   // Variabilă registru de ieșire, pe 1 bit.
inout d;        // Variabilă fir bidirecțională, pe 1 bit.
wire e;         // Variabilă fir locală, pe 1 bit.
reg f;          // Variabilă registru locală, pe 1 bit.
input \[7:0\] g;  // Variabilă fir de intrare, pe 8 biți ordonați 7 -> 0.
wire \[0:7\] h;   // Variabilă fir locală, pe 8 biți ordonați 0 -> 7.
reg \[2:6\] i;    // Variabilă registru locală, pe 5 biți ordonați 2 -> 6.

##### Instanțiere

Verilog permite folosirea unui modul în descrierea altui modul prin instanțierea acestuia. Instanțierea modulelor se face, generic, în felurile următoare:

nume\_modul <nume\_instanta\>(
    <nume\_argument\_1\>,
    <nume\_argument\_2\>,
    ...,
    <nume\_argument\_n\>
    );
 
nume\_modul <nume\_instanta\>(
    .nume\_port\_1(<nume\_argument\_1\>),
    .nume\_port\_7(<nume\_argument\_7\>),
    .nume\_port\_2(<nume\_argument\_2\>),
    ...,
    .nume\_port\_n(<nume\_argument\_n\>)
    );

Și exemple concrete:

module test\_instantiation\_A(output a, output b, input c, input d);
 
    // Implementare modul.
 
endmodule
 
module test\_instantiation\_B(output out1, output out2, input in1, input in2);
    test\_instantiation\_A a1(out1, out2, in1, in2);                    // Am scris argumentele in ordinea porturilor.
    test\_instantiation\_A a2(.b(out2), .a(out1), .c(in1), .d(in2));    // Am scris argumentele intr-o ordine aleatoare, dar pentru fiecare am specificat numele portului la care trebuie legat.
    test\_instantiation\_A a3(out1, out2, in1);                          // Nu am legat portul "d". Nu este o eroare.
 
    // Implementare modul.
 
endmodule

Nu este o eroare să nu legăm toate porturile unui modul atunci când îl instanțiem. Spre exemplu, un sumator poate avea o ieșire de date (suma celor două intrări) și o ieșire de transport (carry). Dacă ieșirea de transport nu ne folosește este perfect valid să nu o conectăm la nicio variabilă.

#### Primitive

Pentru a descrie circuite folosind Verilog, avem la dispoziție și o serie de primitive care sunt asociate porților logice de bază. Fiecare primitivă are asociate porturi prin intermediul cărora se face legătura cu exteriorul. Astfel, există primitive predefinite care oferă posibilitatea conectării mai multor intrări (**and**, **or**, **nor**, **nand**, **xor**, **xnor**), sau a mai multor ieșiri (**buf**, **not**).

Folosirea unei primitive se face prin instanțierea cu lista de semnale care vor fi conectate la porturile ei.

Poartă

Descriere Verilog

[![poata OR cu 3 intrări](/courses/_media/cn1/laboratoare/01/or-gate.png?w=200&tok=d35c3a "poata OR cu 3 intrări")](/courses/_detail/cn1/laboratoare/01/or-gate.png?id=cn1%3Alaboratoare%3A01 "cn1:laboratoare:01:or-gate.png")

or(out, a, b, c);

[![poata NOT](/courses/_media/cn1/laboratoare/01/not-gate.png?w=200&tok=61317b "poata NOT")](/courses/_detail/cn1/laboratoare/01/not-gate.png?id=cn1%3Alaboratoare%3A01 "cn1:laboratoare:01:not-gate.png")

not(out, in);

[![poata NAND](/courses/_media/cn1/laboratoare/01/nand-gate.png?w=200&tok=27c242 "poata NAND")](/courses/_detail/cn1/laboratoare/01/nand-gate.png?id=cn1%3Alaboratoare%3A01 "cn1:laboratoare:01:nand-gate.png")

nand(z, x, y);

*   Pentru primitivele predefinite numele instanței este opțional.
    
*   În cazul primitivelor este obligatoriu să declarăm la început semnalele de ieșire, acestea fiind urmate de cele de intrare.
    
*   Așa cum afirmația precedentă a lăsat să se întrevadă, în Verilog se pot defini și [UDP-uri](http://www.asic-world.com/verilog/udp1.html "http://www.asic-world.com/verilog/udp1.html") (User Defined Primitives), prin intermediul tabelei de adevăr.
    

### Dezvoltare și testare

Ca mediu de dezvoltare vom utiliza Xilinx ISE, ce ne permite sintetizarea codului, simularea codului și programarea pe plăci. Xilinx ISE WebPACK este versiunea gratuită, ce poate fi folosită pentru a sintetiza și programa plăcile folosite la laborator. Puteți descărca gratuit Xilinx ISE WebPACK de (**[aici](/courses/lib/exe/fetch.php?hash=165d45&media=https%3A%2F%2Fwww.xilinx.com%2Fproducts%2Fdesign-tools%2Fise-design-suite%2Fise-webpack.html "https://www.xilinx.com/products/design-tools/ise-design-suite/ise-webpack.html")**) folosind email-ul de la facultate (username@stud.acs.upb.ro)

#### Hello World!

Vom implementa în Verilog un modul ce are o intrare și o ieșire, ambele pe 1 bit. Ieșirea modulului va fi intrarea negată. De asemenea vom implementa un modul de test pentru cel precedent. Pentru crearea proiectelor și modulelor folosind Xilinx ISE urmăriți **[acest tutorial](/courses/lib/exe/fetch.php?hash=5627f0&media=https%3A%2F%2Focw.cs.pub.ro%2Fcourses%2Fcn1%2Ftutoriale%2Fproject-ise "https://ocw.cs.pub.ro/courses/cn1/tutoriale/project-ise")**.

[hello\_world.v](/courses/_export/code/cn1/laboratoare/01?codeblock=9 "Download Snippet")

module hello\_world(
    output out,
    input in
    );
 
    not(out, in);
 
endmodule

#### Testare

Pentru testarea modulelor Xilinx ISE ne pune la dispoziție simulatorul ISIM.

Pentru a testa funcționarea unui modul trebuie să creăm un modul de test (**test fixture**). Pentru un exemplu de simulare a unui modul urmăriți **[acest tutorial](/courses/lib/exe/fetch.php?hash=27a7d6&media=https%3A%2F%2Focw.cs.pub.ro%2Fcourses%2Fcn1%2Ftutoriale%2Fsimulare-ise "https://ocw.cs.pub.ro/courses/cn1/tutoriale/simulare-ise")**.

[test\_hello\_world.v](/courses/_export/code/cn1/laboratoare/01?codeblock=10 "Download Snippet")

\`timescale 1ns / 1ps
 
module test\_hello\_world;
 
    // Intrari.
    reg in;
 
    // Iesiri.
    wire out;
 
    // Instantiem Unit Under Test (UUT).
    hello\_world uut (
        .out(out),
        .in(in)
    );
 
    initial begin
        // Initializam intrarile.
        in \= 0;
 
        // Asteptam 100 ns pentru a termina resetul global.
        #100;
 
        // Adaugam stimuli aici.
        #100 in \= 1;
        #100 in \= 0;
        end
 
endmodule

După cum vedeți, este un modul ca oricare altul. Două construcții ies totuși în evidență:

*   directiva `timescale`: definește unitatea de timp a simulării.
    
*   construcția `#100;`: această construcție indică simulatorului să aștepte 100 de unități de timp. Când prefixăm o instrucțiune cu `#n` simulatorul va aștepta _n_ unități de timp, după care va executa instrucțiunea. **Atenție, această construcție nu este validă decât într-o simulare**.
    

### TL;DR

*   Verilog este un limbaj de descriere hardware, nu un limbaj de programare
    
*   Instrucțiunile în Verilog se execută paralel
    
*   Unitatea de bază este modulul
    
*   Declarații:
    
    *   wire
        
    *   reg
        
*   Construcții:
    
    *   instanțieri module
        
    *   bloc initial
        
*   Primitive
    

### Exerciții

**Task 0** (0p) **\[DEMO\]** Realizați un modul cu 2 intrări (a și b) și 2 ieșiri c = a & b și d = a | b. Testați funcționarea modulului.

**Task 1** (1p) Simulați exemplul din scheletul de laborator.[Tutorial simulare ISE](https://ocw.cs.pub.ro/courses/cn1/tutoriale/simulare-ise "https://ocw.cs.pub.ro/courses/cn1/tutoriale/simulare-ise")

**Task 2** (3p) Implementați o poartă `XOR`, folosind porți `AND`, `OR` și `NOT`. Testați funcționarea modulului.

**Task 3** (3p) Implementați un multiplexor 4:1. Testați funcționarea modulului.

**Task 4** (3p) Implementați un circuit combinațional cu 2 intrări de date, 2 intrări de selecție și 1 ieșire. Intrările de selecție selectează ce funcție logică să se aplice pe cele două intrări de date pentru a rezulta ieșirea. Cele 4 funcții logice sunt `XOR` (folosiți modulul de la task 1), `NAND`, `OR` și `AND`. Modulul va funcționa astfel:

*   Dacă intrările de selecție sunt `11`, se aplică `XOR`
    
*   Dacă intrările de selecție sunt `10`, se aplică `OR`
    
*   Dacă intrările de selecție sunt `01`, se aplică `AND`
    
*   Altfel, se aplică `NAND`
    

Testați funcționarea modulului.

**Task 5** (Bonus 1p) Implementați un sumator Half-Adder (sumator pe 1 bit fără intrare de transport) și unul Full-Adder (sumator pe 1 bit cu intrare de transport). Testați funcționarea modulelor.

### Resurse

*   [Scheletul de laborator](/courses/_media/cn1/laboratoare/01/skel/lab01_skel.zip "cn1:laboratoare:01:skel:lab01_skel.zip (5.8 KB)")
    

Informații
==========

*   [Reguli generale și notare](/courses/cn1/regulament "cn1:regulament")
    

Cursuri
=======

*   [Cursul 0 - Legea lui Moore](/courses/cn1/cursuri/01 "cn1:cursuri:01")
    
*   [Cursul 1 - De la porți logice la procesoare](/courses/cn1/cursuri/02 "cn1:cursuri:02")
    
*   [Cursul 2 - Introducere în Verilog](/courses/cn1/cursuri/03 "cn1:cursuri:03")
    

Laboratoare
===========

*   [00 - Introducere în logica digitală](/courses/cn1/laboratoare/00 "cn1:laboratoare:00")
    
*   [01 - Introducere în Verilog](/courses/cn1/laboratoare/01 "cn1:laboratoare:01")
    

Resurse
=======

*   [Mașină virtuală](/courses/cn1/vm "cn1:vm")
    
*   [Debugging folosind Xilinx ISE](/courses/cn1/resurse/debug "cn1:resurse:debug")
    
*   [Blocking vs Nonblocking](/courses/cn1/resurse/nonblocking "cn1:resurse:nonblocking")
    
*   [Instalare ISE](/courses/_media/cn1/tutoriale/instalarexillinx.pdf "cn1:tutoriale:instalarexillinx.pdf (652.8 KB)")
    

Tutoriale
=========

*   [Gestionare proiect în Xilinx ISE](/courses/cn1/tutoriale/project-ise)
    
    *   [add\_module](/courses/cn1/tutoriale/project-ise/add_module "cn1:tutoriale:project-ise:add_module")
        
    *   [create](/courses/cn1/tutoriale/project-ise/create "cn1:tutoriale:project-ise:create")
        
*   [Programarea FPGA-ului folosind Xilinx ISE](/courses/cn1/tutoriale/programming-ise "cn1:tutoriale:programming-ise")
    
*   [Testarea modulelor](/courses/cn1/tutoriale/simulare-ise "cn1:tutoriale:simulare-ise")
    

### Table of Contents

*   [Laboratorul 01 - Introducere în Verilog](#laboratorul_01_-_introducere_in_verilog)
    
    *   [Ce este Verilog?](#ce_este_verilog)
        
        *   [Verilog is not C!](#verilog_is_not_c)
            
        *   [De ce Verilog?](#de_ce_verilog)
            
    *   [Structura limbajului Verilog](#structura_limbajului_verilog)
        
        *   [Module](#module)
            
        *   [Primitive](#primitive)
            
    *   [Dezvoltare și testare](#dezvoltare_si_testare)
        
        *   [Hello World!](#hello_world)
            
        *   [Testare](#testare)
            
    *   [TL;DR](#tldr)
        
    *   [Exerciții](#exercitii)
        
    *   [Resurse](#resurse)
