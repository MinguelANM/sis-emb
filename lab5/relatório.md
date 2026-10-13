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


---
### 4. Medições de pilha da Parte D e o tamanho de pilha que vocês recomendariam, com justificativa pelo Exemplo 5.3.

   ---
### 5. Evidência da Parte E (log mostrando A/B/C saudáveis com HOG no core 1) + 2 linhas: porque fixar cargas pesadas no core 1 é a boa prática no ESP32?
