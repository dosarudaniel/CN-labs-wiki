===== Laboratorul 02 - Tipuri de descriere a modulelor in Verilog =====

==== ====

În acest laborator, vom învăța despre tipurile de descriere a modulelor în Verilog. Acestea ne ajută să creăm module într-un mod cât mai ușor și eficient.

==== Tipuri de descriere ====

Comportamentul unui modul poate fi descris în trei moduri:
  * la **nivel structural**
  * la **nivel de flux de date**
  * la **nivel procedural**


=== Descrierea la nivel structural ===

Descrierea unui modul la nivel structural se face folosind primitive și module. Acest tip de descriere l-am folosit in [[cn1:laboratoare:01|laboratorul precedent]] și ne ajută să specificăm structura internă a modulului (elemente de circuit și legăturile dintre ele).

|  {{:cn1:laboratoare:02:mux_4_to_1.png?300|Multiplexor 4:1}} | <code Verilog>
module mux_4_to_1(
    output out,
    input in0,
    input in1,
    input in2,
    input in3,
    input sel0,
    input sel1
    );
    
    wire notsel0;
    wire notsel1;
    wire y0;
    wire y1;
    wire y2;
    wire y3;
    
    not(notsel0, sel0);
    not(notsel1, sel1);
    
    and(y0, in0, notsel1, notsel0);
    and(y1, in1, notsel1, sel0);
    and(y2, in2, sel1, notsel0);
    and(y3, in3, sel1, sel0);
    
    or(out, y0, y1, y2, y3);
    
endmodule
</code>  |


=== Descrierea la nivel flux de date ===

Acest tip de descriere specifică relațiile dintre intrări si ieșiri sub forma unor expresii.

|  {{:cn1:laboratoare:02:mux_4_to_1.png?300|Multiplexor 4:1}} | <code Verilog>
module mux_4_to_1(
    output out,
    input in0,
    input in1,
    input in2,
    input in3,
    input sel0,
    input sel1
    );
    
assign out = 
    (in0 & ~sel1 & ~sel0) |
    (in1 & ~sel1 & sel0)  |
    (in2 & sel1 & ~sel0)  |
    (in3 & sel1 & sel0);

endmodule
</code>  |


== Atribuiri continue ==

Atribuirile continue (''assign'') reprezintă relații directe între semnalele. Ele urmează câteva reguli:
  * Variabilă căreia îi atribuim o valoare (variabila din stânga) **trebuie să fie de tip ''wire''**.
  * Expresia din dreapta poate folosi operatori și variabile de tip ''wire'' și ''reg''.
  * Nu o putem folosi în interiorul unui bloc ''initial'' sau ''always''

<code Verilog>
wire a;
reg b;
wire c;

assign c = ~(a & b);
</code>


== Operatori ==

Operatorii pe care îi putem folosi în Verilog sunt:
  * Aritmetici:
    * Unari: ''-'' (schimbare semn - [[http://en.wikipedia.org/wiki/Two's_complement|complementul față de 2]])
    * Binari: ''+'', ''-'', ''*'', ''/'', ''%'' (modulo)
  * Logici:
      * Unari: ''!'' (negare logică - orice nenul devine 0, 0 devine 1)
      * Binari: ''&&'' (și logic), ''||'' (sau logic)
  * Relaționali: ''>'', ''<'', ''>='', ''<nowiki><=</nowiki>'', ''=='', ''!=''
  * Pe biți: 
    * Unari: ''~'' (negare pe biți - [[http://en.wikipedia.org/wiki/Ones%27_complement|complementul față de 1]])
    * Binari: ''&'' (și pe biți), ''|'' (sau pe biți), ''^'' (xor pe biți), ''~^'' sau ''^~'' (exclusive nor - xnor) 
  * Alți operatori:
    * Concatenare: ''{<var0>, ..., <varn>}''  (prin concatenarea unei variabile ''a'', pe 3 biți, cu o variabilă ''b'', pe 4 biți, se obtine o variabilă pe 7 biti ai cărei primi 3 biți sunt cei din ''a'' iar următorii 4 din ''b'')
    * Deplasare stânga (shift left): ''<nowiki><<</nowiki>''
    * Deplasare dreapta (shift right): ''<nowiki>>></nowiki>''
    * Operator ternar: ''(<cond>) ? <expr_true> : <expr_false>;'' (evaluează condiția iar, dacă ea este adevărată, returnează valoarea primei expresii, altfel returnează valoarea celei de-a doua expresii)



=== Descrierea la nivel procedural ===

Folosim acest tip de descriere când dorim să specificăm funcționalitatea modulului într-un mod algoritmic.

|  {{:cn1:laboratoare:02:mux_4_to_1.png?300|Multiplexor 4:1}} | <code Verilog>
module mux_4_to_1(
    output reg out,
    input in0,
    input in1,
    input in2,
    input in3,
    input sel0,
    input sel1
    );
    
    always @(*)
    begin
        case ({sel1, sel0})
            2'b00: out = in0;
            2'b01: out = in1;
            2'b10: out = in2;
            2'b11: out = in3;
            default: out = 1'bx;
        endcase
    end

endmodule
</code>  |


== Blocurile initial și always ==

Blocurile ''initial'' și ''always'' marchează secțiunile de cod procedural ale modulului, iar în interiorul lor se pot folosi construcții de control similare celor din limbajele procedurale.

<note important>
Spre deosebire de descrierea circuitului la nivel structural sau flux de date, la nivel procedural contează ordinea instrucțiunilor.
</note>

Un **bloc ''initial''** este un bloc ce va fi executat o singură dată și se folosește frecvent pentru inițializări sau resetarea circuitului.

<code Verilog >
initial begin
    A = 8'b01010101;        // Atribuim registrului A o valoare binara ('b) pe 8 biti.
    B = {A[0:3], 4’b0000};  // Atribuim registrului B concatenarea intre primii 4 biti
                            // ai registrului A si 4 biti de 0.
    C = 8'h4D;              // Registrul C primeste o valoare in hexazecimal ('h) pe 8 biti.
end
</code>

<note warning>
Blocul ''initial'' poate să nu fie sintetizabil. Pentru a inițializa registrele cu anumite valori este recomandat să folosiți o linie explicită de reset.
</note>

Un **bloc ''always''** este un bloc ce va fi executat continuu, ca o buclă infinită. El poate fi executat încontinuu sau la apariția unui eveniment.

<code Verilog>
input clk;

reg a;
reg b;


always @(posedge clk)
begin
   a <= b;
end
</code>

Construcția ''@(...)'' definiște **lista de sensizitivitate** a blocului ''always'' respectiv. Dacă ea lipsește, atunci blocul se va rula încontinuu. Dacă ea conține vreun semnal atunci blocul va rula doar la apariția unei schimbări a acelui semnal. Fiecare semnal poate fi prefixat cu ''posedge'' sau ''negedge'', pentru a specifica execuția doar la fronturile pozitive sau negative ale semnalului.

<code Verilog>
always begin ... end                // Se va executa încontinuu.
always @(a) begin ... end           // Se va executa la orice tranzitie a semnalului a.
always @(posedge a) begin ... end   // Se va executa la orice tranzitie pozitiva a semnalului a.
always @(negedge a) begin ... end   // Se va executa la orice tranzitie negativa a semnalului a.
always @(a or b) begin ... end      // Se va executa la orice tranzitie a semnalului a sau b.

always @(*)
begin
    b = a;
end                                 // Se va executa la orice tranzitie a oricarui semnal care poate 
                                    // influenta rezultatul blocului (orice semnal citit in interiorul
                                    // blocului). In acest caz, cum o schimbare a semnalului a ar duce la
                                    // schimbarea valorii lui b, blocul se va executa pentru orice 
                                    // tranzitie a semnalului a.                                    </code>


== Construcții de control ==

Construcțiile de control sunt utilizate în secțiunile procedurale de cod, adică în cadrul blocurilor ''initial'' și ''always'', pentru a descrie comportamentul circuitului. Ele sunt:

1. Condiționale: 


  * ''if'':


<code Verilog>
if (sig == 0) 
begin
    a = 2;
end else begin
    a = 1;
end else begin
    a = 0;
end
</code>

  * ''case'':

<code Verilog>
case (sig)
    1’b0: a = 2;
    1’b1: a = 1;
    default: a = 0;
endcase
</code>

2. Bucle:    
  * ''for''

<code Verilog>
for (i = 0; i < 10; i = i + 1) 
begin
    a = a + 5;
end
</code>

  * ''while''

<code Verilog>
i = 0;
while (i < 10) 
begin
    a = a + 5;
    i = i + 1;
end
</code>

  * ''repeat''

<code Verilog>
repeat (10) 
begin
    a = a + 5;
end
</code>

3. Sincronizare:

  * Întârziere: specifică durata de timp între apariția instrucțiunii și momentul executării acesteia.

<code Verilog>
#10 a = a + 1;  // Specifica o intarziere de 10 unitati de timp de simulare inainte de a executa incrementarea.

a = a + 1;
#20;  // Specifica o intarziere de 20 unitati de timp de simulare intre incrementari.
a = a + 1;

#15 a = a + 1; // Asteptam 15 unitati de timp pana la prima incrementare.
b = b + 1;     // A doua incrementare se intampla in acelasi timp.
</code>

4. Eveniment: execuția unei instrucțiuni este determinată de apariția unui eveniment (este de obicei folosit împreună cu blocul ''always'').

<code Verilog>
@r begin
    a = b & c;  // Instrucțiunea se executa doar la modificarea variabilei r.
end
</code>

5. Așteptare: instrucțiunea ''wait'' permite întârzierea unei alte instrucțiuni sau a unui bloc până ce condiția specificată devine adevarată.

<code Verilog>
wait (a == 3) begin
    a = b & c;
end
</code>

<note important>

Diferenta dintre ''wait'' si ''@r'' este explicata [[https://stackoverflow.com/questions/26713683/difference-between-wait-and-statement|aici]].

</note>



== Atribuiri blocante/non-blocante ==


În cadrul blocurilor ''initial'' și ''always'' atribuirile pot fi de două feluri, cu semantici diferite:
  * **Atribuiri blocante**: ''='' - sunt folosite pentru descrierea logicii combinaționale (porți logice). Atribuirile blocante sunt interpretate ca executându-se secvențial, semantica fiind identică cu cea din limbajele de programare procedurale.

<code Verilog >
// Initial a = 0 si b = 1.

a = b;  // a ia valoarea lui b.
b = a;  // b ia valoarea lui a.

// Final a = 1 si b = 1.
</code>

 * **Atribuiri non-blocante**: ''<nowiki><=</nowiki>'' - sunt folosite pentru descrierea logicii secvențiale. Atribuirile non-blocante sunt interpretate ca executându-se **în paralel**, procesul având doi pași:   
       - Partea dreaptă a tuturor atribuirilor este evaluată.
       - Valorile determinate la pasul 1 sunt asignate variabilelor din partea stângă.

<code Verilog>
// Initial a = 0 si b = 1.

a <= b;  // Valoarea lui b este evaluata, dar nu atribuita lui a.
b <= a;  // Valoarea lui a este evaluata, dar nu atribuita lui b.

// Lui a si b le sunt atribuite valorile in acelasi timp.
// Final a = 1 si b = 0.
</code>
 
În cadrul blocurilor ''always'' folosite pentru descrierea **logicii secvențiale** trebuie folosite doar **atribuiri non-blocante**. Pentru a implementa aceast tip de circuite logice, se folosesc blocuri always ce se execută în funcție de semnalul de ceas. 

<code Verilog>
always @(posedge clk) begin
    a <= b;
end
</code>

În cadrul blocurilor ''always'' ce descriu **logică combinațională** se pot folosi doar **atribuiri blocante**. Blocurile pentru logica combinațională se execută la schimbarea unuia dintre semnalele citite în cadrul acestora. 

<code Verilog>
always @(B or C) begin // ASA NU !
    a = b & c;         // B != b și C != c
end
</code>

<note warning>
Pentru a evita greșeli de omitere ale unor semnale din listă putem folosi următoarea sintaxă, în care ''*'' indică toate semnalele care vor fi citite în cadrul blocului.

<code Verilog>
always @(*) begin
    a = b & c;
end
</code>
</note>

În Verilog putem declara variabile de tipul ''reg'' (registru) si ''wire'' (fir). Atunci când se fac atribuiri avem următoarele restricții:
  * Pe o variabilă de tip **''wire''** nu putem face decât **atribuiri continue (assign)**. Acestea trebuie să fie **în afara blocurilor ''always'' sau ''initial''**. Valoarea atribuită poate fi o constantă, o expresie, valoarea unui fir sau a unui registru.
  * Pe o variabilă de tip **''reg''** nu putem face decât **atribuiri blocante/non-blocante**. Acestea trebuie să fie **în interiorul unui bloc ''initial'' sau ''always''**. Valoarea atribuită poate fi o constantă, o expresie, valoarea unui fir sau a unui registru.


----


==== TL;DR ====

  - Comportamentul unui modul poate fi descris în trei moduri:
      * la nivel structural (folosește [[https://ocw.cs.pub.ro/courses/cn1/laboratoare/01#primitive|primitive]] și module)
      * la nivel de flux de date (folosește atribuiri continue și expresii)
      * la nivel procedural (folosește [[https://elf.cs.pub.ro/pp/20/laboratoare/racket/intro#programarea-procedurala|programarea procedurală]]) în blocuri **initial** și/sau **always**
   - În cadrul blocurilor always sau initial întâlnim două tipuri de atribuiri 
      * Atribuirile blocante (**=**) : se execută secvențial
      * Atribuiri non-blocante (** < = **) : se execută în paralel

----



==== Exerciții ====

**Task 0** (tutorial). Descărcați scheletul de laborator. Observați implementarea porții **not**. 
  * Ce tip de descriere a modulului a fost folosit pentru fiecare modul? (modul01, modul02, modul03)
  * Testați comportamentul fiecărui modul prin [[https://ocw.cs.pub.ro/courses/cn1/tutoriale/simulare-ise|simulare]]. Modulele de test sunt deja create.
  * Ce părere aveți despre următorul exemplu ?

|  {{:cn1:laboratoare:02:mux_4_to_1.png?300|Multiplexor 4:1}} | <code Verilog>
module mux_4_to_1(
    output reg out,
    input in0,
    input in1,
    input in2,
    input in3,
    input sel0,
    input sel1
    );

    wire notsel0;
    wire notsel1;
    wire y0;
    wire y1;
    wire y2;
    wire y3;

    not(notsel0, sel0);
    not(notsel1, sel1);
    
    assign y0 = in0 & notsel1 & notsel0;
    assign y1 = in1 & notsel1 &    sel0;
    assign y2 = in2 &    sel1 & notsel0;
    assign y3 = in3 &    sel1 &    sel0;
    
    always @(*)
    begin
        out <= y0 | y1 | y2 | y3;
    end
    
endmodule
</code>  |

<note tip>
În acest exmeplu au fost combinate toate cele 3 tipuri de descriere. De la stânga la dreapta avem descriere: la **nivel structural**, la **nivel de flux de date** și la **nivel procedural**.
</note>

**Task 1** (2p). Implementați o poartă XOR și testați comportamentul ei prin [[https://ocw.cs.pub.ro/courses/cn1/tutoriale/simulare-ise|simulare]].
  * (1p) La nivel flux de date
  * (1p) La nivel procedural

**Task 2** (6p). Implementați un demultiplexor 1:4 și testați comportamentul lui prin [[https://ocw.cs.pub.ro/courses/cn1/tutoriale/simulare-ise|simulare]].
  * (2p) La nivel structural
  * (2p) La nivel flux de date
  * (2p) La nivel procedural

**Task 3** (2p). Implementați un divizor de ceas cu factor de divizare de 1:2 și testați comportamentul lui prin [[https://ocw.cs.pub.ro/courses/cn1/tutoriale/simulare-ise|simulare]]. 

Un divizor de ceas este un modul ce primește ca intrare un semnal de ceas și are ca ieșire un alt semnal de ceas a cărui frecvență, în cazul nostru, trebuie să fie de două ori mai mică decât a semnalului de intrare. Folosiți orice tip de descriere a modulului doriți.

<note tip>
Folosiți următoarea construcție în modulul de test pentru a simula un semnal de ceas:
<code verilog>
reg clk;

always begin
   #5 clk = ~clk;
end

initial begin
   clk = 0;
end
</code>
</note>

<note important>
În cazul în care întâmpinați probleme în scrierea modulelor, puteți consulta [[cn1:resurse:debug|tutorialul de debugging]].
</note>


==== Resurse ====

  * {{:cn1:laboratoare:02:skel:lab02_skel.zip|Scheletul de laborator}}
  * [[cn1:resurse:debug|Tutorial debugging]]
  * {{:cn1:laboratoare:02:vlogref.pdf|Verilog Quick Reference Card}}
  * [[http://inst.eecs.berkeley.edu/~cs150/fa12/resources/Always.pdf|Always @ blocks]]
  * [[http://inst.eecs.berkeley.edu/~cs150/fa12/resources/Nets.pdf|Wire vs reg]]

Alte materiale:
  * [[https://cseweb.ucsd.edu/classes/su13/cse140L-b/handouts/Verilog_Intro.pdf|Verilog crash course]]
  * [[https://retroactive.be/verilog_tips.pdf|Verilog Tips]]
  * [[http://www.csit-sun.pub.ro/~cpop/VerilogHDL_Tools/synver.pdf|Verilog Language Reference]]
