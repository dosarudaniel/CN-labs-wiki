===== Laboratorul 10 - Recapitulare =====

==== 1. Obiective ====

În acest laborator ne propunem să definitivăm conceptele învățate pe parcursul semestrului, reamintindu-ne etapele prin care am trecut în construirea UAL-ului și miniprocesorului nostru. Recapitularea va fi făcută sub forma unui joc pe echipe. Va exista un număr prestabilit de taskuri (întrebări sau cerințe), iar echipa care va răspunde corect prima va primi punctajul pe acea întrebare. La final, punctajul se va împărți între echipe, conform numărului de taskuri la care a răspuns în mod corect fiecare.

==== 2. Taskuri ====

Acestea vor fi făcute publice de asistent în timpul laboratorului, unul câte unul. Dacă taskul implică scriere de cod, acesta va fi verificat în simulator de către toți studenții înainte de trecerea la exercițiul următor.

<note warning>
Echipa care răspunde la o întrebare este responsabilă și cu lămurirea întrebărilor din partea colegilor. Dacă echipa nu poate motiva, sau nu poate da un răspuns corect la o întrebare a colegilor/asistentului, atunci va primi punctaj parțial pe task.
</note>

=== Task 0 ===
Completați individual formularul de [[ https://curs.upb.ro/mod/feedbackadm/view.php?id=24911 | Feedback]].

=== Task 1 ===

Avem următorul modul definit (un mux 2:1):

<code verilog>
module mux2to1 (input [1:0] in, input sel, output out);
    assign out = in[sel];
endmodule
</code>

Creați un modul care să reprezinte un mux 4:1 folosindu-vă de declarări repetate ale modului mux2to1.

=== Task 2 ===

Este implementat bine următorul modul? Dacă nu de ce? MyAnd este o poartă **and**, iar MyUal este un mini-ual care pentru sel == 0 face **and** pe intrări, iar pentru 1 face **or**.

  * **2.a**
<code verilog>
module MyAnd(input a, input b, output c)
     assign c = a & b;
endmodule
</code>

  * **2.b**
<code verilog>
module MyAnd (input a, input b, output c);
    reg regc;

    always @(*) begin
            regc = a & b;
    end

    assign c = regc;
end
</code>

  * **2.c**
<code verilog>
module MyAnd(input a, input b, output c);
    if (a == 1 & b == 1) begin
            c = 1;
    end else begin
            c = 0;
    end
endmodule
</code>

  * **2.d**
<code verilog>
module MyUal(input a, input b, input sel, output out);
    always @(*) begin
            if (sel == 0) begin
                    and(c,a,b);
            end else begin
                    or(c,a,b);
            end
    end
endmodule
</code>

  * **2.e**
<code verilog>
module MyUal(input a, input b, input sel, output out);
    always @(*) begin
            if (sel == 0) begin
                    assign out = a & b;
            end else begin
                    assign out = a | b;
            end
    end
endmodule
</code>

  * **2.f**
<code verilog>
module MyUal(input a, input b, input sel, output out);
    always @(*) begin
            if (sel == 0) begin
                    out = a & b;
            end else begin
                    out = a | b;
            end
    end
endmodule
</code>

  * **2.g**
<code verilog>
module MyUal(input a, input b, input sel, output out);
    reg rego;

    always @(*) begin
            rego <= a & b;
            if (sel == 1) begin
                    rego <= a | b;
            end
    end

    assign out = rego;
endmodule
</code>

  * **2.h**
<code verilog>
module MyUal(input a, input b, input sel, output out);
    reg rego;

    always @(*) begin
            rego = a & b;

            if (sel == 1) begin
                    rego = a | b;
            end
    end

    assign out = rego;
endmodule
</code>

  * **2.i**
<code verilog>
module MyUal(input a, input b, input sel, output out);
    reg rego;

    always @(*) begin
            assign rego = (sel == 0) ? a & b : a | b;
    end

    assign out = rego;
endmodule
</code>

  * **2.j**
<code verilog>
module MyAnd(input[3:0] a, input[3:0] b, output[3:0] c);
    and(c, a, b);
endmodule
</code>

=== Task 3 ===

Care dintre următoarele secvenţe de cod sunt greșite? De ce?

<code verilog>
module MyAndXor(input a, input b, output oand, output oxor);
    assign oand = a & b;
    assign oxor = a ^ b;
endmodule
</code>

MyMod face operatia a ^ (a & b).

  * **3.a**
<code verilog>
module Mymod(input a, input b, output c);
    wire o1;

    MyAndXor (a, b, o1);
    MyAndXor(.oxor(c), .a(a), .b(o1));
endmodule
</code>

  * **3.b**
<code verilog>
module Mymod(input a, input b, output c);
    wire o1, o2;

    MyAndXor m1 (.a(a),  .oand(o1), .oxor(o2));
    MyAndXor m2 (o1, a, o2, c);
endmodule
</code>

  * **3.c**
<code verilog>
module Mymod(input a, input b, output c);
    reg o1, o2;

    MyAndXor m1 (.a(a), .b(b),  .oand(o1));
    MyAndXor m2 (o1, a, o2, c);
endmodule
</code>

=== Task 4 ===

Este vreo diferență între comportamentele următoarelor module?

<code verilog>
module mux2to1 (input [1:0] in, input sel, output out);
    wire notsel, and1, and2;

    not(notsel, sel);
    and(and1, in[0], notsel);
    and(and2, in[1], sel);
    or(out, and1, and2);
endmodule
</code>

<code verilog>
module mux2to1 (input [1:0] in, input sel, output out);
    assign out = (sel == 0) ? in[0] : in[1];
endmodule
</code>

<code verilog>
module mux2to1 (input [1:0] in, input sel, output out);
    reg rego;

    always @(*) begin
            case (sel)
                1’b0: rego =in[0];
                1’b1: rego =in[1];
                default rego = 1’bx;
            endcase
    end

    assign out = rego;
endmodule
</code>

=== Task 5 ===

Este vreo diferență între comportamentele următoarelor module?

<code verilog>
module Mymod(input a, input b, output c);
    reg op1, op2;

    initial begin
            op1 = 0;
            op2 = 0;
    end

    always @(*) begin
            op1 = a & b;
            op2 = a ^ op1;
    end

    assign c = op2;
endmodule
</code>

<code verilog>
module Mymod(input a, input b, output c);
    reg op1, op2;

    initial begin
            op1 = 0;
            op2 = 0;
    end

    always @(*) begin
            op1 <= a & b;
            op2 <= a ^ op1;
    end

    assign c = op2;
endmodule
</code>

<code verilog>
module Mymod(input a, input b, output c);
    assign c = a ^ (a & b);
endmodule
</code>

<code verilog>
module Mymod(input a, input b, output c);
    reg op1, op2;

    initial begin
            op1 = 0;
            op2 = 0;
    end

    always @(a,b) begin
            op1 = a & b;
            op2 = a ^ op1;
    end

    assign c = op2;
endmodule
</code>

=== Task 6 ===

Creați un debouncer în Verilog.

=== Task 7 ===

Faceți un automat Moore pentru următorul fsm implementat în Verilog:

<code verilog>
`timescale 1ns / 1ps
module fsm(
    input clk,
    input reset,
    input in,
    output out
    );

    reg [1:0] currentState;
    reg [1:0] nextState;
    localparam STATE_Initial = 2'd0,
    STATE_1 = 2'd1,
    STATE_2 = 2'd2,
    STATE_3 = 2'd3;
 
    assign out = (currentState == STATE_3);
 
    always@ ( posedge clk ) begin
      if ( reset )
             currentState <= STATE_Initial;
      else
             currentState <= nextState;
      end
    end
 
    always@ ( * ) begin
          nextState = currentState;
 
          case ( currentState )
              STATE_Initial : begin
                        if(in == 0) begin
                            nextState = STATE_1;
                        end else begin
                            nextState = STATE_2;
                        end
              end
              STATE_1 : begin
                        if(in == 0) begin
                            nextState = STATE_1;
                        end else begin
                            nextState = STATE_2;
                        end
              end
              STATE_2 : begin
                        if(in == 0) begin
                            nextState = STATE_2;
                        end else begin
                            nextState = STATE_3;
                        end
              end
              STATE_3 : begin
                        if(in == 0) begin
                            nextState = STATE_1;
                        end else begin
                            nextState = STATE_2;
                        end
              end
              default: begin
                        nextState = STATE_Initial;
              end
          endcase
    end
endmodule
</code>

=== Task 8 ===

Este următorul modul o implementare corectă al unui sumator ripple carry? Modulul FullAdder este implementat corect și face ce îi spune numele.

<code verilog>
module RippleCarry(
    input[7:0] A,
    input[7:0] B,
    input Cin,
    output[7:0] S,
    output Cout
    );
    wire[6:0] C;

    FullAdder fa0(.A(A[0]),.B(B[0]),.Cin(Cin),.S(S[0]),.Cout(C[0]));
    FullAdder fa4(.A(A[4]),.B(B[4]),.Cin(C[3]),.S(S[4]),.Cout(C[4]));
    FullAdder fa5(.A(A[5]),.B(B[5]),.Cin(C[4]),.S(S[5]),.Cout(C[5]));
    FullAdder fa6(.A(A[6]),.B(B[6]),.Cin(C[5]),.S(S[6]),.Cout(C[6]));
    FullAdder fa1(.A(A[1]),.B(B[1]),.Cin(C[0]),.S(S[1]),.Cout(C[1]));
    FullAdder fa2(.A(A[2]),.B(B[2]),.Cin(C[1]),.S(S[2]),.Cout(C[2]));
    FullAdder fa3(.A(A[3]),.B(B[3]),.Cin(C[2]),.S(S[3]),.Cout(C[3]));
    FullAdder fa7(.A(A[7]),.B(B[7]),.Cin(C[6]),.S(S[7]),.Cout(Cout));
endmodule
</code>

=== Task 9 ===

Este implementat corect următorul modul de FullAdder?

<code verilog>
module FullAdder(
    input A,
    input B,
    input Cin,
    output S,
    output Cout
    );
    wire S1, C1, C2;

    HalfAdder ha1(.A(A),.B(B),.S(S1),.C(C1));
    HalfAdder ha2(.A(S1),.B(Cin),.S(S),.C(C2));
    xor(Cout, C1, C2);
endmodule
</code>

=== Task 10 ===

Este implementat corect următorul modul de HalfAdder?

<code verilog>
module HalfAdder(
    input A,
    input B,
    output S,
    output C
    );
    assign S = A | B;
    assign C = A & B;
endmodule
</code>

=== Task 11 ===

Este implementat corect următorul modul de scăzător?

<code verilog>
module Scazator(
    input[7:0] A,
    input[7:0] B,
    output[7:0] D,
    output Neg
    );
    wire Cout;

    Exercitiu_2_RippleCarry rc(.A(A),.B(~B),.Cin(1),.S(D),.Cout(Cout));
    assign Neg = D[7];
 
endmodule
</code>

=== Task 12 ===

Este bine implementat următorul modul de FullAdder pentru CarryLookAhead?

<code verilog>
module FullAdder(
    input A,
    input B,
    input Cin,
    output S,
    output P,
    output G
    );
    wire S1, C1, C2;
    HalfAdder ha1(.A(A),.B(B),.S(S1));
    HalfAdder ha2(.A(S1),.B(Cin),.S(S));
    assign P = A | B;
    assign G = A ^ B;
endmodule
</code>

=== Task 13 ===

Care din implementările următoare ale operației **MUL** sunt corecte?

  * **13.a**
<code verilog>
module POARTA_MUL(
    input[3:0] A,
    input[3:0] B,
    output[7:0] out
    );
 
    reg[8:0] P;
    reg[8:0] Aux;
    reg[8:0] S;
    reg[3:0] negA;
    reg var_aux;
    integer i;

    always @(*) begin
             P = {4'b0000, B, 1'b0};
             negA = ~A + 1;
             Aux = {A, 5'b00000};
             S = {negA, 5'b00000};

             for (i = 0; i < 4; i = i + 1) begin
                     case (P[1:0])
                        2'b00: begin
                        end
                        2'b01: begin
                                P = P + Aux;
                        end
                        2'b10: begin
                                P = P + S;
                        end
                        2'b11: begin
                        end
                    endcase
 
                    var_aux = P[8];
                    P = P >> 1;
                    P[8] = var_aux;
            end
    end
 
    assign out = P[8:1];
endmodule
</code>

  * **13.b**
<code verilog>
module POARTA_MUL(
    input[3:0] A,
    input[3:0] B,
    output[7:0] out
    );
 
    reg[8:0] P;
    reg[8:0] Aux;
    reg[8:0] S;
    reg[3:0] negA;
    reg var_aux;
    integer i;

    always @(*) begin
             P = {4'b0000, B, 1'b0};
             negA = ~A + 1;
             Aux = {A, 5'b00000};
             S = {negA, 5'b00000};

         for (i = 0; i < 4; i = i + 1) begin
             case (P[1:0])
                        2'b00: begin
                        end
                        2'b01: begin
                                P = P + Aux;
                        end
                        2'b10: begin
                                P = P + S;
                        end
                        2'b11: begin
                        end
                    endcase
 
                    P = P >>> 1;
            end
    end
 
    assign out = P[8:1];
endmodule
</code>

  * **13.c**
<code verilog>
module POARTA_MUL(
    input[3:0] A,
    input[3:0] B,
    output[7:0] out
    );
 
    reg[8:0] P;
    reg[8:0] Aux;
    reg[8:0] S;
    reg[3:0] negA;
    reg var_aux;
    integer i;

    always @(*) begin
             P = {4'b0000, B, 1'b0};
             negA = ~A + 1;
             Aux = {A, 5'b00000};
             S = {negA, 5'b00000};

             for (i = 0; i < 4; i = i + 1) begin
                     case (P[1:0])
                        2'b00: begin
                        end
                        2'b01: begin
                                P = P + Aux;
                        end
                        2'b10: begin
                                P = P + S;
                        end
                        2'b11: begin
                        end
                    endcase
 
                    P = P >> 1;
            end
    end
 
    assign out = P[8:1];
endmodule
</code>

  * **13.d**
<code verilog>
module POARTA_MUL(
    input[3:0] A,
    input[3:0] B,
    output[7:0] out
    );
 
    reg[8:0] P;
    reg[8:0] Aux;
    reg[8:0] S;
    reg[3:0] negA;
    reg var_aux;
    integer i;

    always @(*) begin
             P = {4'b0000, B, 1'b0};
             negA = ~A + 1;
             Aux = { 5'b00000, A};
             S = {,5'b00000, negA};

             for (i = 0; i < 4; i = i + 1) begin
                     case (P[1:0])
                        2'b00: begin
                        end
                        2'b01: begin
                                P = P + Aux;
                        end
                        2'b10: begin
                                P = P + S;
                        end
                        2'b11: begin
                        end
                    endcase
 
                    var_aux = P[8];
                    P = P >> 1;
                    P[8] = var_aux;
            end
    end
 
    assign out = P[8:1];
endmodule
</code>

  * **13.e**
<code verilog>
module POARTA_MUL(
    input[3:0] A,
    input[3:0] B,
    output[7:0] out
    );
 
    reg[8:0] P;
    reg[8:0] Aux;
    reg[8:0] S;
    reg[3:0] negA;
    reg var_aux;
    integer i;

    always @(*) begin
             P = {4'b0000, B, 1'b0};
             negA = ~A + 1;
             Aux = {negA, 5'b00000};
             S = {A, 5'b00000};

             for (i = 0; i < 4; i = i + 1) begin
                     case (P[1:0])
                            2'b00: begin
                        end
                        2'b01: begin
                                P = P + Aux;
                        end
                        2'b10: begin
                                P = P + S;
                        end
                        2'b11: begin
                        end
                    endcase
 
                    var_aux = P[8];
                    P = P >> 1;
                    P[8] = var_aux;
            end
    end
 
    assign out = P[8:1];
endmodule
</code>

=== Task 14 ===

Care dintre următoarele module implementează corect un contor în care numărul decrementat este în format ASCII?

  * **14.a**

<code verilog>
module counter(
output reg [15:0] counter,
output reg done,
input [15:0] ascii_in,
input decrement,
input reset,
input clock
    );

    always@(posedge clock) begin
        if(reset)
            counter <= ascii_in;
            else begin
               if(decrement) begin
                   if(counter == "01") begin
                        counter = "UN";
                        done = 1;
                    end else begin
                        if (counter[7:0] == "0") begin
                            counter[7:0] = "9";
                           counter[15:8] = counter[15:8] - 1;
                        end else begin
                           counter[7:0] = counter[7:0] - 1;
                        end
                    end
                end
            end
        end
endmodule
</code>

  * **14.b**

<code verilog>
module counter(
output reg [15:0] counter,
output reg done,
input [15:0] ascii_in,
input decrement,
input reset,
input clock
    );

    always@(posedge clock) begin
        if(reset)
            counter <= ascii_in;
            else begin
               if(decrement) begin
                   if(counter == "01") begin
                        counter = "UN";
                        done = 1;
                    end else begin
                        if (counter[15:8] == "0") begin
                            counter[15:8] = "9";
                           counter[7:0] = counter[7:0] - 1;
                        end else begin
                           counter[15:8] = counter[15:8] - 1;
                        end
                    end
                end
            end
        end
endmodule
</code>

=== Task 15 ===

Care dintre următoarele stringuri în format ASCII este construit corect?

  * **15.a**

<code verilog>
reg [9:0] ascii;
 
initial begin
    ascii = "Bye, CN1.\n";
end
</code>

  * **15.b**

<code verilog>
reg [79:0] ascii;
 
initial begin
    ascii = "Bye, CN1.\n";
end
</code>

  * **15.c**

<code verilog>
reg [8:0] ascii;
 
initial begin
    ascii = "Bye, CN1.\n";
end
</code>

  * **15.d**

<code verilog>
reg [71:0] ascii;
 
initial begin
    ascii = "Bye, CN1.\n";
end
</code>

=== Task 16 ===

Descrieți pe scurt conceptul de pipelinening, enumerând prin ce etape trece o instrucțiune. Ce probleme pot apărea în cazul pipelineningului și cum se pot corecta?

=== Task 17 ===

Care sunt diferențele dintre circuitele logice combinaționale și cele secventiale?

=== Task 18 ===

Ce tipuri de atribuiri pot fi folosite într-un modul Verilog si care sunt diferențele dintre acestea?

=== Task 19 ===

Comparați cele 3 tipuri de descriere ale modulelor în Verilog.

=== Task 20 ===

Declarați și instanțiați un modul care are:
un port de intrare si iesire ''data'' pe 16 biți cu LSB în stânga;
un port de ieșire ''valid'' pe un bit, de tip registru;
un port de intrare ''address'' pe 8 biți cu MSB în stânga;
un port de intrare ''write'' pe un bit;
un port de intrare ''row'' pe un bit.

Înainte de a îl instanția, declarați și variabilele necesare instanțierii.

=== Task 21 ===

Ce valori vor avea x, y, z la finalul secventei următoare de cod, știind că inițial ''x = 6'', ''y = 2'', ''z = 4'' și că au lățimea de 3 biti?

<code Verilog>
x <= x & y;
y <= x & z;
z <= y ^ x;
</code>

=== Task 22 ===

Ce valori vor avea x, y, z la finalul secventei următoare de cod, știind că inițial ''x = 6'', ''y = 2'', ''z = 4'' și că au lățimea de 3 biti?

<code Verilog>
x = x & y;
y = x & z;
z = y ^ x;
</code>

=== Task 23 ===

Identificati greseliile din următorul modul Verilog.

<code Verilog>
module mux_4_to_1(
    output reg out,
    input in0,
    input in1,
    input in2,
    input in3,
    input sel0,
    input sel1
    );
 
    always @*
        case ([sel1, sel0])
            1'b00: out = in0;
            2'b01: out = in1;
            2'b10: out = in2;
            2'b11: out = in3;
            default: out = 1b’x;
        end
    end
endmodule
</code>

=== Task 24 ===

Corectați următorul modul Verilog care contine o intrare de selectie pe 3 biti, 2 intrari a si b pe 2 biti si o iesire tot pe 2 biti. În funcție de selecție, ieșirea va avea următoarele valori:

  * ''sel == 1'': se aplică NAND pe cele 2 intrări;

  * ''sel == 2'': se aplică OR pe cele 2 intrări;

  * ''sel == 4'': ieșirea modulului random (primul parametru este ieșirea, apoi sunt cele 2 intrări care ar trebui sa fie a și b);

<code verilog>
module complete_me(output [2:0] out, input [2:0] a, input [2:0] b, input [3:0] sel);
    wire out_nand;
    wire out_or;
    wire out_random;

    always @(*) begin
        random(out_random, a, b);
    end

    always @(*) begin
        out_nand <= ~(a & b);
        out_or = or(a, b);
    end

    case(sel)
        3’b0: out = out_nand;
        3’b1: out = out_or;
        3’b2: out = out_random;
        default: out = 2’bx;
    endcase
endmodule
</code>

Secvențele greșite de cod se pot înlocui cu următoarele răspunsuri:
<code Verilog>
output [2:0] out, input [2:0] a, input [2:0] b, input [3:0] sel
</code>

<code Verilog>
output out, input a, input b, input sel
</code>

<code Verilog>
output [1:0] out, input [1:0] a, input [1:0] b, input [2:0] sel
</code>

<code Verilog>
wire out_nand;
reg out_or;
wire out_random;
</code>

<code Verilog>
wire out_or;
reg out_nand;
reg out_random;
</code>

<code Verilog>
reg out_nand;
reg out_or;
wire out_random;
</code>

<code Verilog>
random(out_random, a, b);
</code>

<code Verilog>
always @(*) begin
    random rand(out_random, a, b);
end
</code>

<code Verilog>
random rand(out_random, a, b);
</code>

<code Verilog>
assign out_nand = ~(a & b);
</code>

<code Verilog>
out_nand = nand(a, b);
</code>

<code Verilog>
nand(a, b);
</code>

<code Verilog>
out_or = or(a, b);
</code>

<code Verilog>
assign out_or = a | b;
</code>

<code Verilog>
or(a, b);
</code>

<code Verilog>
assign out = sel == 3’b001 ? out_nand : 3’b010 ? out_or : 3’b100 ? out_random : 2’bx;
</code>

<code Verilog>
if (sel == 1)
    out = out_nand;
else if (sel == 2)
    out = out_or;
else if (sel == 4)
    out = out_random;
else
    out = 2’bx;
end
</code>

<code Verilog>
if (sel == 3’b0)
    out = out_nand;
else if (sel == 3’b1)
    out = out_or;
else if (sel == 3’b2)
    out = out_random;
else
    out = 2’bx;
end
</code>



==== 3. Problemă examen ====

În vederea pregătirii problemei pentru examen (a.k.a proba numita “problema”), trebuie să aveți în vedere cele 2 mari tipuri de probleme prezentate la curs:
  * BRANCH PREDICTOR
  * DATA HAZARDS IN PIPELINE

Pentru ambele tipuri de probleme, găsiți teoria necesara în cursul de CN de pe moodle. Pe lângă exemplele din curs, mai jos am atașat câte un model complet de redactare pentru fiecare subiect.

În plus, puteți consulta următoarele link-uri:
  * [[https://en.wikipedia.org/wiki/Branch_predictor#One-level_branch_prediction|Branch predictor]]
  * [[http://web.cs.iastate.edu/~prabhu/Tutorial/PIPELINE/hazards.html|Hazard in pipeline]]
  * [[http://web.cs.iastate.edu/~prabhu/Tutorial/PIPELINE/dataHaz.html|Data hazards in pipeline]]
  * [[https://en.wikipedia.org/wiki/Amdahl%27s_law|Amdahl's law]]
  * [[https://en.wikipedia.org/wiki/Speedup|Speedup]]

<note tip>
**ATENȚIE!** Vă rugăm ca în redactarea problemelor să folositi notațiile/convențiile prezentate la curs și la laborator.
</note>

== Exemplu problemă BRANCH PREDICTOR ==

Fie următorul cod în assembly (simplificat):
<code asm>
mov ax, $a
mov bx, $b
 
_while:
    cmp ax, bx  ; ax ? bx
 
    jg _greater ; ax > bx ?
    jl _less    ; ax < bx ?
    je _pwp     ; ax == bx ?
 
    jmp _while
 
_greater:
    sub ax, bx ; ax = ax - bx
    jmp _while
 
_less:
    sub bx, ax ; bx = bx - ax
    jmp _while
 
_pwp: ;se afiseaza conținutul lui ax

</code>

<note tip>
**Observații**
  - Recapitulare assembly
    * ''jmp'' este instrucțiune ce implementează un salt necondiționat
    * ''jg'', ''jl'', ''je'' sunt instrucțiuni ce implementează salturi CONDITIONATE (doar pentru acestea Branch Predictorul poate optimiza lucruri)
    * ''add''/''sub'', ''cmp'' sunt folosite pentru adunare/scadere și comparare
    * ''mov'' este instrucțiunea care copiază în destinație (primul operand), conținutul sursei (al doilea operand)
  - În acest exemplu, instrucțiunile au fost explicate, atat în această secțiune cat și în cod (prin comentarii). Ne așteptăm ca la examen să știți ce fac instrucțiunile: ''add''/''sub'', ''je''/''jg''/''jl''/''jmp'', ''cmp'', ''inc'', load (''ld'') /store (''st'')/''mov''. De asemenea, ce este un registru (ex. ''ax''/''eax'', ''bx''/''ebx'', ''cx''/''ecx'', ''dx''/''edx'' sau ''r1'', ''r2'', ''r3'' …), cum luăm valoarea de la o adresa (''b''), sintaxa pentru instrucțiuni (ex. ''mov reg destinație reg_sursa'').
</note>

Se știu numărul de cicli de ceas pentru fiecare tip de instrucțiune: mov - 1 ciclu, cmp - 1 ciclu, sub - 1 ciclu, branch - 20 cicli (dacă nu a fost prezis corect) sau 2 cicli (dacă a fost prezis corect), iar frecvența procesorului este de 1GHz.

Se va cere rezolvarea unui subiect asemănător cu unul dintre următoarele:

A. Fie procesorul A care implementează un branch predictor **global**, iar metoda de branch prediction folosită este **1-bit counter** (taken/not taken). Starea inițială este not taken.
  * Ce valoare afișează programul?
  * Enumerați prin ce stări trece branch predictorul, semnalând dacă branchul a fost prezis corect sau greșit.
  * Câte branchuri au fost prezise corect?
  * Câte branchuri au fost prezise incorect?
  * Care este timpul de rulare al programului?


B. Fie procesorul B care implementează un branch predictor **global**, iar metoda de branch prediction folosită este **2-bit counter** (strongly taken/weak taken/weak not taken/strongly not taken). Starea inițială este strongly not taken.
  * Ce valoare afișează programul?
  * Enumerați prin ce stări trece branch predictorul, semnalând dacă branchul a fost prezis corect sau greșit.
  * Câte branchuri au fost prezise corect?
  * Câte branchuri au fost prezise incorect?
  * Care este timpul de rulare al programului?

C. Fie procesorul C care implementează un branch predictor **local**, iar metoda de branch prediction folosită este **1-bit counter** (taken/not taken). Starea inițială este not taken.
  * Ce valoare afișează programul?
  * Enumerați prin ce stări trec branch predictorii, semnalând dacă branchul a fost prezis corect sau greșit.
  * Câte branchuri au fost prezise corect?
  * Câte branchuri au fost prezise incorect?
  * Care este timpul de rulare al programului?

D. Fie procesorul D care implementează un branch predictor **local**, iar metoda de branch prediction folosită este **2-bit counter** (strongly taken/weak taken/weak not taken/strongly not taken). Starea inițială este strongly not taken.
  * Ce valoare afișează programul?
  * Enumerați prin ce stări trec branch predictorii, semnalând dacă branchul a fost prezis corect sau greșit.
  * Câte branchuri au fost prezise corect?
  * Câte branchuri au fost prezise incorect?
  * Care este timpul de rulare al programului?


<spoiler Enunt vechi si rezolvare>
 Avem 2 procesoare, fiecare având frecvența de 1GHz, care execută acest cod, procesoarele A și B. Metoda de branch prediction folosită de fiecare este:

**A.** 1-bit counter (taken/not taken)

**B.** 2-bit counter (strongly taken/weak taken/weak not taken/strongly not taken)

Ambele pornesc cu o abordare pesimistă, counterul/state machine-ul fiecăreia având starea inițială pentru procesorul A not taken și pentru B strongly not taken. De altfel, ambele implementează un branch predictor global, astfel că există un singur counter/state machine global, pentru toate branch-urile. Se știu numărul de cicli de ceas pentru fiecare tip de instrucțiune: mov - 1 ciclu, cmp - 1 ciclu, sub - 1 ciclu, branch - 20 cicli (dacă nu a fost prezis corect) sau 2 cicli (dacă a fost prezis corect).

Se cer următoarele:

**1.** Pentru input-ul a=26 și b=34 să se evidențieze prin ce stări trec branch-predictor-ele celor 2 procesoare, semnalându-se și care branch-uri au fost prezise corect, respectiv incorect. (4p)

**2.** Să se calculeze timpul de rulare al programului pe cele 2 procesoare, pentru input-ul de la punctul 1. (3p)

**3.** Fie un procesor C, cu frecvență de 1Ghz, care are aceeași metoda de branch prediction pe 2 biți ca procesorul B, implementând însă un branch predictor local (câte un counter/state machine pentru fiecare branch). Execută procesorul C codul dat mai repede decât procesorul A sau B, pentru input-ul de la punctul 1? Argumentați. Care este speed-up față de B? (3p)

[[https://docs.google.com/spreadsheets/d/1VR7XeqPSfzxMAJiE3ClhReDBkI_NgrWj0_gOplrMnFU/edit#gid=0​|Rezolvare problema]]
</spoiler>​

== Exemplu problemă DATA HAZARDS IN PIPELINE ==
Fie următorul cod în assembly (x86). Indentificați hazardele de date. Pentru fiecare menționați intrucțiunile implicate, arătând cauzele fiecărui hazard.

Treceți hazardele în ordinea crescătoare a instrucțiunilor.

Exemplu: Dacă există:
    * un hazard de tipul RAW între liniile x și y (x < y) din cauza registrului eax
    * un hazard de tipul RAW între liniile x și z (x < z, y < z) din cauza registrului eax
    * un hazard de tipul RAW între liniile t și z (x < t, t < z) din cauza registrului eax

 atunci veți scrie:

Ix - Iy: RAW (eax)

Ix - Iz: RAW (eax)

It - Iz: RAW (eax)

<code asm>
0: mov ebx, edx             ; ebx = edx
1: add ecx, ebx             ; ecx = ecx + ebx
2: mov eax, [0xCAFEBABE]    ; eax = MEM[0xCAFEBABE]
3: mov edx, eax             ; edx = eax
4: xor edi, eax             ; edi = edi ^ eax
</code>

==== 4. Model colocviu ====

Colocviul va fi dat pe Moodle. Regulamentul se află [[https://ocw.cs.pub.ro/courses/cn1/regulament|aici]].

Formatul acestuia va fi următorul:
  * 4 subiecte de câte 2.5 puncte
  * Timp de lucru: 40 minute.
  * Colocviul verifică toate cunoștințele din materia predată la laborator, printre care și noțiuni de Verilog (veți scrie cod în editorul de text de pe Moodle).

Puteți să consultați, pentru a vă familiariza cu formatul, și [[ https://curs.upb.ro/mod/quiz/view.php?id=311654 | Modelul de colocviu]].

Exemple de subiecte:
  * Subiectul 1 va fi o întrebare de teorie/o comparație/o definiție/un algoritm/o idee/un principiu/etc din materia de laborator.
  * Subiectul 2 poate să ceară menționarea unor concepte + exemplificare prin cod Verilog (ex. semnătură module, instanțiere module, conversie între 2 tipuri diferite de descriere a unui modul) etc.
  * Subiectul 3 va presupune corectarea unui modul/o secvență de cod Verilog, determinarea valorilor unor semnale dintr-o secvență de cod Verilog.
  * Subiectul 4 verifică faptul că puteți realiza diagrama unui FSM Moore/Mealy care să facă ceva simplu (ex. recunoașterea unei secvențe). Pentru mai multe detalii legate de cum funcționează tool-ul folosit pentru acest exercițiu puteți consulta secțiunea de linkuri utile. Acesta va fi deja integrat în instanța de test de pe Moodle.
  * Indicii 1, 2, 3, 4 sunt orientativi. (Ex. Subiectul 1 poate sa fie FSM Moore, iar subiectul 4 poate sa fie o întrebare. Ați prins ideea. :D)

Mai jos puteți consulta un model.

<code verilog>
Colocviu CN1

(2.5p) Explicați cum funcționează un sumator Carry Look Ahead și care sunt avantajele
lui față de un sumator Ripple Carry.
 
 
(2.5p) Declarați și instanțiați un modul denumit converter care are:
- o intrare reprezentată de un șir în format ASCII de 10 caractere (denumită error_msg);
- o intrare reprezentată de un număr în format ASCII de 3 cifre (denumită num_ascii);
- un port de ieșire pe un bit, de tip registru (denumit status);
- un port de ieșire pe 10 biți cu MSB în stânga (denumit num).

Înainte de a îl instanția, declarați și variabilele necesare instanțierii. Instanțierea
trebuie făcută în cel puțin 2 moduri diferite.
 
(2.5p) Identificați greșelile din următorul modul Verilog.

module fix-me(output reg out, output ready, input [1:0] active, input a, input b, input clk)
    wire a_act;
    reg b_act;

    initial begin
        ready = 0;
    end

    always @(clk)
        ready <= ~ready;
    end

    assign a_act = and(a, active[0]);
    assign b_act = b & active(1);

    out <= a_act & b_act;
end    
 
(2.5p) Creați diagrama pentru un automat cu stări de tip Moore care primește intrările '1'
și '0' și are ca ieșire "da" sau "nu". Automatul trebuie să recunoască secvențe care conțin
o anumită subsecvență.

Subsecvența va consta în reprezentarea binară (pe numărul minim de biți) a numărului de litere
din prenumele și numele vostru. Dacă aveți mai multe prenume, atunci alegeți unul singur.

Exemplu:
Pentru Andrei-Theodor Popa => 6 (Andrei) + 4 (Popa) = 10 litere => 1010
"1010" => ieșire "da"; intrare "111010" => ieșire "da"; intrare "101001" => ieșire "da";
intrare "1111" => ieșire "nu"; intrare "1011" => ieșire "nu"; intrare "10010" => ieșire "nu".
</code>



==== 5. Linkuri utile ====

[[https://ocw.cs.pub.ro/courses/cn1]]

[[https://markusfeng.com/projects/graph/ | Subiectul de automate]]

[[https://meltdownattack.com/| Spoiler pentru ce poți face cu noțiuni de la CN1 si alte noțiuni care vor fi prezentate la CN2 și SO]]