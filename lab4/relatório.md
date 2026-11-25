# Relatório de Sistemas Embarcados - Semana 4

##  Integrantes
* Alexandre de Oliveira Júnior
* Luisa Martins Schineider
* Miguel Arcanjo N. Meriguete

---

## Entregas

### 1. Tabela com as 10 latências da Parte A + média e máximo, com a explicação capturar × processar (≤ 5 linhas).
| Amostra | Latência de Captura (ms) | Latência de Processamento (ms) |
| :---: | :---: | :---: |
| **1** | 2,9 | 103,1 |
| **2** | 3,0 | 99,4 |
| **3** | 2,8 | 104,6 |
| **4** | 3,1 | 100,8 |
| **5** | 3,3 | 105,2 |
| **6** | 2,7 | 98,9 |
| **7** | 3,1 | 102,8 |
| **8** | 2,5 | 101,2 |
| **9** | 3,3 | 102,5 |
| **10** | 2,8 | 101,5 |
| **Média** | **2,95 ms** | **102,00 ms** |
| **Máximo** | **3,30 ms** | **105,20 ms** |

Capturar: é a função da ISR, que detecta o evento e responde em microssegundos (µs), com baixa latência garantida pela resposta do hardware.  
Processar: é a função da tarefa, que identifica o flag quando o escalonador permite sua execução. Sua latência inclui o vTaskDelay do laço, podendo ser maior que a da ISR.

---
### 2. Resposta do item 8: cenário numérico em que o polling do Lab 3 perderia eventos.

O polling não conseguiria contar corretamente os pulsos do encoder, pois seu intervalo de verificação (10 ms) é dez vezes maior que o período dos pulsos (1 ms). Dessa forma, perderia aproximadamente 9 dos 10 pulsos (90%).

---
### 3. Prints das duas falhas da Parte C (erro do printf-na-ISR e mensagem do task_wdt) + a explicação da cadeia do WDT.

---
### 4. Código da Parte D (a ISR modificada e a `gerador_task`) + três leituras de duração comparadas aos valores programados (150 ms / 1200 ms).

---
### 5. Parágrafo final: por que `printf` dentro da ISR é proibido e **como** o firmware contorna (flag `volatile` lida pela tarefa)?