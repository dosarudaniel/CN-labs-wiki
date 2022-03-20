===== Laboratorul 09 - Pipelining =====

==== Obiective ====

În acest laborator vom învăța cum funcționează un pipeline pentru instrucțiuni și vom implementa un pipeline simplu.

==== 1. Fazele execuției unei instrucțiuni ====

Execuția unei singure instrucțiuni poate fi împărțită în mai multe etape. Acestea pot fi oricât de multe sau puține și pot avea diverse funcții. O împărțire comună, utilizată de primele procesoare RISC, are 5 stagii:
  * **Instruction fetch** (IF): Instrucțiunea este adusă din memorie într-un registru de procesor.
  * **Instruction decode** (ID): Instrucțiunea adusă anterior este decodificată și sunt determinate unitățile și datele necesare execuției.
  * **Execute** (EX): Acțiunea determinată anterior este executată.
  * **Memory access** (MEM): Dacă este necesar, se accesează memoria de date.
  * **Register write back** (WB): Dacă este necesar, se scriu date în registrele de procesor.

O instrucțiune nu trebuie să folosească toate stagiile disponibile. Spre exemplu, pe o arhitectura [[https://en.wikipedia.org/wiki/Load/store_architecture|load/store]], doar instrucțiunile //load// și //store// accesează memoria, deci folosesc etapa MEM, restul folosesc doar registrele de procesor, deci folosesc etapa WB.

==== 2. Pipelining ====

Execuția normală a instrucțiunilor are loc în mod secvențial. Deci execuția unei instrucțiuni va începe după terminarea ultimului stagiu al instrucțiunii precedente. Astfel, considerând modelul descris mai sus, pentru a executa 3 instrucțiuni avem nevoie de 15 cicli de ceas (considerând că toate instrucțiunile folosesc toate stagiile).

{{ :cn1:laboratoare:09:ex_no_pipeline.png?700 |Execuția fără pipeline}}

A executa instrucțiuni în pipeline înseamnă că este câte o instrucțiune în fiecare stagiu la orice moment dat. În timp ce o instrucțiune este în stagiul de execuție, alta este decodificată, alta este adusă din memorie, etc. Asta duce la o reducere semnificativă a timpului de execuție al unui program. În același timp în care puteam executa doar 3 instrucțiuni fără pipeline, putem executa 11 instrucțiuni în pipeline. 

{{ :cn1:laboratoare:09:ex_pipeline.png?700 |Execuția în pipeline}}

<note>
Execuția fiecărei instrucțiuni în parte durează la fel de mult ca în varianta fără pipeline, însă execuția programului durează mai puțin.
</note>

Trebuie totuși să aplicăm o constrângere asupra stagiilor. Fiecare stagiu trebuie să dureze la fel de mult timp.

==== 3. Hazarde ====

O problemă introdusă de execuția în pipeline sunt hazardele. Un hazard este situația în care execuția următoarei instrucțiuni este imposibilă sau va produce rezultate greșite.

=== 3.1. Hazarde de date ===

Un hazard de date se produce atunci când o instrucțiune are nevoie de un rezultat într-un stagiu al execuției, iar acel rezultat este calculat într-un stagiu al altei instrucțiuni ce va fi executat mai târziu.

^  Hazard  ^  Exemplu  ^  Registru afectat  ^
|  Read After Write (RAW)  |  <code>
R2 <- R1 + R3
R4 <- R2 + R2
</code>  |  R2  |
|  Write After Read (WAR)  |  <code>
R4 <- R1 + R5
R5 <- R1 + R2
</code>  |  R5  |
|  Write After Write (WAW)  |  <code>
R2 <- R4 + R7
R2 <- R1 + R3
</code>  |  R2  |

Aceste hazarde sunt rezolvate prin [[https://en.wikipedia.org/wiki/Bubble_(computing)|bubbling]], execuția out-of-order sau pasarea operanzilor înainte (forwarding).

<note>
Hazardele WAR si WAW pot apărea numai în medii de execuție concurente (e.g. procesoare multi-core).
</note>

=== 3.2. Hazarde structurale ===

Hazardele structurale au loc atunci când două instrucțiuni trebuie să folosească aceeași unitate hardware în același timp. Un astfel de hazard are loc, spre exemplu, într-un sistem cu o singură memorie când se încearcă citirea unei instrucțiuni în același timp cu scrierea rezultatului altei instrucțiuni în aceeași memorie. Aceste hazarde sunt rezolvate prin separarea unităților astfel încât această situație să nu se poată întâmpla (ex: separarea memoriei de instrucțiuni față de memoria de date) sau prin bubbling.

=== 3.3. Hazarde de control ===

Hazardele de control au loc după instrucțiunile de salt condițional. Problema aici este că procesorul trebuie să încarce următoarea instrucțiune înainte de a ști rezultatul saltului, deci s-ar putea să încarce instrucțiunea greșită. Aceste hazarde pot fi rezolvate prin bubbling, dar de obicei sunt tratate de unități specializate de [[https://en.wikipedia.org/wiki/Branch_predictor|branch prediction]].

==== 5. Exerciții ====

Vom lucra pe un procesor extrem de simplu, ce execută instrucțiuni în 5 stagii: IF, ID, EX, MEM și WB.

**Task 01** (4p) Implementați logica procesorului, fără pipeline. Urmăriți TODO-urile marcate ''TODO 1'' din modulele ''cpu'', ''decoder'', ''alu'' și ''memory''.

Structura unei instrucțiuni este următoarea: ''Cod instructiune (16 biți) | operand 1 (8 biți) | operand 2 (8 biți)''. Spre exemplu, instrucțiunea ''ADD R1, R1'' este codificată astfel: ''00000000000000010000000100000001''.

Instructiunea ''NEG'' are un singur operand (''operand 1'').

**Task 02** (4p) Implementați logica de pipelining. Urmăriți TODO-urile marcate ''TODO 2'' din modulul ''cpu_pipelined''.
<note tip>
Din moment ce unele componente ale pipeline-ului folosesc aceleași elemente, avem nevoie să reținem într-o alta variabila elementele scrise de o componentă în ciclul anterior de ceas, de care are nevoie acum componenta următoare. Variabilele care sunt folosite de componente consecutive ale pipeline-ului trebuie reținute și într-un registru special buffered. Spre exemplu, între //memory// și //decoder// avem variabila **instruction** folosită de ambele. Într-un ciclu de ceas, //memory// scrie în **instruction**, dar //decoder// are nevoie de valoarea de la ciclul de ceas anterior. Pentru aceasta, //decoder// va primi variabila **instruction_buffer**, care are valoarea lui **instruction** din ciclul anterior de ceas.
</note>

**Task 03** (2p) Rezolvați hazardele din codul dat ca exemplu. Urmăriți TODO-urile marcate ''TODO 3'' din modulul ''memory''.

<note tip>
Când simulați deschideți fișierul ''Default.wcfg''. Acesta are toate semnalele incluse pentru debugging. Dupa ce ați incărcat fișierul în simulator, apăsați butonul de ''Relaunch''. De asemenea, aveți grijă să nu suprascrieți ''Default.wcfg'' când ieșiți din simulator.
</note>

==== Resurse ====

  * {{:cn1:laboratoare:09:lab09_skel_bis.zip|Scheletul de laborator}}
