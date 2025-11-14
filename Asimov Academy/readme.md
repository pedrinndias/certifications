### 🧠 Resumo: Lista 02 - Arquitetura de Computadores

---

#### Parte 1: Unidade Lógica e Aritmética (ULA)

* **1. Papel da ULA:** É o "cérebro" de cálculos da CPU. Realiza **Operações Aritméticas** (soma, subtração) e **Operações Lógicas** (AND, OR, NOT).
* **2. Vantagem do Complemento de 2:** **Eficiência de hardware**. Permite que a subtração ($A - B$) seja feita usando o *mesmo* circuito da adição ($A + (\text{C2 de } B)$), simplificando o design da ULA.

---

#### Parte 2: Lógica Sequencial e Memória

* **3. Combinacional vs. Sequencial:**
    * **Combinacional:** A saída depende *apenas* das entradas atuais (ex: ULA).
    * **Sequencial:** A saída depende das entradas atuais *e* do estado anterior (possui memória, ex: Registrador).
* **4. Papel do Clock:** É um sinal de **sincronização**. Ele divide o tempo em "passos discretos" (ticks) e dita *quando* os circuitos sequenciais devem atualizar seu estado, garantindo que os dados estejam estáveis.
* **5. Frequência e Limites do Clock:** A faixa comum é de 2-5 GHz. Os limites práticos são: 1) **Geração de Calor** (muito rápido = muito quente) e 2) **Atraso de Propagação** (o sinal precisa de tempo para viajar; se o clock for mais rápido, ele lê "lixo").
* **6. Registradores (1-bit e 16-bit):**
    * **1-bit:** Unidade de memória básica (Flip-Flop). Um pino `load` controla se ele mantém o estado (`load=0`) ou armazena um novo valor da entrada (`load=1`) no próximo tick do clock.
    * **16-bit:** Um conjunto de 16 registradores de 1 bit que compartilham o *mesmo* sinal `load`.
* **7. Endereçamento (256 registradores):** São necessários **8 bits** ($2^{8} = 256$).
* **8. Clock vs. Propagação:** O ciclo de clock deve ser *mais longo* que o atraso de propagação. Se for mais rápido, o circuito lerá um valor "lixo" (instável/incorreto), causando erros de cálculo.
* **9. Exemplo de "Manter Estado":** O **Program Counter (PC)**. Ele deve "lembrar" (manter) o endereço da próxima instrução para que o programa execute em sequência.
* **10. Hierarquia de Memória:** Uma pirâmide (do topo para a base: mais rápido/caro $\rightarrow$ mais lento/barato): **Registradores** $\rightarrow$ **Cache (L1-L3)** $\rightarrow$ **RAM** $\rightarrow$ **SSD/HD**.
    * **Função do Cache:** A RAM é muito lenta para a CPU. O cache armazena cópias de dados da RAM usados com frequência, servindo como um "buffer" de alta velocidade.
* **11. RAM (1K x 16-bit):**
    * **Total de Bits:** 16.384 bits ($1024 \times 16$).
    * **Diferença:** O **Endereço** é a *localização* (ex: 10). O **Conteúdo** é o *dado* armazenado naquela localização (ex: o valor 42).
* **12. Leitura (Read) vs. Escrita (Write) na RAM:**
    * **(a) Leitura:** É **combinacional**. Você fornece um `address` e a RAM *imediatamente* retorna o conteúdo em `out`.
    * **(b) Escrita:** É **sequencial**. Você fornece `address`, `in` (dado) e `load=1`. O dado só é gravado *no próximo tick do clock*.

---

#### Parte 3: Linguagem de Máquina e Arquitetura

* **13. Conceito de Programa Armazenado:** A ideia de Von Neumann de que as **instruções** (código) e os **dados** são armazenados juntos na mesma memória.
* **14. Instrução A vs. C (Hack):**
    * **Instrução A (`@valor`):** Carrega um valor no **Registrador A**. Usada para definir constantes ou endereços.
    * **Instrução C (`dest=comp;jump`):** Realiza **cálculos** (usando a ULA) e/ou **desvios** (pulos).
* **15. Análise de Código (1):** O programa calcula `RAM[5] = RAM[3] + RAM[4]`.
* **16. Programa para Zerar RAM[10]:**
    ```assembly
    @10
    M=0
    ```
* **17. Papel do Program Counter (PC):** Armazena o **endereço da próxima instrução**. No ciclo de "busca", o PC envia seu endereço para a Memória de Instruções, que retorna a instrução a ser executada.
* **18. Programa (Subtração Condicional):** O código calcula `D = RAM[0] - RAM[1]`. Em seguida, usa um pulo condicional (`D;JLE`) para pular o salvamento se o resultado for $\leq 0$. Caso contrário (se for $> 0$), ele armazena o resultado em `RAM[10]`.
* **19. Registradores A, D, M (Hack):**
    * **D:** Registrador de **Dados**. Único que armazena resultados da ULA e é usado para testes de pulo.
    * **A:** Registrador de **Endereço**. Define qual endereço da RAM o `M` irá acessar, ou simplesmente carrega uma constante.
    * **M:** Um registrador "virtual" que representa o **conteúdo da memória em RAM[A]**.
* **20. Análise de Código (2):** O programa armazena o valor **7** na posição **RAM[100]**.
* **21. Análise de Código (3):** O programa armazena o resultado de `RAM[0] - RAM[1]` na posição `RAM[10]`.
* **22. "Flags" (zero, negativo):** São bits de status gerados pela ULA que informam sobre o resultado (ex: se foi zero, se foi negativo). Eles são cruciais para **tomar decisões** e permitir **desvios condicionais** (jumps).
* **23. Alto Nível vs. Máquina:**
    * **Alto Nível (C, Python):** Abstrato, legível por humanos (ex: `if`, `while`).
    * **Máquina (Binário):** O que a CPU realmente entende (ex: `1110111...`).
    * **Compilador:** O tradutor que converte a lógica de alto nível para o código de máquina equivalente.

---

#### Parte 4: Ciclo de Execução

* **24. Caminho de Dados (M=D+1):**
    1.  **Busca (Fetch):** O **PC** envia seu endereço para a ROM (Memória de Instruções), que retorna o binário de `M=D+1`. O PC é incrementado.
    2.  **Decodificação/Execução:** A CPU decodifica a instrução. A **ULA** é instruída a calcular `D+1`. Ela pega o valor de **D**, soma 1, e coloca o resultado em sua saída.
    3.  **Armazenamento (Write Back):** O resultado da ULA (`D+1`) é enviado para a entrada `in` da RAM. O endereço da RAM é pego do **Registrador A**. O sinal `load` da RAM é ativado (1), e o valor é gravado no próximo tick do clock.