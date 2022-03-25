====== Laboratorul 04 - Automate cu stări ======

===== Introducere =====

Un automat finit (Finite State Automaton, sau FSM) este, un model de calculabilitate, folosit pentru proiectarea diverselor programe sau circuite secvenţiale. Altfel spus, automatele cu stari finite ne ajuta sa modelam execution flow-uri, lucru necesar in diverse domenii cum ar fi matematica, inteligenta artificiala, jocuri sau analiza lingvistica (*cough* *cough* A-ul din "LFA" in anul 3).

Un FSM modeleaza o masina ipotetica avand un numar finit de stari. Trasatura fundamentala a acestei masini este ca numai una din aceste stari poate fi activa in oricare moment de timp. Asta inseamna ca pentru a putea executa toate actiunile pentru care a fost proiectata, aceasta trebuie sa isi schimbe starea activa (sau curenta) in functie de niste conditii prestabilite. Vom parcurge în acest laborator o modalitate de reprezentare a stărilor şi tranziţiilor automatelor.

Automatele finite, pe lângă faptul că au stări şi tranziţii, **pot primi intrări şi pot da la ieşire diverse informaţii**, ceea ce le dă şi utilitatea. Din punctul de vedere al **condiţiilor** în care automatele dau informaţii la output, ele sunt împărţite în două mari categorii generale.

===== 1. Mealy vs. Moore =====

La **automatele Mealy**, ieşirea depinde de //**starea curentă şi de input-ul curent**//. O diagramă simplă pentru un automat Mealy este prezentată mai jos. <html> Observaţi pe fiecare arc (= tranziţie) <span style="color:red;"> input-urile </span> şi <span style="color:blue;"> output-urile </span> pe care automatul le dă după tranziţie. De exemplu, tranziţia  A -> B se poate citi astfel: "Din A, cu input-ul 0, trec în B şi scot la ieşire 0".
</html>
{{ :cn1:laboratoare:04:autom_mealy.png?800 |}}

La **automatele Moore**, pe de altă parte, output-ul este determinat //**exclusiv de starea în care se află**//. Mai jos găsiţi o diagramă pentru un automat Moore simplu.<html> Observaţi <span style="color:red;"> input-urile </span> care determină tranziţiile şi <span style="color:blue;"> output-urile </span> corespunzătoare fiecărei stări, care sunt independente de valorile de intrare.
</html>
{{ :cn1:laboratoare:04:autom_moore.png?800 |}}

Automatele pot fi construite atât în forma Mealy, cât şi Moore. În laborator vom lucra cu varianta Moore, pentru că este mai uşor de reprezentat.


===== 2. Reprezentarea automatelor =====

Pentru a implementa şi programa un automat finit, avem nevoie de o metodă de memorare a stării automatului într-un moment de timp oarecare, deci de o metodă de codificare.

Cele mai folosite metode de codificare a stărilor unui automat sunt:
  * //Numărare efectivă//, în binar: de exemplu, la o codificare pe 4 biţi, se numără stările de la 0000, 0001, 0010, ... 1111
  * //Codul Gray//, în care numerele consecutive diferă doar printr-un singur bit: 0000, 0001, 0011, 0010 ...
  * //Codul "one-hot"//, în care avem nevoie de atâţia biţi câte stări există. În starea **i**, toţi biţii sunt 0, //mai puţin bitul i, care e pe 1 (hot)//: 0010 = automatul se află în starea 2 (din cele 4 posibile)
  * //Codul "one-cold"//, asemănător cu cel de mai sus, numai că valorile sunt inversate: 1101 = automatul se află în starea 2 din cele 4 posibile.

In cadrul acestui laborator vom recomanda folosirea codificarii prin numarare efectiva a starilor, deoarece aceasta este cea mai usor accesibila alternativa.

===== 3. Un automat complet =====

Să implementăm automatul din figură:
{{ :cn1:laboratoare:04:exemplu_automat.png?546 |}}

<note>
Observăm că:
  * avem multiple ieşiri
  * automatul are 5 stări, deci avem nevoie de o codificare pe 3 biţi, şi totuşi rămân coduri nefolosite; vom introduce 3 stări false pentru a completa codificarea (nu este absolut necesar, dar este considerat good-practice)
</note>


<note tip>
De regula, pentru a ne asigura ca automatul nostru este complet si corect, avem nevoie de cateva lucruri in vedere atunci cand il construim:
  * Definirea starilor
  * Definirea tranzitiilor (este foarte important ca tranzitiile sa fie toate definite si testate, inainte de implementarea propriu zisa a FSM-ului)
  * Definirea intrarilor
  * Definirea iesirilor
</note>

==== Pașii pentru realizarea unui FSM in Verilog ====

=== Codificarea stărilor ===

  * Definim stările necesare automatului pe numarul minim de biți (eg. 6 stări au nevoie de minim 3 biți)
  * În general este recomandat să avem o stare inițială în care să se facă inițializarea automatului. Logica automatului va continua din această stare.

<note important> Este recomandat sa aveți **nume sugestive** pentru stările automatului. Spre exemplu o stare **STATE_ABCA** este starea în care automatul a citit la intrare sirul ABCA. </note> 

<code Verilog>
localparam STATE_Initial = 3’d0 ,
           STATE_1 = 3’d1 ,
           STATE_2 = 3’d2 ,
           STATE_3 = 3’d3 ,
           STATE_4 = 3’d4 ,
           STATE_5_PlaceHolder = 3’d5 ,
           STATE_6_PlaceHolder = 3’d6 ,
           STATE_7_PlaceHolder = 3’d7;
</code>
=== Precizarea comportamentului ieșirilor ===

  * În funcție de stări și intrări, precizăm valorile ce trebuie să apară la ieșirea automatului. 
  * Putem avea atât ieșiri pe un bit, cât și pe mai multi biți, în funcție de cerințele automatului.

<code Verilog>
output wire Output1 ,
output wire Output2 ,
output reg [2:0] Status

// ieșiri pe 1 bit
assign Output1 = ( CurrentState == STATE_1 ) | ( CurrentState == STATE_2 );
assign Output2 = ( CurrentState == STATE_2 );

// ieșiri pe mai multi biți
always@ ( * ) begin
  Status = 3’b000 ;
  case ( CurrentState )
  STATE_2 : begin
    Status = 3’b010 ;
  end
  STATE_3 : begin
    Status = 3’b011 ;
  end
  endcase
end
</code>

=== Logica secvențială de tranziție după ceas ===

  * starea se va modifica doar pe frontul pozitiv al impulsului de ceas.
  * NextState este determinat de logica combinaționala a automatului. Rolul acestui block always este strict de a realiza tranziția după ceas.

<code Verilog>
always@ ( posedge Clock ) begin
  if ( Reset ) 
    CurrentState <= STATE_Initial ;
  else 
    CurrentState <= NextState ;
end
</code>

=== Logica de schimbare a stărilor ===

  * aici implementăm algoritmul/logica de tranziție a automatului.
  * tranzițiile între stări se fac în funcție de starea curentă și de intrări.

<code Verilog>
always@ ( * ) begin
  NextState = CurrentState ;
  
  case ( CurrentState )
  STATE_Initial : begin
    NextState = STATE_1 ;
    end
  STATE_1 : begin
    if (A & B) 
      NextState = STATE_2 ;
  end
  STATE_2 : begin
    if (A) 
      NextState = STATE_3 ;
  end
  STATE_3 : begin
    if (!A & B) 
      NextState = STATE_Initial ;
    else 
      if (A & !B) 
        NextState = STATE_4 ;
  end
  STATE_4 : begin 
  end
  //Stări pentru tratarea erorilor
  //Dacă automatul ajunge în aceste stări se va reseta.
  STATE_5_PlaceHolder : begin
    NextState = STATE_Initial ;
  end
  STATE_6_PlaceHolder : begin
    NextState = STATE_Initial ;
  end
  STATE_7_PlaceHolder : begin
    NextState = STATE_Initial ;
  end
  endcase
end
</code>

====  ====

<spoiler Prin asamblarea pașilor de mai sus obținem un schelet de cod minimal pentru un FSM>
<code verilog>
module BasicFsm (
    // ------------------------------------------------------------
    // Ieșiri
    // ------------------------------------------------------------
    output wire Output1 ,
    output wire Output2 ,
    output reg [2:0] Status
    // ------------------------------------------------------------
    // Intrări
    // ------------------------------------------------------------
    input wire Clock ,
    input wire Reset ,
    input wire A ,
    input wire B ,
    // ------------------------------------------------------------
    );

    // --------------------------------------------------------------------
    // Codificarea stărilor
    // --------------------------------------------------------------------
    localparam STATE_Initial = 3’d0 ,
    STATE_1 = 3’d1 ,
    STATE_2 = 3’d2 ,
    STATE_3 = 3’d3 ,
    STATE_4 = 3’d4 ,
    STATE_5_PlaceHolder = 3’d5 ,
    STATE_6_PlaceHolder = 3’d6 ,
    STATE_7_PlaceHolder = 3’d7;
    // --------------------------------------------------------------------


    // --------------------------------------------------------------------
    // Regiștri pentru memorarea stărilor
    // --------------------------------------------------------------------
    reg [2:0] CurrentState ;
    reg [2:0] NextState ;
    // --------------------------------------------------------------------

    // --------------------------------------------------------------------
    // Ieșiri
    // --------------------------------------------------------------------
    // ieșiri pe 1 bit
    assign Output1 = ( CurrentState == STATE_1 ) | ( CurrentState == STATE_2 );
    assign Output2 = ( CurrentState == STATE_2 );

    // ieșiri pe mai mulți biți (aici doar Status)
    always@ ( * ) begin
        Status = 3’b000 ;
        case ( CurrentState )
            STATE_2 : begin
                Status = 3’b010 ;
            end
            STATE_3 : begin
                Status = 3’b011 ;
            end
        endcase
    end
    // --------------------------------------------------------------------

    // --------------------------------------------------------------------
    // Tranziție sincrona: bloc always@(posedge Clock) 
    // --------------------------------------------------------------------
    always@ ( posedge Clock ) begin
        if ( Reset ) begin
            CurrentState <= STATE_Initial ;
        else begin
            CurrentState <= NextState ;
        end
    end
    // --------------------------------------------------------------------


    // --------------------------------------------------------------------
    // Tranziție condiționată: bloc always@ ( * ) 
    // --------------------------------------------------------------------
    always@ ( * ) begin
        NextState = CurrentState ;
  
        case ( CurrentState ) begin
            STATE_Initial : begin
                NextState = STATE_1 ;
            end
            STATE_1 : begin
                if (A & B) begin 
                    NextState = STATE_2 ;
                end
            end
            STATE_2 : begin
                if (A) begin
                    NextState = STATE_3 ;
                end
            end
            STATE_3 : begin
                if (!A & B) begin 
                    NextState = STATE_Initial ;
                end
                else begin 
                    if (A & !B) begin 
                        NextState = STATE_4 ;
                    end
                end
            end
            STATE_4 : begin
                // ----------------------------------------------------------------------------
                //In mod normal ar trebui ca aici sa existe macar o tranzitie inapoi in STATE_4
                //sau in STATE_INITIAL
                //dar am lasat gol pentru a reflecta diagrama pe care o modelam
                //!!!BAD EXAMPLE!!! Asigurati-va ca exista tranzitii din fiecare stare
                // ----------------------------------------------------------------------------
            end
            //Stări pentru tratarea erorilor
            //Dacă automatul ajunge în aceste stări se va reseta.
            STATE_5_PlaceHolder : begin
                NextState = STATE_Initial ;
            end
            STATE_6_PlaceHolder : begin
                NextState = STATE_Initial ;
            end
            STATE_7_PlaceHolder : begin
                NextState = STATE_Initial ;
            end
        endcase
    end
    // --------------------------------------------------------------------

endmodule
</code>
</spoiler>

===== TL;DR =====

  * Finite State Automaton, sau FSM, folosit pentru proiectarea diverselor programe sau circuite secvenţiale
  * Au un număr finit de stări, iar în orice moment de timp se pot afla într-una singura din acestea
  * Pot executa tranziții, pot primi intrări și pot da la ieșire informații
  * 2 tipuri: Mealy și Moore
  * Mealy -> ieşirea depinde de starea curentă şi de input-ul curent
  * Moore -> output-ul este determinat exclusiv de starea în care se află
  * Reprezentarea stărilor -> numărare, cod Gray, cod ”one-hot”, cod ”one-cold”

===== Exerciții =====

<note>
Reminder sa va descarcati masina virtuala a laboratorului de CN1 daca nu ati facut-o deja.

[[https://ocw.cs.pub.ro/courses/cn1/vm|Masina Virtuala CN1]]
</note>
 
**Task1** (1.5p) Vrem să avem un simplu LED care se aprinde și se stinge la un anumit interval de timp. Astfel, vom folosi un FSM simplu, cu doar două stări. 
    *În prima stare, LED-ul va fi stins, se va aștepta un anumit interval de timp (LED_TMR din defines.vh), iar apoi trece in starea 2.
    *Starea a doua, în care va petrece același interval de timp, dar aprins.
Urmăriți TODO-urile din schelet!



**Task2** (3p) Chiar dacă a trecut Craciunul, acum știm destul de multe pentru a realiza o simplă instalație de pom. Vrem să implementăm un FSM care să producă următoarea secvență folosind LED-urile, unde "*" înseamnă că LED-ul respectiv este aprins, "-" înseamnă că LED-ul respectiv este stins. t00, t01, ..., t14 sunt momente de timp. Timpul dintre tn si tn+1 este de o secundă (Utilizați SECOND din defines.vh).

    t00 *-*-*-*-
    t01 -*-*-*-*
    t02 *-*-*-*-
    t03 -*-*-*-*
    t04 *------*
    t05 -*----*-
    t06 --*--*--
    t07 ---**---
    t08 --*--*--
    t09 -*----*-
    t10 *------*
    t11 -**-*--*
    t12 *---**-*
    t13 *---*-**
    t14 -**-*--*
    mergi la t00

**Task3** (3.5p) Vrem să simulăm comportamentul unui semafor pentru pietoni controlat prin apăsarea unui buton. Inițial ne aflăm într-o stare în care vom aprinde primele 4 becuri ale unei benzi de 8 LED-uri (simulând culoarea rosie a semaforului). Așteptăm un semnal de la buton, iar când acesta este primit, nu vom trece direct în starea următoare, ci vom mai aștepta un interval de timp stabilit (SMPHR_RED_TMR din defines.vh). Apoi vom trece în starea următoare, în care vom aprinde celelalte 4 LED-uri și vom porni un counter. Când se termina cuanta de timp alocată pentru culoarea verde (SMPHR_GRN_TMR din defines.vh), vom reveni în starea inițială.


<note important>Pentru a asigura o funcționare corectă pe un circuit real, trebuie să folosiți în mod normal un debouncer, pe care să îl atașați butonului folosit (vezi secțiunea [[:cn1:laboratoare:03:debouncing|Switch debouncing]] din laborator).
Dar, deoarece acesta este un mediu simulat, in care o schimbare de semnal se produce instantaneu, fara niciun fel de noise, puteti trata input-urile ca deja debounce-uite.
</note>


**Task4** (4p) Considerăm ADN-ul unei specii ca fiind o succesiune de nucleotide: adenină(A), guanină(G), citozină(C) şi timină(T). Task-ul vostru este să identificaţi exemplarele mutante, care au în succesiunea de nucleotide secvenţa **GGTC**. 
Trebuie să creaţi un automat Moore care primeşte la fiecare pas câte o nucleotidă şi, dacă s-a identificat exemplarul mutant, se blochează într-o stare rezervată.

<note tip>
FSM-ul trebuie să fie capabil să identifice pattern-ul menționat și să trateze corect inclusiv input-urile eronate.
(Spre exemplu: "ATCG" este greșit, "AAAAAAA" este greșit, "**GGTC**" este corect, "ATC**GGTC**" este corect, "GGG**GGTC**" este corect, "**GGTC**A" este corect)
</note>

    * intrările automatului vor fi: clock, Reset(pentru posibilitatea introducerii altui ADN) şi cele 4 nucleotide; de exemplu, pentru nucleotide, dacă ordinea parametrilor este A,G,C,T şi se introduce C, intrările vor avea valorile 0,0,1,0
    * automatul va avea o singură ieşire: un LED care, dacă este aprins, semnalează detecţia unui mutant
    * pe placă, intrările vor fi: un switch pentru Reset şi cele 4 push-buttons pentru nucleotide
    * iesirea va fi un led care se aprinde cand se detecteaza un mutant

===== Resurse =====


  * {{:cn1:laboratoare:04:skel:lab04_skel_remote.zip|Scheletul de laborator}}


  * [[https://reference.digilentinc.com/_media/nexys:nexys3:nexys3_rm.pdf|Datasheet Digilent Nexys 3]]
  * [[https://reference.digilentinc.com/_media/nexys:nexys3:nexys3_sch.pdf|Schema Digilent Nexys 3]]
