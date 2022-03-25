====== Laboratorul 03 - FPGA ======

===== Obiective =====

În acest laborator vom lucra pentru prima oara cu plăcuţa [[https://reference.digilentinc.com/reference/programmable-logic/nexys-3/reference-manual|Digilent Nexys 3]], ce are încorporat FPGA-ul Spartan6. La finalul acestui laborator veţi înţelege diferența dintre cod simulat și cod sintetizat.

==== 1. FPGA-uri ====

Un FPGA (Field-Programmable Gate Array) este un circuit integrat care poate fi programat pentru a se comporta ca orice alt circuit digital. Spre deosebire de un procesor, care stochează și execută instrucțiuni, programarea unui FPGA înseamnă reconfigurarea hardware a acestuia pentru a realiza funcționalitatea dorită.

FPGA-urile sunt construite dintr-un număr mare de blocuri logice configurabile (eng. configurable logic block - CLB), identice, interconectate printr-o matrice de fire și switch-uri programabile.

Numărul de celule dintr-un FPGA variază de la model la model, putând ajunge până la câteva sute de mii și chiar milioane. Un exemplu este familia [[https://www.xilinx.com/products/silicon-devices/fpga/virtex-7.html#productTable|Virtex-7]] de la Xilinx care poate conține chiar şi 2 milioane de celule logice.

{{ :cn1:laboratoare:03:fpga.png?500 |Structura internă a unui FPGA}}

<spoiler Structura internă a unui CLB simplu>

{{ :cn1:laboratoare:03:clb.png?400 |Structura internă a unui CLB simplu}}

În general, un CLB poate conţine mai multe celule logice ca cea din figura de mai sus. Se pot observa două structuri [[https://electronics.stackexchange.com/q/169532|LUT]] (Look-Up Table), acestea au rolul de a emula un circuit combinaţional cu 3 intrări. Folosind un multiplexor împreună cu cele două LUT putem emula o poartă logică cu 4 intrări. Acest bloc are şi un Full-Adder, alte multiplexoare şi un bistabil de tip D (DFF - D Flip Flop). Aşadar, un CLB poate face parte dintr-un circuit digital combinaţional sau secvenţial complex.

</spoiler>

==== 2. Digilent Nexys 3 ====

{{ :cn1:laboratoare:03:fpga_overview.png?600 |Digilent Nexys 3}}

În cadrul acestui semestru veți folosi plăcuțele [[https://reference.digilentinc.com/reference/programmable-logic/nexys-3/reference-manual|Digilent Nexys 3]], ce au la bază FPGA-ul Spartan-6:
  * Nexys™3 Spartan-6 FPGA 
  * 16 MB RAM
  * alimentarea se va face prin USB 2.0
  * suport pentru USB-USART, port VGA (8 biti), dispozitive IO.
  * 32MB PCM
  * 10/100 Ethernet PHY
  * un port USB pentru mouse/tastatură

Plăcuța se va programa folosind Xilinx ISE Design Suite și un cablu USB.

{{ :cn1:laboratoare:03:placuta_general.jpg?300 |Periferice Digilent Nexys 3}}

==== 3. Sintetizarea folosind Xilinx ISE ====

Implementarea modulelor folosind un FPGA se numește **sintetizare**. Acesta este procesul de a transforma descrierea high-level (high-level design) a unui modul, ce nu are un corespondent direct în hardware, într-o descriere low-level (gate-level design), ce are un corespondent direct în hardware.

După pasul de sintetizare nu putem totuși programa un FPGA. Ne trebuie un fişier UCF (User Constraint File) pentru a asocia pinii fizici ai FPGA-ului la intrările și ieșirile modulului. Structura sa de bază aratǎ în felul următor:

<code Verilog>
NET "A"     LOC = B21;   // Intrarea/Ieşirea numită "A" este rutată la pinul B21 al FPGA.
NET "B<0>"  LOC = B22;   // Bitul 0 al intrării/ieşirii multibit numită "B" este rutat la pinul B22 al FPGA.
NET "B<1>"  LOC = C21;   // Bitul 1 al intrării/ieşirii multibit numită "B" este rutat la pinul C21 al FPGA.
</code>

După ce stabilim constrângerile putem implementa design-ul. Odată implementat, pentru a putea încărca acest design pe un FPGA, trebuie să creăm un fișier de programare, ce va avea extensia //**.bit**// . Folosind un programator și acest fișier vom încărca design-ul pe un FPGA. 

<note warning>
Toți pașii necesari programării unui FPGA folosind Xilinx ISE îi puteți găsi în [[cn1:tutoriale:programming-ise|acest]] tutorial. Vă rugăm să îl parcurgeți!
</note>

<note important>
Implicit design-urile sunt scrise într-o memorie non-volatilă. Design-ul se va pierde în momentul în care resetați (apăsați butonul RESET) sau opriți plăcuța. Există și posibilitatea de a programa în mod permanent plăcuța de laborator (mai multe detalii puteți să găsiți in [[https://reference.digilentinc.com/_media/nexys:nexys3:nexys3_rm.pdf |datasheet]] la pagina 2).
</note>

==== 4. Hello World! ====

Vom implementa un modul în Verilog care are următorul comportament: atunci când apăsăm pe un buton se aprinde un LED, iar când lăsăm butonul se stinge LED-ul.

=== Cum gândim design-ul? ===

1. Stabilim interfața modulului: o intrare (butonul) și o ieșire (LED-ul).

2. Scriem codul verilog pentru modulul nostru.

<code Verilog hello_world.v>
module hello_world(
    output led,
    input button
    );
    
    assign led = button;
endmodule
</code>

3. Simulăm comportamentul. Este indicat să simulăm mai întâi pe calculator, deoarece sintetizarea design-ului poate dura foarte mult.

4. Asociem pinii pe plăcuță.

În figura de mai jos puteți vedea fiecare switch la ce pin al FPGA-ului este conectat. Alegem pinii astfel încât să îndeplinească funcțiile necesare modulului nostru: pinul B8 pentru cele intrare (fiindcă este legat pe placă la un buton) și pinul U16 pentru ieșire (fiindcă este legat la un LED).

{{ :cn1:laboratoare:03:pini.jpg?400 |I/O de bază Digilent Nexys 3}}

5. Generăm fișierul UCF.

<code Verilog hello_world.ucf>
NET "button"        LOC = B8;
NET "led"           LOC = U16;
</code>

6. Sintetizăm și implementăm design-ul. Generăm fișierul de programare. Încărcăm pe placă.

7. ???

8. Profit.

==== 5. Switch debouncing ====

În general în momentul în care apăsǎm un buton sau schimbǎm un întrerupǎtor semnalul la ieşire nu este unul treaptǎ (cum ar fi ideal şi ne-ar ajuta foarte mult). De fapt semnalul arată ca în figura urmǎtoare.

{{ :cn1:laboratoare:03:sw-pulldown.gif?600 |Semnalul la apasarea si lasarea unui buton}}

Din această cauză citirea unui astfel de input poate genera aparenţa mai multor apăsări. Pentru a detecta corect o apăsare trebuie să folosim un circuit (sau o logică) de debouncing. În cea mai simplă formă acesta este un delay: citim semnalul, aşteptăm o perioadă până când acesta s-a stabilizat şi acum luăm în considerare valoarea obţinută. Forma corectă de a proiecta un debouncer este de a ţine minte perioada dintre douǎ schimbări ale semnalului. În cazul în care această perioadă este mai mică decât o valoare de prag considerăm că semnalul nu s-a stabilizat şi valoarea nu este de încredere, altfel valoarea poate fi folosită.

<code Verilog debouncer.v>
// Pseudocod pentru debouncer
module debouncer(
    output reg button_out,
    input clk,
    input reset,
    input button_in
    );

    always @(posedge clk) begin
        if (reset == 1) begin
            // resetăm ieșirea și alte auxiliare
        end else begin
            // Ținem un contor intern de delay, pe care îl creștem
            // Reținem schimbările butonului
            // Actualizăm ieșirea debouncerului doar când contorul revine la 0
        end
    end

endmodule
</code>

Un exemplu de cum folosim debouncerul:
<code Verilog Hello.v>
module Hello(
    output reg button_pressed,
    input button_in,
    input clk,
    input reset
    );
	 
    wire button_debounced;	

    // Instantiem modulul de debouncer si ii dam butonul ca intrare
    // Iesirea modulului (button_debounced) identifica corect apasarea
    // butonului dat ca intrare si va fi folosita in restul programului
    // in locul lui button_in
    debouncer db(button_debounced, clk, reset, button_in);
    
    always @(posedge button_debounced, posedge reset) begin
        if (reset == 1) begin
            button_pressed <= 0;
	end else begin
            button_pressed <= ~button_pressed;
        end
    end
    
endmodule
		
</code>

<spoiler Soluție hardware debouncing>
[[https://youtu.be/-aQH0ybMd3U?t=10m31s|Cu un latch SR]]

[[https://youtu.be/81BgFhm2vz8?t=47|Cu 555 Timer]]

</spoiler>

<note warning>
Oricând lucrăm cu butoane sau întrerupatoare trebuie sa luăm în considerare fenomenul de debouncing. De multe ori întârzierea dată de execuţia codului este de ajuns pentru a ascunde acest fenomen, însă nu trebuie să ne bazăm niciodată pe asta!
</note>

===== TL;DR =====

  * Un **FPGA** e un circuit integrat care poate fi programat pentru a se comporta ca orice alt circuit digital.
  * În cadrul acestui laborator vom folosi plăcuțele FPGA [[https://reference.digilentinc.com/reference/programmable-logic/nexys-3/reference-manual| Digilent Nexys 3]]
  * Implementarea modulelor folosind un FPGA se numește **sintetizare**.
  * Fenomenul de "Bouncing" e întâlnit la apăsarea unui întrerupător - semnalul nu e unul de tip treaptă. Soluția? **Switch debouncing.** 

===== Exerciții =====

**Task 01** (1p) Descarcati scheletul de laborator si sintetizati codul pe placă ([[cn1:tutoriale:programming-ise|Tutorial]]). Analizand fisierul UCF si inscriptiile de pe placa, ce buton trebuie apasat pentru aprinderea ledului?

**Task 02** (4p) Implementați un circuit combinațional cu 4 intrări și 2 ieșiri. Două dintre intrări sunt intrări de date. Celelalte două intrări sunt intrări de selecție și decid ce funcție logică să fie aplicată pe cele două intrări de date pentru a rezulta ieșirile. Una dintre ieșiri este rezultatul funcției logice (i.e. 0 sau 1) iar cea de-a doua este negatul primei ieșiri (i.e. 1 sau 0). Cele 4 funcții logice sunt:
  * XOR    (0,0)
  * NAND   (0,1)
  * OR     (1,0)
  * AND    (1,1)

<note tip>
Conectați cele 4 intrări la 4 switch-uri. Conectați cele 2 ieșiri la două LED-uri.  ([[cn1:laboratoare:03:design|Tutorial de asignare a pinilor]]) Astfel, primul LED va fi aprins când rezultatul funcției este 1, iar cel de-al doilea LED când rezultatul este 0 (doar un led va fi aprins la un moment dat).
</note>

<note tip>
Atunci când creaţi un nou proiect, aveţi grijă să selectaţi dispozitivul corect. Pentru placuţa de la laboratorul curent, trebuie să folosiţi **Spartan 6, XC6LX16-CSG324** (aşa cum scrie pe carcasa placuţei).
</note>

**Task 03** (1p) Implementați un modul debouncer, care primește ca intrare un buton, semnal de ceas și reset. Modulul va detecta corect o apăsare a butonului, eliminând aparența mai multor apăsări. Pentru a vizualiza rezultatul, la apăsarea butonului se va aprinde un LED, iar la o nouă apăsare LED-ul se va stinge. Folosiți modulul Hello de mai sus pentru a vă testa codul, adăugându-l în proiect.

<note tip>
Debouncerul se asigură că o apăsare de buton va fi interpretată de FPGA ca o singură apăsare de buton.
</note>

**Task 04** (2p) Implementați un modul care primește ca intrare un buton, semnal de ceas și reset. La apăsarea butonului se va incrementa un contor intern. Semnalele de ieșire trebuie conectate la cele 8 LED-uri de pe plăcuță. Reprezentați contorul intern în baza 2 folosind ledurile (unde 0 = LED stins, 1 = LED aprins). Modulul va folosi intrarea de reset pentru a reseta contorul intern. Folosiți semnalul de ceas intern al plăcuței.

<note tip>
La fiecare apăsare de buton contorul trebuie incrementat **o singură dată**. Citiți cu atenție capitolul despre [[cn1:laboratoare:03:debouncing| switch debouncing]].

[[https://reference.digilentinc.com/_media/nexys:nexys3:nexys3_rm.pdf|Datasheet]], secţiunea 5, pagina 11. Aflați frecvența și pinul la care trebuie conectată intrarea de ceas pentru a folosi ceasul intern al plăcuței.
</note>

**Task 05** (2p) Modificaţi modulul de mai sus astfel încât contorul să se incrementeze la o secundă, fără să mai fie necesară apăsarea butonului, păstrând opțiunea de reset a contorului.

<note tip>
**Hint:** Folosiți-vă de faptul că știți frecvența ceasului intern al plăcuței.
</note>

===== Resurse =====

  * {{:cn1:laboratoare:03:skel:lab03_skel.zip|Scheletul de laborator}}
  * [[https://reference.digilentinc.com/_media/nexys:nexys3:nexys3_rm.pdf|Datasheet Digilent Nexys 3]]
  * [[https://reference.digilentinc.com/_media/nexys:nexys3:nexys3_sch.pdf|Schema Digilent Nexys 3]]
  * [[https://www.xilinx.com/products/silicon-devices/fpga/what-is-an-fpga.html|Xilinx: What is an FPGA?]]
  * [[https://en.wikipedia.org/wiki/Field-programmable_gate_array|Wikipedia: FPGA]]