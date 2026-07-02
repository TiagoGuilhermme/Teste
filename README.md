#Respostas do Laboratorio 03

Aluno : Tiago Guilherme da Silva
Repositorio : lab03_04_verilog_tiago_silva
Branch de trabalho : lab03_verilog_combinational

## Q01

**Resposta:** A principal diferença está no objetivo. Em linguagens de programação como C, C++ e C#, a ideia é descrever um algoritmo, ou seja, uma sequência bem definida e finita de passos que será interpretada pela arquitetura visando atingir um resultado requerido pelo programador. Já em linguagens de descrição de hardware, tais como Verilog, SystemVerilog ou VHDL, o objetivo é descrever o comportamento de blocos de circuitos digitais de forma que tudo ocorra de maneira concorrente. Essa foi uma maneira de abstrair a complexidade dos grandes sistemas, facilitando a implementação.

Por isso, profissionais dessa área devem entender esses conceitos, pois isso afeta a forma como o código deve ser pensado. Por exemplo, como em linguagens de programação está sendo descrito um algoritmo, a sequência das instruções importa, então o programador deve ficar atento a isso. Já em HDL, os profissionais não precisam se preocupar tanto com isso devido à concorrência, exceto em alguns casos.

## Q02

**Resposta:** A necessidade ocorre pela escolha de implementação. O mux poderia ter sido implementado sem usar o bloco `always`, apenas com as expressões lógicas; no entanto, sua implementação se deu com o bloco `always`. Por conta disso, a sintaxe obriga o uso do `reg` no lado esquerdo para atribuições, porém essa lógica não implementa elementos de memória quando bem descrita, mas sim gera circuitos combinacionais. Ou seja, o `always @(*)` em Verilog e o `always_comb` em SystemVerilog geram circuitos combinacionais quando descritos corretamente.

## Q03

**Resposta:** Quando isso ocorre, um circuito cujo objetivo inicial era ser puramente combinacional pode agora apresentar elementos sequenciais, com a aparição de latches inseridos pela ferramenta da Synopsys devido à má descrição do módulo. Por isso, uma boa descrição para modelos é aquela que deixa claro o que deve ser feito, não abrindo espaço para a ferramenta supor ou presumir ligações e inclusões de elementos indesejados. Por isso, como boa regra, sempre deve-se colocar todos os casos de if/else e, caso feito o uso de `case`, sempre usar o `default` para dar robustez à descrição.

## Q04

**Resposta:** A diferença é compreendida no fato de que o operador `!==`, caso os elementos que estão sendo comparados estejam em algum estado indefinido, como X ou Z, permite que a ferramenta consiga identificar o erro e parar a simulação. Já quando feito o uso do `!=`, se os sinais estiverem indefinidos, ainda assim a simulação pode passar, gerando erros de lógica.

**Exemplo:** Se `a = 1'bx` e `b = 1'b1`, então `a !== b` é verdadeiro (detecta a indefinição e falha o teste), enquanto `a != b` pode avaliar como `x` e não disparar erro, mascarando o problema.

## Q05

**Resposta:** No somador *ripple carry*, cada bit só pode calcular sua soma após receber o carry do bit anterior — o carry se propaga sequencialmente, do LSB ao MSB. Isso cria um caminho crítico que atravessa todos os full adders em cadeia, fazendo com que o atraso total seja a soma dos atrasos de cada estágio:

$$T_{ripple} \propto n \cdot t_{carry}$$

onde *n* é o número de bits e *t_carry* o atraso de geração do carry em cada full adder.

Já no *carry-lookahead adder* (CLA), os sinais de *generate* (G) e *propagate* (P) são calculados em paralelo, e o carry de cada estágio é obtido diretamente por equações lógicas, sem esperar a propagação sequencial. Isso reduz drasticamente o atraso, ao custo de mais hardware.

**Com o aumento do número de bits:** no ripple carry, o atraso cresce **linearmente** (O(n)) — dobrar a largura praticamente dobra o atraso, tornando a arquitetura inadequada para somadores largos. No carry-lookahead, o atraso cresce de forma muito mais lenta, idealmente **logarítmica** (O(log n)) em estruturas hierárquicas, ao custo de maior complexidade e área.

## Q06

**Resposta:** Observando as formas de onda da figura `verdi_analysis_alu4b`, a flag vai a 1 quando temos A == B, ou seja, quando A - B == 0. Com isso, é possível executar uma operação importante de comparar dois números diferentes.

**Casos:**

| A | B |
|---|---|
| 1 | 1 |
| 0 | 0 |

## Q07

**Resposta:** A instrução `$finish` encerra a simulação, finalizando o processo do simulador quando executada.

Em um testbench combinacional finito (sem processo contínuo de clock), a simulação termina naturalmente quando não há mais eventos agendados, então a omissão de `$finish` teria pouco ou nenhum impacto — a simulação chegaria ao fim de qualquer forma.

Já em um testbench com geração contínua de clock (ex.: `always #5 clk = ~clk;`), sempre haverá um próximo evento agendado, então sem `$finish` a simulação nunca terminaria sozinha, rodando indefinidamente (ou até o limite de tempo/memória do simulador).

## Q08

**Resposta:**

### Diferença entre `&` e `&&` em Verilog

- **`&`** é o operador **bitwise AND (E lógico bit a bit)**. Ele opera bit a bit entre dois operandos de mesma largura, produzindo um resultado com a mesma largura dos operandos. Exemplo: `4'b1010 & 4'b1100 = 4'b1000`.

- **`&&`** é o operador **logical AND (E lógico booleano)**. Ele trata cada operando como um valor booleano único: qualquer operando diferente de zero é considerado `1` (verdadeiro), e zero é considerado `0` (falso). O resultado é sempre **1 bit** (`0` ou `1`), independentemente da largura dos operandos.

### Exemplo de erro silencioso em um barramento de 4 bits

```verilog
wire [3:0] a = 4'b1010;
wire [3:0] b = 4'b1100;
wire [3:0] c;

assign c = a && b;  // ERRADO: deveria ser a & b
```

**O que acontece:**

`a && b` avalia `a` como verdadeiro (pois `a != 0`) e `b` como verdadeiro (pois `b != 0`), resultando no valor booleano `1'b1`. Esse resultado de 1 bit é então **zero-extendido** para preencher os 4 bits de `c`.

## Q09

**Resposta:**

**Situação em que a adição (op=00) produz resultado truncado e incorreto em complemento de dois:**

Como a ULA não possui uma flag de *overflow*, o único mecanismo disponível para perceber que a soma "estourou" a largura de bits é observar o *carry-out*. Isso foi implementado concatenando mais um bit ao resultado, de forma que a saída passa a ter 5 bits em vez de 4, permitindo representar diretamente o `carry-out` para os casos em que **A + B > 15** (considerando os operandos como valores sem sinal, de 0 a 15).

Se a saída `y` fosse definida com apenas 4 bits, conseguiríamos representar valores até 15; a partir disso, o bit mais significativo do resultado seria descartado e a informação seria perdida. Com a concatenação do bit de `carry-out`, essa informação é recuperada — mas é importante notar que esse mecanismo detecta corretamente o **overflow em aritmética sem sinal** (unsigned), e **não** o overflow em complemento de dois (aritmética com sinal). Nesse último caso, o `carry-out` sozinho pode indicar estouro quando na verdade o resultado está correto, ou deixar de indicar quando o resultado está incorreto — por isso a ULA precisaria de uma flag de *overflow* dedicada, calculada de outra forma.

**Como a flag de overflow poderia ser calculada a partir dos bits de entrada e do carry:**

Em complemento de dois, o overflow ocorre quando o sinal do resultado é logicamente impossível dado o sinal dos operandos — ou seja, quando somamos dois números de **mesmo sinal** e obtemos um resultado de **sinal diferente**. Isso pode ser detectado por meio do XOR entre o *carry* que entra no bit mais significativo (MSB) e o *carry* que sai do MSB:

$$overflow = C_{in}(MSB) \oplus C_{out}(MSB)$$

Ou seja: se o carry que entra no último estágio de soma (bit de sinal) for diferente do carry que sai desse mesmo estágio, houve overflow. Alternativamente, o overflow também pode ser expresso diretamente a partir dos bits de sinal dos operandos (A₃, B₃) e do resultado (Y₃):

$$overflow = (A_3 \cdot B_3 \cdot \overline{Y_3}) + (\overline{A_3} \cdot \overline{B_3} \cdot Y_3)$$

ou seja, overflow ocorre quando A e B têm o mesmo sinal (ambos positivos ou ambos negativos) e o resultado apresenta sinal contrário ao dos operandos.











