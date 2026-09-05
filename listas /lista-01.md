# Lista de Exercícios 01 — Semanas 1 a 3
## Parte A — Introdução (semana 1)
---
**Q1.** Um sistema embarcado é um sistema computacional projetado para executar uma função específica de um produto/conjunto maior. Entretanto, esses sistemas podem apresentar restições. A variar de qual função o sistema deve executar essas restrições precisam receber atenção redobrada. 
Exemplos de restição e produtos que essas restrições podem são dominantes: 
- Tempo Real: controle do Airbag automotivo, a exatidão do sistema depende não apenas do resultado lógico da computação, mas também do momento exato em que ele é entregue.
- Consumo de Energia: marca-passo, cada microampère economizado estende diretamente a vida útil do dispositivo por anos.
- Custo: controle remoto, a margem de lucro e a viabilidade comercial dependem de frações de centavos no custo de componentes de hardware devido à fabricação em massa.
- Memória: Cartão Inteligente, o circuito integrado precisa caber em uma lâmina de plástico milimétrica e barata, dispondo tipicamente de poucos kilobytes de RAM.
- Confiabilidade: computador de controle de voo, o sistema deve operar de forma contínua sem falhas, ou falhar de forma segura, mesmo sob condições extremas de ruído elétrico, radiação ou desgaste mecânico.

**Q2.**
(a) Controle de airbag: MCU (tempo real, disparo precisa ocorrer em uma janela determinística de poucos milissegundos após o impacto / confiabilidade: trata-se de uma aplicação crítica onde falhas não são toleradas)

(b) Roteador Wi-Fi doméstico: MPU / SoC (custo: Por ser um produto de consumo de grande volume, o SoC altamente integrado minimizan a lista de materiais)

(c) Protótipo de codec de vídeo proprietário: FPGA (tempo real, O hardware reconfigurável do FPGA permite criar pipelines de processamento massivamente paralelos que superam o paralelismo sequencial de MCUs e MPUs)

(d) Sensor de umidade a bateria por 2 anos: MCU (energia, MCUs apresentam consumo em modos de hibernação profunda acordando periodicamente apenas para realizar a leitura do sensor e transmitir os dados)

**Q3.**

Carga no modo ativo: $120\text{ mA} \times 3\text{ s} = 360\text{ mA}\cdot\text{s}$

Carga no modo dormindo: $0{,}040\text{ mA} \times 597\text{ s} = 23{,}88\text{ mA}\cdot\text{s}$

$$I_{\text{média}} = \frac{383{,}88\text{ mA}\cdot\text{s}}{600\text{ s}} \approx 0{,}6398\text{ mA} \quad (\approx 640\ \mu\text{A})$$

$$\text{Autonomia (horas)} = \frac{C}{I_{\text{média}}} = \frac{1200\text{ mAh}}{0{,}6398\text{ mA}} \approx 1875{,}59\text{ horas}$$

A transmissão domina o consumo da bateria.

( Que mudança de software dobraria a autonomia?)
Reduzir o tempo de rádio/transmissão ativo pela metade (de 3 para 1,5) por meio de empacotamento binário mais compacto de dados.
