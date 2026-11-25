# Lista de Exercícios 01 — Semanas 1 a 3
## Parte A — Introdução (semana 1)
---
### **Q1.** Um sistema embarcado é um sistema computacional projetado para executar uma função específica de um produto/conjunto maior. Entretanto, esses sistemas podem apresentar restições. A variar de qual função o sistema deve executar essas restrições precisam receber atenção redobrada. 
Exemplos de restição e produtos que essas restrições podem são dominantes: 
- Tempo Real: controle do Airbag automotivo, a exatidão do sistema depende não apenas do resultado lógico da computação, mas também do momento exato em que ele é entregue.
- Consumo de Energia: marca-passo, cada microampère economizado estende diretamente a vida útil do dispositivo por anos.
- Custo: controle remoto, a margem de lucro e a viabilidade comercial dependem de frações de centavos no custo de componentes de hardware devido à fabricação em massa.
- Memória: Cartão Inteligente, o circuito integrado precisa caber em uma lâmina de plástico milimétrica e barata, dispondo tipicamente de poucos kilobytes de RAM.
- Confiabilidade: computador de controle de voo, o sistema deve operar de forma contínua sem falhas, ou falhar de forma segura, mesmo sob condições extremas de ruído elétrico, radiação ou desgaste mecânico.

### **Q2.**
(a) Controle de airbag: MCU (tempo real, disparo precisa ocorrer em uma janela determinística de poucos milissegundos após o impacto / confiabilidade: trata-se de uma aplicação crítica onde falhas não são toleradas)

(b) Roteador Wi-Fi doméstico: MPU / SoC (custo: Por ser um produto de consumo de grande volume, o SoC altamente integrado minimizan a lista de materiais)

(c) Protótipo de codec de vídeo proprietário: FPGA (tempo real, O hardware reconfigurável do FPGA permite criar pipelines de processamento massivamente paralelos que superam o paralelismo sequencial de MCUs e MPUs)

(d) Sensor de umidade a bateria por 2 anos: MCU (energia, MCUs apresentam consumo em modos de hibernação profunda acordando periodicamente apenas para realizar a leitura do sensor e transmitir os dados)

### **Q3.**

Carga no modo ativo: $120\text{ mA} \times 3\text{ s} = 360\text{ mA}\cdot\text{s}$

Carga no modo dormindo: $0{,}040\text{ mA} \times 597\text{ s} = 23{,}88\text{ mA}\cdot\text{s}$

$$I_{\text{média}} = \frac{383{,}88\text{ mA}\cdot\text{s}}{600\text{ s}} \approx 0{,}6398\text{ mA} \quad (\approx 640\ \mu\text{A})$$

$$\text{Autonomia (horas)} = \frac{C}{I_{\text{média}}} = \frac{1200\text{ mAh}}{0{,}6398\text{ mA}} \approx 1875{,}59\text{ horas}$$

A transmissão domina o consumo da bateria.

( Que mudança de software dobraria a autonomia?)
Reduzir o tempo de rádio/transmissão ativo pela metade (de 3 para 1,5) por meio de empacotamento binário mais compacto de dados.

### **Q4.**

(a1) Em tempo real rígido a resposta do sistema deve ocorrer dentro de um prazo limite, sem tolerância. Uma resposta fora desse tempo é considerada uma falha.
**Exemplo no automóvel:** Módulo de disparo do Airbag ou Sistema de Freios ABS.

(a2) Em tempo real brando a perda de um prazo pode significar uma perda na qualidade de serviço, mas o sistema continua operando sem provocar falhas.
**Exemplo no automóvel:** Central multimídia / Navegação GPS.

(b) O projeto da malha de luminosidade se encaixa na categoria de tempo real brando, pois, a perda do um prazo no laço de controle de iluminação não gera consequências de danos físicos ou risco.

### **Q5.**

O papel do simulador é implementar e testar a lógica do firmware antes da montagem física da bancada, facilitar a identificação de bugs algorítmicos e erros de código em um ambiente limpo e controlado. Em relação ao hardware, as principais diferenças são que ele não reproduz ruídos elétricos na linha de alimentação, flutuações de tensão, perdas parasitas em trilhas, tempos de subida/descida ou a inércia e atraso de propagação real.

### **Q6.**

### **Q7.**

Instruções necessárias por segundo: $12.000 \times 1.000 = 12.000.000\text{ instr/s}$ ($12\text{ MIPS}$).

ESP32 (240 MHz - 240 MIPS):
$$\text{Ocupação} = \frac{12\text{ MHz}}{240\text{ MHz}} = \mathbf{5\%}$$

AVR (Arduino - 16 MHz - 16 MIPS):
$$\text{Ocupação} = \frac{12\text{ MHz}}{16\text{ MHz}} = \mathbf{75\%}$$

Conclusão: O Arduino fica sobrecarregado para PDS (Processamento Digital de Sinais), restando pouca CPU para outras tarefas. O ESP32 executa a tarefa com folga(5%).

### **Q8.**

A diferenciação entre as memórias do ESP32:

- Flash: É uma memória não volátil (preserva os dados mesmo sem energia) e mais lenta. É utilizada para armazenar o firmware/código do programa e as constantes que não mudam durante a execução.

- SRAM: É uma memória volátil (perde os dados ao desligar) e muito rápida. É utilizada para armazenar as variáveis globais e locais, a pilha (stack) e a memória dinâmica (heap).

- Memória RTC: É uma memória volátil, mas mantida ativa por uma bateria de backup (ou circuito de baixíssimo consumo). Possui velocidade média e é usada para guardar dados críticos que precisam ser mantidos quando o microcontrolador entra em modo de sono profundo (deep sleep).

**Por que variáveis globais "vivem" na SRAM, mas seu valor inicial vem da Flash?**

O valor inicial precisa ser salvo na Flash para persistir ao desligar o dispositivo. Na inicialização (boot), o código de startup copia esses valores para a SRAM, onde as variáveis vivem e podem ser alteradas rapidamente.

### **Q9.**

**Linha em C para acender o GPIO2:**
*(volatile uint32_t *)(0x3FF44008) = (1 << 2);

Os registradores W1TS e W1TC alteram unicamente o bit escrito em 1 no hardware interno. Isso elimina a necessidade de ler o registrador inteiro, modificar o bit e reescrevê-lo, garantindo operação atômica e evitando condições de corrida.

### **Q10.**
- Taxa de dados: $3\text{ eixos} \times 16\text{ bits} = 48\text{ bits} = 6\text{ bytes per amostra}$.
- Taxa por segundo (1 kHz): $6\text{ bytes} \times 1000 = 6.000\text{ bytes/s} = 6\text{ KB/s}$.
- Para 5 segundos: $6\text{ KB/s} \times 5\text{ s} = \mathbf{30\text{ KB}}$.
-- Cabe na SRAM (~520 KB)? Sim, ocupa uma fração pequena da SRAM.
- Para 60 segundos: $6\text{ KB/s} \times 60\text{ s} = \mathbf{360\text{ KB}}$.
-- Cabe na SRAM? Inviável/Não recomendado, pois compromete quase toda a memória útil do sistema.
- Solução: Utilizar  Double Buffering (streaming): preenchendo um buffer de memória enquanto outro transfere/salva os dados (ex: via Wi-Fi) de forma contínua.

### **Q11.**

- O que impede: Impede que o compilador otimize o acesso a uma variável mantendo seu valor salvo em registrador em vez de relê-la na memória RAM a cada instrução.

- Bug silencioso sem volatile: Em uma ISR que altera uma flag (ex: flag = 1), o laço principal while(!flag){} pode entrar em loop infinito, pois o compilador lerá a flag apenas uma vez e assumirá que ela nunca muda dentro do laço.

### **Q12.**

1. Zerar bits 3–2:
REG = REG | (0b00000100) => REG & =~ (3 << 2);

3. Escrever o valor 01 nos bits 3–2:
$$\text{REG} = \text{REG} \ \vert{} \ (0\text{b}00000100) \quad \Rightarrow \quad \text{REG} \ \vert{}=\ (1 \ll 2);$$

4. Inverter o bit 7:
$$\text{REG} = \text{REG} \ \hat{} \ (0\text{b}10000000) \quad \Rightarrow \quad \text{REG} \ \hat{}=\ (1 \ll 7);$$

### **Q13.**

GPIO de 3,3 V ($I = 4\text{ mA} = 0{,}004\text{ A}$, $V_F = 3{,}0\text{ V}$):

$$R = \frac{V_{CC} - V_F}{I} = \frac{3{,}3\text{ V} - 3{,}0\text{ V}}{0{,}004\text{ A}} = \frac{0{,}3\text{ V}}{0{,}004\text{ A}} = \mathbf{75\text{ }\Omega}$$

-Comentário prático: Margem de tensão muito pequena ($0{,}3\text{ V}$). Se $V_F$ variar levemente para $3{,}2\text{ V}$, a corrente cai para $1{,}33\text{ mA}$

GPIO de 5 V ($I = 4\text{ mA}$, $V_F = 3{,}0\text{ V}$):

$$R = \frac{5{,}0\text{ V} - 3{,}0\text{ V}}{0{,}004\text{ A}} = \frac{2{,}0\text{ V}}{0{,}004\text{ A}} = \mathbf{500\text{ }\Omega}$$

**Por que nunca ligar 5 V direto no GPIO de 3,3 V:** O nível de 5 V excede a tensão máxima suportada pelo pino, o que pode queimar os diodos de proteção e danificar permanentemente o microcontrolador.

### **Q14.**

- Pull-Up: Resistor ligado entre o pino e $V_{CC}$ (garante nível LÓGICO ALTO por padrão).
- Pull-Down: Resistor ligado entre o pino e o GND (garante nível LÓGICO BAIXO por padrão).

### **Q15.**

**Proposta e justificativa:** Utilizar uma janela de confirmação por temporização (ex: ler o estado do botão, aguardar 10 ms e ler novamente). Como bouncing dura no máximo 6 ms, um atraso de 10 ms ultrapassa a transição instável e garante a leitura do estado estável do botão.

**Compromisso (Latência x Robustez):**

- Janelas muito curtas (< 6 ms): baixa latência, porém risco de falsos gatilhos.
- Janelas muito longas (> 50 ms): alta robustez, porém introduz um atraso perceptível para o usuário ao pressionar o botão.
