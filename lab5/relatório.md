# Relatório de Sistemas Embarcados - Semana 5

##  Integrantes
* Alexandre de Oliveira Júnior
* Luisa Martins Schineider
* Miguel Arcanjo N. Meriguete

---

## Entregas

### 1. Explicação da Parte A.7 (por que prioridades diferentes convivem em CPU ociosa).
Como as tarefas A, B e C passam a maior parte do tempo em espera (vTaskDelayUntil), a CPU fica livre cerca de 99,9% do tempo. Como poucas tarefas precisam ser executadas ao mesmo tempo, há pouca disputa pela CPU. Assim, tarefas com prioridades diferentes funcionam bem juntas, pois a prioridade é usada para decidir qual tarefa será executada quando mais de uma estiver pronta.
---
### 2. Saída da Parte B.9 (starvation + task_wdt) e resposta da B.10 (time slicing).

Resposta da Parte B.9 (starvation + task_wdt)

Resposta da Parte B.10 (Time Slicing):
A e B rodam normalmente por terem prioridades mais altas (5 e 3) e liberarem a CPU com delays. C e HOG compartilham a prioridade 1; o FreeRTOS realiza alternância por tempo (time slicing) a cada tick (10 ms) entre elas. Por isso, C executa metade do tempo esperado e apresenta o dobro do período

---
### 3. Tabela da Parte C preenchida + a conta de ativações perdidas (C.12).

| Configuração | Período médio (ms) | Período máx (ms) |
| -- | -- | -- |
| vTaskDelay + corpo 50 ms | 250,0 ms | 250,2 ms |
| vTaskDelayUntil + corpo 50 ms | 200,0 ms | 200,1 ms |

---
### 4. Medições de pilha da Parte D e o tamanho de pilha que vocês recomendariam, com justificativa pelo Exemplo 5.3.

A folga caiu para 9 palavras ($9 \times 4 = 36$ bytes de margem), muito próximo de causar stack overflow.

Pela regra Tamanho da Pilha = Uso Máximo + 50%, o consumo total máximo com o buffer é 512 + 1500 = 2012 bytes. Aplicando a margem de 50% (2012 * 1,5 = 3018 bytes), recomendariamos dimensionar a pilha em 3072 bytes para garantir operação segura sem estouro. 

   ---
### 5. Evidência da Parte E (log mostrando A/B/C saudáveis com HOG no core 1) + 2 linhas: porque fixar cargas pesadas no core 1 é a boa prática no ESP32?

O Core 0 do ESP32 já gerencia por padrão a pilha de protocolos de rede (Wi-Fi, Bluetooth) e serviços do sistema. Isolar o processamento pesado no Core 1 evita travamentos do Wi-Fi e garante determinismo para as tarefas da aplicação.
