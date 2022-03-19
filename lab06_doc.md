====== Laboratorul 6 - Sumatoare ======



==== 1. Scopul laboratorului ====

Scopul acestui laborator este proiectarea unor module ce implementează operații aritmetice. În acest laborator vom implementa sumatoare simple și le vom examina performanțele.
==== 2. Mod de lucru ====

Cuvintele calculatoarelor sunt compuse din biți, deci o reprezentare facilă este în numere binare. Numărul de biți din care este compus un cuvânt determină gama de valori reprezentabilă în acel cuvânt. Spre exemplu, un cuvânt de 32 de biți poate conține valori între 0 și 2<sup>32</sup> - 1 = 4.294.967.295 (desigur, am considerat numărul ca fiind fără semn).

De asemenea, pentru a reprezenta numere negative putem folosi primul bit dintr-un cuvânt ca bit de semn. Astfel un cuvânt cu primul bit 0 este considerat număr pozitiv, iar un cuvânt cu primul bit 1 este considerat negativ. Numerele negative sunt reprezentate în [[http://en.wikipedia.org/wiki/Two%27s_complement|cod complementar lui 2]]. 

==== 3. Sumatoare ====

Un sumator este un circuit digital ce realizează suma a două numere. În calculatoarele moderne ele se găsesc nu numai în unitatea aritmetică-logică ([[http://en.wikipedia.org/wiki/Arithmetic_logic_unit|UAL]]) ci și în alte unități ale procesorului, fiind folosite pentru a calcula adrese, indici, etc.

=== 3.1. Half-adder ===

{{ :cn1:laboratoare:06:half-adder.png?300 |}}

Un half-adder este un circuit care realizează suma a doi operanzi pe un singur bit. Intrările sale sunt A și B, cei doi operanzi. El generează la ieșire două semnale: S (suma) si C (carry - transportul către următorul rang). Tabela de adevăr pentru un half-adder este prezentată în figura 3.1.

^ A ^ B ^ S ^ C ^
| 0 | 0 | 0 | 0 |
| 0 | 1 | 1 | 0 |
| 1 | 0 | 1 | 0 |
| 1 | 1 | 0 | 1 |

//Fig 3.1: Tabela de adevăr pentru un half-adder//

=== 3.2. Full-adder ===

{{ :cn1:laboratoare:06:382_fa_diagram.gif?400 |}}

Un full-adder este un circuit care realizează suma a doi operanzi ținând cont și de transportul din rangul inferior. Intrările sale sunt A, B (cei doi operanzi) și C<sub>in</sub> (transportul de la rangul inferior). El generează la ieșire doua semnale: S (suma) și C<sub>out</sub> (carry out - transportul către rangul următor). Tabela de adevăr pentru un full-adder este prezentată în figura 3.2.

^  A  ^  B  ^  C<sub>in</sub>  ^  S  ^  C<sub>out</sub>  ^
|  0  |  0  |  0  |  0  |  0  | 
|  0  |  0  |  1  |  1  |  0  |
|  0  |  1  |  0  |  1  |  0  |
|  0  |  1  |  1  |  0  |  1  |
|  1  |  0  |  0  |  1  |  0  |
|  1  |  0  |  1  |  0  |  1  |
|  1  |  1  |  0  |  0  |  1  |
|  1  |  1  |  1  |  1  |  1  |
// Figura 3.2: Tabela de adevăr pentru un full-adder//

Un full-adder se poate implementa folosind 2 half-addere:
{{ :cn1:laboratoare:06:images.png?400 |}}

=== 3.3. Sumatorul ripple-carry ===

Cum în practică vom avea nevoie de numere mai mari decât 1, acestea vor fi reprezentate pe mai mulți biți. În acest caz avem o problemă: sumatoarele noastre nu pot aduna decât maxim un bit! Pentru a rezolva problema trebuie să creăm un circuit care să facă suma numerelor reprezentate pe mai mulți biți. 

Un astfel de circuit, și cel mai simplu, este sumatorul ripple-carry. Pentru a construi un astfel de sumator pe n biți avem nevoie de fie n sumatoare full-adder (dintre care primul va avea intrarea C<sub>in</sub> legată la 0 întotdeauna), fie de n-1 sumatoare full-adder și un half-adder (care va înlocui acel prim full-adder și **NUMAI** pe acela). 

Conectarea lor, prezentată în figura 3.3, este una foarte simplă: ieșirea C<sub>out</sub> a modulului de rang //i// va fi conectată la intrarea C<sub>in</sub> a modulului de rang //i+1//. Acest fapt ne bucură în faza de proiectare, fiindcă nu avem mult de lucru, însă un astfel de sumator este încetinit de faptul că modulul de rang //i// trebuie să aştepte modulul de rang //i-1// sa îşi termine executia pentru a afla cât este C<sub>in</sub>, iar cel de rang //i-1// la rândul său trebuie să îl aștepte pe cel de rang //i-2// s.a.m.d. 

{{ :cn1:laboratoare:06:ripple-carry4bit.png?400 | Ripple-carry pe 4 biți }}


//Figura 3.3: Un sumator ripple-carry pe 4 biți//

===== TL;DR =====
  * Cuvintele calculatorului sunt compuse din biți, prin urmare, cuvintele sunt de fapt niște valori.
    * Convenție ([[https://en.wikipedia.org/wiki/Two%27s_complement|Complement față de 2]]):
        * **pozitive** - primul bit este 0
        * **negative** - primul bit este 1
  * **Sumator** = circuit digital care realizează suma a două numere
    * **Half-adder** = realizează suma a doi operanzi pe un singur bit
    * **Full-adder** = realizează suma a doi operanzi pe un singur bit ținând cont și de transportul din rangul inferior
    * **Ripple-carry** = sumator pe **n** biți realizat din **n** sumatoare 'full-adder' cascadate
      * C<sub>in<sup>(i+1)</sup></sub> = C<sub>out<sup>(i)</sup></sub>
      * C<sub>in<sup>(0)</sup></sub> = 0

===== Exerciții =====
<note important>
Urmăriţi **TODO**-urile din module pentru rezolvarea exerciţiilor. Modulele și fişierele de test sunt **deja create** în scheletul laboratorului.
</note> 

  - (4p) Implementați un half-adder și un full-adder.(**NU** aveți voie să folosiți operatorul '+' din Verilog).
    - (2p) Implementarea half-adder se va face la oricare dintre din următoarele nivele:
      * la nivel structural
      * la nivel flux de date
      * la nivel procedural
    - (2p) Implementarea full-adder se va face utilizând două instanțe ale half-adder-ului implementat la subpunctul anterior.
  - (2p) Implementați un sumator ripple-carry pe 8 biți.
    * !!! Pentru implementare folosiți modulele de la punctul precedent.
  - (2p) Implementați un scăzător pe 8 biti, pornind de la modulul de la punctul precedent.
  - (4p) Implementați un calculator de buzunar care oferă suport pentru operația de **adunare** și de **scădere**, **folosind modulele implementate anterior**.
    - Caracteristici:
      - Operația poate avea **oricâți operanzi** și operatori
      - Operanzii și operatorii **se introduc alternativ** în mod secvențial
      - **Afișarea rezultatului** se face doar la primirea operatorului =
    - Cazuri specifice:
      - Introducerea **unui singur operand** urmat de operatorul = va afișa valoarea operandului
      - Verificarea **corectitudinii secvenței** de operatori și operanzi
        * 13 ADD 14 EQL      -> 27
        * 13 17 ADD 14 EQL   -> 27
      - **Utilizați semnalul ready** pentru a semnala că starea curentă așteaptă o intrare
        * ready = 1, cand este in starea de asteptare a unui operand/operator
        * ready = 0, altfel
 <note important>
In scheletul de laborator consideram ca operatori:
  * ADD = 0
  * SUB = 1
  * EQL = 2
  * ERR = 3

Ca operanzi putem sa folosim doar numere **mai mari strict** ca 3

</note>
      

<hidden>
**EXERCITIU PENTRU PREDAT OFFLINE**
  - (4p) Implementați un sumator/scăzător pe 8 biți cu ajutorul [[http://www.digilentinc.com/Data/Products/NEXYS3/Nexys3_rm_V2.pdf|placii de laborator]].
    - Caracteristici:
      - 2 operanzi pe 8 biţi
      - Afişare operanzi şi rezultat pe afişajul cu 7 segmente
      - Afişare progres pe cele 8 LED-uri
    - Mod de operare:
      - Preluare operand 1 -> prin apăsarea unui 'push-button'
      - Preluare operand 2 -> prin apăsarea aceluiaşi 'push-button'
        * !!! Până la apăsarea butonului, pe afişajul cu 7 segmente va fi afişată valoarea operandului
      - Afişare:
        * rezultat -> dacă este ţinut apăsat butonul corespunzător unei operaţii
        * 'APAS' -> dacă nu este apăsat niciun buton
      - La apăsarea butonului de reset, circuitul se întoarce la preluarea primului operand
    *  **Citiţi comentariile din modul şi urmăriţi TODO-urile!**
</hidden>
===== Resurse =====
  * {{:cn1:laboratoare:06:lab06_skel.zip|Scheletul de laborator}}


===== Link-uri utile =====
  * [[http://www.fpgadeveloper.com/2011/07/code-templates-generate-for-loop.html | Generate in verilog]]
  * [[http://www.ecs.umass.edu/ece/koren/arith/simulator/Add/ripple/ripple.html | Ripple Carry Adder Simulator]]

