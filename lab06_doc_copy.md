Laboratorul 6 - Sumatoare
=========================

### 1\. Scopul laboratorului

Scopul acestui laborator este proiectarea unor module ce implementează operații aritmetice. În acest laborator vom implementa sumatoare simple și le vom examina performanțele.

### 2\. Mod de lucru

Cuvintele calculatoarelor sunt compuse din biți, deci o reprezentare facilă este în numere binare. Numărul de biți din care este compus un cuvânt determină gama de valori reprezentabilă în acel cuvânt. Spre exemplu, un cuvânt de 32 de biți poate conține valori între 0 și 232 - 1 = 4.294.967.295 (desigur, am considerat numărul ca fiind fără semn).

De asemenea, pentru a reprezenta numere negative putem folosi primul bit dintr-un cuvânt ca bit de semn. Astfel un cuvânt cu primul bit 0 este considerat număr pozitiv, iar un cuvânt cu primul bit 1 este considerat negativ. Numerele negative sunt reprezentate în [cod complementar lui 2](http://en.wikipedia.org/wiki/Two%27s_complement "http://en.wikipedia.org/wiki/Two%27s_complement").

### 3\. Sumatoare

Un sumator este un circuit digital ce realizează suma a două numere. În calculatoarele moderne ele se găsesc nu numai în unitatea aritmetică-logică ([UAL](http://en.wikipedia.org/wiki/Arithmetic_logic_unit "http://en.wikipedia.org/wiki/Arithmetic_logic_unit")) ci și în alte unități ale procesorului, fiind folosite pentru a calcula adrese, indici, etc.

#### 3.1. Half-adder

[![](/courses/_media/cn1/laboratoare/06/half-adder.png?w=300&tok=64e473)](/courses/_detail/cn1/laboratoare/06/half-adder.png?id=cn1%3Alaboratoare%3A06 "cn1:laboratoare:06:half-adder.png")

Un half-adder este un circuit care realizează suma a doi operanzi pe un singur bit. Intrările sale sunt A și B, cei doi operanzi. El generează la ieșire două semnale: S (suma) si C (carry - transportul către următorul rang). Tabela de adevăr pentru un half-adder este prezentată în figura 3.1.

A

B

S

C

0

0

0

0

0

1

1

0

1

0

1

0

1

1

0

1

_Fig 3.1: Tabela de adevăr pentru un half-adder_

#### 3.2. Full-adder

[![](/courses/_media/cn1/laboratoare/06/382_fa_diagram.gif?w=400&tok=a37c42)](/courses/_detail/cn1/laboratoare/06/382_fa_diagram.gif?id=cn1%3Alaboratoare%3A06 "cn1:laboratoare:06:382_fa_diagram.gif")

Un full-adder este un circuit care realizează suma a doi operanzi ținând cont și de transportul din rangul inferior. Intrările sale sunt A, B (cei doi operanzi) și Cin (transportul de la rangul inferior). El generează la ieșire doua semnale: S (suma) și Cout (carry out - transportul către rangul următor). Tabela de adevăr pentru un full-adder este prezentată în figura 3.2.

A

B

Cin

S

Cout

0

0

0

0

0

0

0

1

1

0

0

1

0

1

0

0

1

1

0

1

1

0

0

1

0

1

0

1

0

1

1

1

0

0

1

1

1

1

1

1

_Figura 3.2: Tabela de adevăr pentru un full-adder_

Un full-adder se poate implementa folosind 2 half-addere: [![](/courses/_media/cn1/laboratoare/06/images.png?w=400&tok=1b618b)](/courses/_detail/cn1/laboratoare/06/images.png?id=cn1%3Alaboratoare%3A06 "cn1:laboratoare:06:images.png")

#### 3.3. Sumatorul ripple-carry

Cum în practică vom avea nevoie de numere mai mari decât 1, acestea vor fi reprezentate pe mai mulți biți. În acest caz avem o problemă: sumatoarele noastre nu pot aduna decât maxim un bit! Pentru a rezolva problema trebuie să creăm un circuit care să facă suma numerelor reprezentate pe mai mulți biți.

Un astfel de circuit, și cel mai simplu, este sumatorul ripple-carry. Pentru a construi un astfel de sumator pe n biți avem nevoie de fie n sumatoare full-adder (dintre care primul va avea intrarea Cin legată la 0 întotdeauna), fie de n-1 sumatoare full-adder și un half-adder (care va înlocui acel prim full-adder și **NUMAI** pe acela).

Conectarea lor, prezentată în figura 3.3, este una foarte simplă: ieșirea Cout a modulului de rang _i_ va fi conectată la intrarea Cin a modulului de rang _i+1_. Acest fapt ne bucură în faza de proiectare, fiindcă nu avem mult de lucru, însă un astfel de sumator este încetinit de faptul că modulul de rang _i_ trebuie să aştepte modulul de rang _i-1_ sa îşi termine executia pentru a afla cât este Cin, iar cel de rang _i-1_ la rândul său trebuie să îl aștepte pe cel de rang _i-2_ s.a.m.d.

[![ Ripple-carry pe 4 biți ](/courses/_media/cn1/laboratoare/06/ripple-carry4bit.png?w=400&tok=ebfb54 " Ripple-carry pe 4 biți ")](/courses/_detail/cn1/laboratoare/06/ripple-carry4bit.png?id=cn1%3Alaboratoare%3A06 "cn1:laboratoare:06:ripple-carry4bit.png")

_Figura 3.3: Un sumator ripple-carry pe 4 biți_

TL;DR
-----

*   Cuvintele calculatorului sunt compuse din biți, prin urmare, cuvintele sunt de fapt niște valori.
    
    *   Convenție ([Complement față de 2](https://en.wikipedia.org/wiki/Two%27s_complement "https://en.wikipedia.org/wiki/Two%27s_complement")):
        
        *   **pozitive** - primul bit este 0
            
        *   **negative** - primul bit este 1
            
*   **Sumator** = circuit digital care realizează suma a două numere
    
    *   **Half-adder** = realizează suma a doi operanzi pe un singur bit
        
    *   **Full-adder** = realizează suma a doi operanzi pe un singur bit ținând cont și de transportul din rangul inferior
        
    *   **Ripple-carry** = sumator pe **n** biți realizat din **n** sumatoare 'full-adder' cascadate
        
        *   Cin(i+1) = Cout(i)
            
        *   Cin(0) = 0
            

Exerciții
---------

Urmăriţi **TODO**\-urile din module pentru rezolvarea exerciţiilor. Modulele și fişierele de test sunt **deja create** în scheletul laboratorului.

1.  (4p) Implementați un half-adder și un full-adder.(**NU** aveți voie să folosiți operatorul '+' din Verilog).
    
    1.  (2p) Implementarea half-adder se va face la oricare dintre din următoarele nivele:
        
        *   la nivel structural
            
        *   la nivel flux de date
            
        *   la nivel procedural
            
    2.  (2p) Implementarea full-adder se va face utilizând două instanțe ale half-adder-ului implementat la subpunctul anterior.
        
2.  (2p) Implementați un sumator ripple-carry pe 8 biți.
    
    *   !!! Pentru implementare folosiți modulele de la punctul precedent.
        
3.  (2p) Implementați un scăzător pe 8 biti, pornind de la modulul de la punctul precedent.
    
4.  (4p) Implementați un calculator de buzunar care oferă suport pentru operația de **adunare** și de **scădere**, **folosind modulele implementate anterior**.
    
    1.  Caracteristici:
        
        1.  Operația poate avea **oricâți operanzi** și operatori
            
        2.  Operanzii și operatorii **se introduc alternativ** în mod secvențial
            
        3.  **Afișarea rezultatului** se face doar la primirea operatorului =
            
    2.  Cazuri specifice:
        
        1.  Introducerea **unui singur operand** urmat de operatorul = va afișa valoarea operandului
            
        2.  Verificarea **corectitudinii secvenței** de operatori și operanzi
            
            *   13 ADD 14 EQL → 27
                
            *   13 17 ADD 14 EQL → 27
                
        3.  **Utilizați semnalul ready** pentru a semnala că starea curentă așteaptă o intrare
            
            *   ready = 1, cand este in starea de asteptare a unui operand/operator
                
            *   ready = 0, altfel
                

In scheletul de laborator consideram ca operatori:

*   ADD = 0
    
*   SUB = 1
    
*   EQL = 2
    
*   ERR = 3
    

Ca operanzi putem sa folosim doar numere **mai mari strict** ca 3

Resurse
-------

*   [Scheletul de laborator](/courses/_media/cn1/laboratoare/06/lab06_skel.zip "cn1:laboratoare:06:lab06_skel.zip (18.8 KB)")
    

Link-uri utile
--------------

*   [Generate in verilog](http://www.fpgadeveloper.com/2011/07/code-templates-generate-for-loop.html "http://www.fpgadeveloper.com/2011/07/code-templates-generate-for-loop.html")
    
*   [Ripple Carry Adder Simulator](http://www.ecs.umass.edu/ece/koren/arith/simulator/Add/ripple/ripple.html "http://www.ecs.umass.edu/ece/koren/arith/simulator/Add/ripple/ripple.html")
    

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

*   [Laboratorul 6 - Sumatoare](#laboratorul_6_-_sumatoare)
    
    *   *   [1\. Scopul laboratorului](#scopul_laboratorului)
            
        *   [2\. Mod de lucru](#mod_de_lucru)
            
        *   [3\. Sumatoare](#sumatoare)
            
            *   [3.1. Half-adder](#half-adder)
                
            *   [3.2. Full-adder](#full-adder)
                
            *   [3.3. Sumatorul ripple-carry](#sumatorul_ripple-carry)
                
    *   [TL;DR](#tldr)
        
    *   [Exerciții](#exercitii)
        
    *   [Resurse](#resurse)
        
    *   [Link-uri utile](#link-uri_utile)
        

