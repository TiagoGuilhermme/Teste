# Respostas do Laboratório 04

Aluno: tiago.silva  
Repositório: lab03_04_verilog_tiago_silva  
Branch de trabalho: lab04_verilog_sequential


## Q01

**Resposta:** A diferença é que o reset síncrono só atua na borda ativa do clock, sendo tratado como parte do caminho de dados e integrando-se diretamente à análise temporal. Já o reset assíncrono atua independente do sinal de clock, ou seja, quando acionado, não é necessário esperar a próxima borda do clock para o reset ocorrer.

- O reset assíncrono pode ser usado em situações onde o clock do sistema ainda não está disponível ou ainda não foi inicializado.

- Ambos são suportados pelo fluxo de DFT; o que é diferente é a controlabilidade deles, o que implica que, dependendo da lógica, o uso de um ou de outro pode ser mais vantajoso. Por exemplo, na fase de deslocamento, os resets assíncronos impõem um desafio maior, porque precisam ser mantidos obrigatoriamente inativos — se forem ativados durante essa etapa, todos os valores que estavam sendo deslocados serão apagados.


## Q02

**Resposta:** Fazendo uso do `<=` (não-bloqueante), o simulador lê todos os valores antigos antes de atualizar as saídas. Assim, cada flip-flop passa o dado correto para o vizinho na mesma borda de clock, garantindo o deslocamento perfeito de bit em bit. Já se usarmos `=` (bloqueante), a atribuição ocorre imediatamente, na sequência em que as linhas do código são executadas. Isso faz com que um flip-flop já leia o valor atualizado do flip-flop anterior na mesma borda de clock, em vez do valor que ele tinha antes da borda. O resultado é que múltiplos estágios do registrador acabam recebendo o mesmo valor de uma só vez, quebrando o comportamento de deslocamento (shift) que se pretendia descrever.


## Q03
**Resposta:** Fazendo o uso do modelo Moore, a saída unlock é gerada dependendo exclusivamente do estado atual da máquina de estados (FSM). Isso significa que ela só muda de valor quando ocorre uma transição de estado na borda ativa do clock. Agora, se mudarmos o comportamento para o modelo Mealy, a saída unlock passaria a ser gerada dependendo tanto do estado atual quanto das entradas atuais do sistema (neste caso, as entradas confirm e pw_ok).

**Vantagem:** resposta mais rápida (um ciclo de clock a menos de latência), o que em uma FSM combinada com hardware físico (como um relé de trava da porta) pode ser relevante se cada ciclo custar tempo perceptível.

**Risco:** como `pw_ok` vem direto do comparador (`comparator4b`), que é puramente combinacional a partir de `stored_pw`, qualquer instabilidade ou glitch em `stored_pw`/`pw_ok` antes de estabilizar se propagaria imediatamente para `unlock`, já que ela não está mais "filtrada" por um registrador de estado. Isso pode gerar pulsos espúrios de destravamento se as entradas não estiverem bem sincronizadas — problema que o modelo Moore evita, pois `unlock` só reflete o estado já registrado (estável) na borda anterior do clock.

## Q04
**Resposta:** Quando o circuito está no estado $S1$ (primeiro bit `1` encontrado) e recebe um segundo `1`, esse novo bit anula o anterior, mas inicia uma nova chance de completar a sequência "101". Se a máquina de estados voltasse para o estado inicial ($S0$), ela esqueceria esse segundo `1` e falharia em detectar o padrão a partir dele.

Mantendo-se em $S1$, o sistema preserva o registro de que o último bit visto foi um `1`. Assim, em um fluxo como `1` → `1` → `0` → `1`, a FSM consegue avançar corretamente para $S2$ ao receber o `0` e, finalmente, para $S3$ para ativar o sinal de detecção. Sem essa transição para si mesmo, sequências contínuas válidas seriam completamente ignoradas.

## Q05
**Resposta:**
**BLOCO 1: Registrador de Estado (Sequencial)**

**Responsabilidade:** Atualizar o estado atual da FSM a cada borda ativa do relógio (clock) ou restaurar o estado inicial quando o sinal de reinicialização (reset) for ativado.

**Características:** É um bloco puramente síncrono, descrito com `always @(posedge clk or negedge rst_n)`. Ele apenas transfere o valor calculado do próximo estado para o registrador do estado atual (`state <= next_state`).

---

**BLOCO 2: Lógica de Próximo Estado (Combinacional)**

**Responsabilidade:** Calcular qual será o próximo estado (`next_state`) da máquina com base no estado atual (`state`) e nas entradas do sistema.

**Características:** É um bloco puramente combinacional, descrito com `always @(*)`. Ele utiliza uma estrutura condicional (como o `case`) para mapear todas as transições de estado.

---

**BLOCO 3: Lógica de Saída (Combinacional)**

**Responsabilidade:** Definir o valor das saídas do sistema.

**Características:** Também é descrito em um bloco combinacional `always @(*)`. Em uma arquitetura Moore, as saídas dependem estritamente do estado atual. No sistema de controle de acesso do laboratório, as saídas externas seguem o padrão Moore, enquanto os pulsos de controle interno (como início do temporizador e incremento de tentativas) também avaliam as entradas, configurando um comportamento do tipo Mealy.

---

Separar a FSM em três blocos impede misturar os operadores `=` e `<=` no mesmo bloco `always`, o que causaria sérios problemas de comportamento e possível incompatibilidade entre a simulação e o circuito sintetizado. Facilita também a definição de valores padrão (*default*)



## Q06
**Resposta:**

* **Atribuição Contínua e Antecipação:** O sinal `max_attempts` é gerado via lógica combinacional contínua através da expressão `max_attempts = (attempt_count > MAX_TRIES - 1)`. Como `MAX_TRIES = 3`, a inequação simplifica-se para `attempt_count > 2`, o que indica o atingimento do limite na tentativa seguinte.
* **Ativação Imediata:** No exato instante em que ocorre a segunda falha, o contador síncrono `attempt_count` é atualizado para `2`. Sendo uma lógica combinacional pura, `max_attempts` reage imediatamente a essa mudança e muda para `1` ainda no mesmo ciclo de clock.
* **Avaliação da FSM e Transição:** Na terceira tentativa incorreta, a FSM se encontra avaliando as regras de transição no estado `S_CHECKING`. Como o sinal `max_attempts` já está em nível alto (`1`), a FSM avalia a condição combinacional de próximo estado `else if (max_attempts) next_state = S_BLOCKED;`.
* **Amostragem na Borda:** Devido ao agendamento de atribuições não-bloqueantes (`<=`) no registrador de estados, a FSM agenda a transição para `S_BLOCKED` para o próximo ciclo de clock. Na mesma borda em que a FSM efetivamente assume `S_BLOCKED`, o contador também recebe o seu incremento concomitante, alcançando o valor `3`.


## Q07
Resposta:


## Q08
**Resposta:**

A organização dos estímulos por meio de *tasks* no testbench apresenta vantagens práticas significativas em relação à escrita direta e contínua no bloco `initial`. A primeira vantagem é a **reutilização de código**, que elimina a duplicação de sequências repetitivas de comandos (como aplicar pulsos de clock específicos ou padronizar a inserção de dados e senhas no sistema). A segunda vantagem é a **legibilidade e facilidade de manutenção**, pois o uso de tarefas auxiliares limpa o corpo principal do bloco `initial`, permitindo que os cenários de testes sejam descritos de forma muito mais clara e estruturada em alto nível. Adicionalmente, essa estrutura facilita a **automação e padronização das checagens**, permitindo criar rotinas genéricas para verificar as saídas e contabilizar erros do sistema automaticamente.



