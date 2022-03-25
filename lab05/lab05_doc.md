===== Laboratorul 05 - Citire si scriere Caractere ASCII =====

=== ===

  * caracterele ASCII
  * exersare lucru cu [[cn1:laboratoare:04|FSM]]-uri

===== =====

==== Tabela ASCII ====

Tabela ASCII este codificarea standard de caractere folosita incepand cu 1960 cand a fost standardizata. Initial acoperea 127 de caractere printabile, ulterior fiind extinsa pana la 255. Fiecare caracter este encodat pe 8 biti.

{{ cn1:laboratoare:05:nou:738px-ASCII-Table.svg.png?600x0 |Tabela ASCII}}

In Verilog un caracter poate fi retinut sub forma unui registru de 8 biti in felul urmator:

<code Verilog>
reg [7:0] ascii_char;

assign ascii_char = "B";
</code>

Pentru a putea construi un string se adauga cate 8 biti pentru fiecare caracter adaugat.

<note>
Trebuie adaugat un byte inclusiv pentru caracterul '\n'.

<code Verilog>
reg [103:0] ascii;

initial begin
    ascii = "Hello World.\n";
end
</code>
</note>

Pentru a lucra cu numere in format ASCII nu uitati ca ele incep de la caracterul '0' (0x30). Regulile aritmetice se aplica in continuare, e.g. '1' = '0' + 1 (0x31 = 0x30 + 1), insa operatiile pe numere formate din doua sau mai multe cifre trebuie cifra cu cifra, adica daca una dintre cifre scade sub 0, decrementata si urmatoarea cifra mai semnificativa.

<code Verilog>
reg [15:0] numar = "10";
...
// numar = numar - 1;
if (numar[7:0] == "0") begin
    numar[7:0] = "9";
    numar[15:8] = numar[15:8] - 1;
end else begin
    numar[7:0] = numar[7:0] - 1;
end
</code>

==== Task ====

Task-urile reprezintă o facilitate a limbajului Verilog care oferă posibilitatea de a scrie cod reutilizabil și mai ușor de înțeles. În acest sens sunt foarte similare cu funcțiile din C/C++. În Verilog există și funcții (denumite Functions) care funcționează similar, însă pentru ceea ce avem noi nevoie în rezolvarea laboratoarelor, task funcționează foarte bine. Task-urile sunt foarte utile pentru testarea funcționalitătilor codului nostru, deoarece permit folosirea de delay-uri.

Utilizăm task-uri atunci când avem de efectuat aceeași operație sau bloc de operații de mai multe ori. Pentru a evita duplicarea codului și volumul de editări de care am avea nevoie pentru a corecta potențialele erori, precum si pentru a face codul mai ușor de citit preferăm să apelăm de oricâte ori este nevoie un task descris o singură dată.

Regulile pentru folosirea de task-uri:

  * Task-urile pot avea oricât de multe input-uri și output-uri
  * Task-urile pot folosi întârzieri (posedge, # delay, etc.)
  * Task-urile pot apela alte task-uri și funcții
  * Task-urile pot modifica variabile globale, definite în exteriorul lor
  * Variabilele declarate în interiorul task-urilor sunt locale acelui task
  * Task-urile pot utiliza atât atribuiri blocante cât și non-blocante


Un exemplu:

<code Verilog>
module traffic_lights;
    reg clock, red, amber, green;
    parameter on = 1, off = 0, red_tics = 350,amber_tics = 30, green_tics = 200;
    
    // initialize colors
    initial
         red = off;
    initial
         amber = off;
    initial
         green = off;

    // sequence to control the lights
    always begin
         red = on;  // turn red light on
         light(red, red_tics); // and wait.
         green = on; // turn green light on
         light(green, green_tics); // and wait.
         amber = on; // turn amber light on
         light(amber, amber_tics); // and wait.
    end

    // task to wait for ’tics’ positive edge clocks
    // before turning ’color’ light off

    task light;
         output color;
         input [31:0] tics;
         begin
             repeat (tics)
                 @(posedge clock);
             color = off; // turn light off
         end
    endtask
    
    
endmodule // traffic_lights
</code>

Mai multe legat de task-uri puteti gasi [[https://www.chipverify.com/verilog/verilog-task|aici]]


==== TL;DR ====

  * Tabela ASCII encodeaza caractere printabile in grupuri de 8 biti
  * In Verilog un grup de caractere este salvat de la cel mai nesemnificatil la cel mai semnificativ
  * Realizăm acest lucru folosind un automat cu stări finite, in care fiecare cifră este o stare.
  * Task este o unealtă extraordinar de utilă care ne permite să scriem cod Verilog care să simuleze funcții.

==== Exerciții ====


**Task 1** (6p). Implementati un lacat care sa se deschida doar dupa introducerea caracterelor "A", "C", "B", "D", in aceasta ordine.

<note>
Pentru verificare, in simulare ar trebui sa vedem out = 1 cand lacatul a fost deschis(automatul a ajuns in starea STATE_UNLOCK). In schelet, la linia 127 se out este setat automat.

	assign out = (state == STATE_UNLOCK ? 1 : 0);

</note>

**Task 2** (4p). Implementati un contor care sa blocheze lacatul timp de 3 unitati de timp daca au fost introduse 3 coduri gresite la rand. Contorul trebuie sa decrementeze un numar in format ASCII. Astfel, comportamentul lacutului devine urmatorul:
  - Lacatul este in starea ''STATE_INIT'' si permite introducerea de noi caractere.
  - Daca este introdus un caracter gresit, lacatul considera codul gresit si incrementeaza contorul de greseli, ''fail''.
    * Daca ''fail'' a ajuns la 3 lacatul trece in starea ''STATE_COUNTER''.
    * Altfel, lacatul revine in starea ''STATE_INIT'', revening la pasul 1.
  - Daca este introdus un caracter corect, lacatul trece in starea ''STATE_1''.
  - Pentru fiecare caracter gresit, lacatul executa pasul 2.
  - Pentru fiecare caracter corect, lacatul executa pasul 3, mergand din starea ''STATE_1'' in starea ''STATE_2'', apoi ''STATE_3'', apoi ''STATE_UNLOCKED''.
  - In starea ''STATE_UNLOCKED'' lacatul se dechide si ramane in aceasta stare, orice caracter ar primi la intrare.
  - In starea ''STATE_COUNTER'' lacatul nu permite introducerea de caractere noi timp de 3 unitati de timp, i.e. decrementeaza un contor, plecand de la valoarea 3, iar cand contorul ajunge la 0 lacatul resetaza contorul de greseli ''fail'' la 0, trece in starea ''STATE_INIT'' si revine la pasul 1.

<note>
Modificati unlock_test.v pentru a testa comportamentul.(e.g. introduceti 3 coduri gresite, dupa introduceti codul bun, lacatul ar trebui sa ramana inchis.

</note>

**Task 3** (2p). Modificati lacatul astfel incat atunci cand e deschis sa poata fi schimbata combinatia de deblocare. Astfel, comportamentul lacatului ramane la fel ca la exercitiul precedent, cu urmatoarele modificari:
  * In starea ''STATE_UNLOCKED'' urmatoarele 4 caractere primite sunt salvate ca un nou cod de deblocare, apoi lacatul revine in starea ''STATE_INIT''.

<note hint>
Veti avea nevoie sa definiti mai multe stari precum ''STATE_UNLOCKED'', e.g. ''STATE_UNLOCKED0'', ''STATE_UNLOCKED1'', etc.
</note>

<note>
Pentru verificare, vom schimba codul initial si vom verifica daca out ajunge de doua ori pe 1. Vom modifica unlock_text.v pentru a defini comportamentul dorit.

</note>

==== Resurse ====

  * {{cn1:laboratoare:05:nou:skel:lab05_v3.zip|Scheletul de laborator}}