Aqui estão as tabelas em markdown:

---

## Q11

**Resposta:**

**a)** → slack (VIOLATED) = -0.12

| Campo | Slack_Violated_01 | Slack_Violated_02 |
|---|---|---|
| Startpoint | b[1] (input port clocked by clk) | b[1] (input port clocked by clk) |
| Endpoint | acc_reg[13] (rising edge-triggered flip-flop clocked by clk) | acc_reg[13] (rising edge-triggered flip-flop clocked by clk) |

**b)** → Levels of Logic: 18.00

**c)** → Como observado em: #intadd_8/U4/S (FADDX1_RVT) = 0.12, temos que a celula FADDX1_RVT que é um Full Adder, intrissicamente de logica combinacional, contribui para um maior atraso.

| EP | Slack |
|---|---|
| _sel15 | -0.115 ns |
| _sel16 | -0.115 ns |
| _sel17 | -0.115 ns |
| _sel18 | -0.115 ns |
| _sel19 | -0.115 ns |

---

## Q12

**Resposta:**

| Período | Freq. | WNS (ns) | Violações | Área (μm²) | Status |
|---|---|---|---|---|---|
| 1,5 ns | 667 MHz | -0.11 | 21.00 | 1681.126993 | VIOLATED |
| 2,5 ns | 400 MHz | 0.07 | 0.00 | 907.831541 | PASS |
| 4,0 ns | 250 MHz | 1.57 | 0.00 | 907.831541 | PASS |

Assim, o menor periodo de clock em que as restrições de timing são obtidas é quando T = 2.5ns

---

## Q13

**Resposta:**

**a)** → A ULA apresenta maior área que o contador. Isso ocorre porque a ULA possui uma estrutura lógica mais complexa, incluindo circuitos aritméticos e lógicos combinacionais para diversas operações, enquanto o contador limita-se essencialmente a um acumulador e lógica de incremento simples.

**b)** → O leakage da MAC é significativamente maior devido à sua vasta área de silício comparada à do contador. Como a corrente de fuga estática é diretamente proporcional ao número de transistores e à área total do design, a complexidade da MAC — contendo multiplicadores e somadores de alta densidade — resulta em um consumo de energia estática muito superior ao da lógica simples do contador.

| DESIGN | AREA (um²) | WNS (ns) | POTÊNCIA | STATUS |
|---|---|---|---|---|
| contador | 132.409026 | 1.78 | 13.8019 uW | MET |
| alu | 286.420287 | 0.95 | 14.0396 uW | MET |
| mac_unit | 1381.272647 | -0.12 | 308.2294 uW | VIOLATED |

---

## Q14

**Resposta:**

| set_max_area | Área (μm²) | WNS (ns) | N° Células | Status |
|---|---|---|---|---|
| Sem restrição | 132.92 um² | +1.7817 ns | 39 | PASS |
| 90 μm² | 131.340546 | +1.40 ns | 37 | PASS |
| 70 μm² | 131.931584 | +1.43 ns | 37 | PASS |

→ Ao reduzir a área máxima, o compilador é forçado a usar células lógicas menores ou menos eficientes, que frequentemente possuem atrasos internos (*delay*) superiores às células maiores. Além disso, a restrição de área limita a capacidade do *Design Compiler* de realizar otimizações como a duplicação de lógica ou o uso de *buffers* de alta performance para reduzir o atraso de sinal em caminhos críticos. Como resultado, o sinal demora mais para percorrer a lógica combinacional, fazendo com que chegue após o tempo limite do clock, gerando violações de *setup*.
