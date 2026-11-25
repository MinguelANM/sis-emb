# Lista de Exercícios 02 - semana 4 a 6

## Grupo: Alexandre de Oliveira Junior, Miguel Arcanjo N. Meriguete, Luisa Martins Schineider
## Parte A — Interrupções e timers-semana 4

## Q1. Compare polling e interrupção quanto a: latência de pior caso, uso de CPU e complexidade. Em que situação o polling é a escolha certa?

Latência de pior caso: 
No polling, o limite vem da periodicidade da varredura.
Na interrupção, vem da dinâmica do hardware e da concorrência de eventos.

Uso de CPU: 
o polling a CPU gasta ciclos continuamente lendo registradores sem novidades leituras vazias.
Na interrupçãoa CPU executa outras tarefas ou permanece ociosa até o evento ocorrer.

Complexidade: 
No polling, você controla exatamente quando o periférico é lido e quando o estado muda. 
Na interrupção, a ISR pode interromper o fluxo principal entre duas instruções de máquina, exigindo cuidado redobrado ao manipular variáveis multibyte ou registradores compartilhados.  

A escolha certa: O polling é a escolha certa quando vale mais a pena ter um código simples, previsível e direto, ou quando chegam muitos dados sem parar do que deixar o processador "descansando" entre um evento e outro.

## Q2. *(estilo Exemplo 4.1)* Um encoder gera pulsos de 400 µs a até 1 kHz. Mostre por que a varredura de 10 ms do Lab 3 (`vTaskDelay(pdMS_TO_TICKS(10))`) perde pulsos e dimensione o período de polling que não perderia. Conclua se ainda é razoável ou se a ISR se impõe.

> 💡 *Critério da teoria-04: período de varredura ≤ período do evento ÷ 10. Faça as contas dos
> dois lados antes de concluir.*
> 
Por que perde pulsos: 
a tarefa de leitura executa aproximadamente a cada 10ms ou 10000µs,Cada pulso permanece em nível alto por apenas  400µs retorna a zero antes da próxima verificação. Como o intervalo entre leituras é 25 vezes maior que a duração do pulso,Um pulso pode subir e descer inteiramente durante o intervalo em que a tarefa está bloqueada no vTaskDelay, passando completamente despercebido.


Dimensionamento correto: 
Para não perder nenhum pulso por polling, é preciso detectar tanto o nível alto quanto o retorno ao nível baixo de cada ciclo do encoder
$T_{varredura} \le \frac{1\text{ ms}}{10} \implies T_{varredura} \le \mathbf{100\ \mu\text{s}}$.

Conclusão: O polling é simples e bom para fluxos contínuos ou leituras periódicas, mas 10 ms perde pulsos de 400 µs a 1 kHz. Para esse encoder, a ISR se impõe por unir baixa carga de CPU e resposta confiável.

## Q3 Enumere as três regras de ouro de uma ISR vistas em aula (curta; sem chamadas bloqueantes; APIs `FromISR` + `IRAM_ATTR` quando aplicável) e explique o que dá errado ao violar cada uma.
> 💡 *Os experimentos do Lab 4, Parte C, são as provas empíricas: cite o que vocês viram no
> monitor (o crash do printf e o task_wdt).*

1. Ser o mais curta possível: faça apenas o mínimo essencial na ISR; violar atrasa outras interrupções e faz perder eventos.

 2. Não usar funções bloqueantes (printf, vTaskDelay): nunca espere ou bloqueie dentro da ISR; violar pode travar o sistema ou causar panic/assert.

3. Usar APIs FromISR e o atributo IRAM_ATTR: se APIs FromISR para o RTOS e IRAM_ATTR na IRAM/ESP32; violar pode corromper o escalonador ou causar crash por falha de cache/Flash.

## Q4. *(estilo Exemplo 4.2)* Com clock base de 80 MHz, projete prescaler e valor de comparação para interrupções a cada 250 ms. Dê duas combinações válidas e diga qual prefere e por quê.

Fórmula: $\text{Valor} = \left(\frac{80.000.000}{\text{Prescaler}}\right) \times 0{,}25\text{ s}$

Combinação 1:
 Divisor do prescaler:$\rightarrow f_{\text{timer}} = 1\text{ MHz}$
Valor de comparação:$CMP = \frac{20\,000\,000}{80} - 1 = 249\,999$

Combinação 2: 
Divisor do prescaler:$\rightarrow f_{\text{timer}} = 80\text{ kHz}$
Valor de comparação: $CMP = \frac{20\,000\,000}{1\,000} - 1 = 19\,999$

Preferência: 
Prefiro a Combinação 1, porque cada tick vale exatamente $1\ \mu\text{s}$ — o que torna a leitura, a depuração e eventuais ajustes de tempo muito mais diretos e intuitivos.

## Q5. O que é um watchdog timer, que classe de falha ele mitiga e por que "alimentar" o WDT dentro de uma ISR de timer é um **anti-padrão**?
O que é um watchdog timer?
É um temporizador de supervisão que fica contando o tempo continuamente.

Que classe de falha ele mitiga?
Ele mitiga travamentos de fluxo de execução/falhas de responsividade em que o sistema deixa de progredir normalmente, mas não é capaz de se destravar sozinho.
ex:Loops infinitos,Deadlocks,CPU presa em rotinas travadas.

Se você alimenta o WDT dentro da ISR de um timer a ISR continua rodando no tempo certo e continua resetando o WDT.
Enquanto isso, a tarefa principal, a rede, o controle ou o escalonador podem estar completamente travados em um loop infinito ou deadlock.

## FreeRTOS-semana 5

## Q6 Desenhe o diagrama de estados de uma tarefa (pronta, executando, bloqueada, suspensa) e
dê um evento concreto do nosso Lab 5 que causa cada transição.
> 💡 *Ex.: A→bloqueada: `vTaskDelayUntil`; bloqueada→pronta: fim do delay; pronta→executando:
> escalonador; executando→pronta: preempção pelo HOG…*

<details><summary>🖼️ diagrama de estado do lab 5 </summary>

![alt text](image.png)

</details>

## Q7 *(estilo Exemplo 5.1)* Tarefa de 100 ms usa `vTaskDelay(100 ms)` e seu corpo demora
7 ms. Calcule o período efetivo e a deriva acumulada em 1 min; corrija com `vTaskDelayUntil` e
explique a diferença de semântica.

![Comparação entre vTaskDelay (período relativo, com deriva) e vTaskDelayUntil (período absoluto)](https://raw.githubusercontent.com/fabiobento/sis-emb-2026-2/main/assets/figuras/vtaskdelay_vs_until.png)

Período efetivo: Período efetivo: $100\text{ ms} + 7\text{ ms} = 107\text{ ms}$

Isso corresponde a uma frequência real de $\approx 9{,}35\text{ Hz}$ em vez de $10\text{ Hz}$.

Em $1\text{ min}$ de tempo real, deveriam ocorrer $\frac{60{.}000\text{ ms}}{100\text{ ms}} = 600$ ativações.

600 ciclos efetivos levam $600 \times 107\text{ ms} = 64{.}200\text{ ms}$ ($64{,}2\text{ s}$), gerando um atraso acumulado de $+4{.}200\text{ ms}$ ($+4{,}2\text{ s}$) em relação aos $60\text{ s}$ esperados.


Correção: Usar vTaskDelayUntil()

Diferença de semântica: usar vTaskDelayUntil() corrige a deriva porque ela calcula o próximo despertar a partir de quando a tarefa acordou no último ciclo (xLastWakeTime), e não de quando terminou de rodar.

## Q8 *(estilo Exemplo 5.2)* Tarefas: A (3 ms de CPU a cada 10 ms), B (10 ms a cada 100 ms), C (log, quando der). Atribua prioridades, calcule a utilização total e verifique se C ainda roda. O que acontece com C se B virar 60 ms a cada 100 ms?

Prioridades (método Rate Monotonic - menor período = maior prioridade):

A: Alta (Período 10 ms).
B: Média (Período 100 ms).
C: Baixa (Best effort / Quando der).

Utilização Total ($U$):

$$U_A = \frac{3\text{ ms}}{10\text{ ms}} = 0{,}30 \quad (30\%)$$

$$U_B = \frac{10\text{ ms}}{100\text{ ms}} = 0{,}10 \quad (10\%)$$

$$U_{\text{total}} = 30\% + 10\% = 40\% \quad (0{,}40)$$

A tarefa C roda? Sim. Restam 60% do tempo de CPU livre para a tarefa C (e para a Idle).

O que acontece se B virar $60\text{ ms}$ a cada $100\text{ ms}$?

$$U_{\text{total}}' = 30\% + 60\% = 90\% \quad (0{,}90)$$
A tarefa C ainda roda, mas agora ficará restrita a usar apenas os 10% restantes da CPU.

## Q9 Explique *starvation* e como o escalonador do FreeRTOS com *time slicing* trata tarefas
de **mesma** prioridade. Por que um `while(1){}` sem bloqueio numa tarefa de prioridade alta
congela as demais (e como o WDT de idle nos avisa)?
> 💡 *Vocês provocaram isso duas vezes: Lab 4 (PROVOCAR_WDT) e Lab 5 (HOG). Cite os logs.*

Starvation (Inanição):causado quando uma tarefa pronta para iniciar (Ready) fica impedida de obter tempo de CPU porque outras tarefas com prioridade maior ou sempre ativas usando o processadorde forma exclusiva.

Time Slicing (Round Robin):Quando não há tarefas de prioridade maior prontas o FreeRTOS alterna entre tarefas de mesma prioridade em round-robin a cada tick, movendo a tarefa atual para o fim da fila de prontos e restaurando a próxima.

Problema do while(1) sem bloqueio: Um laço while(1) sem pausa dentro de uma tarefa do FreeRTOS faz a tarefa ficar presa rodando sem parar. Como ela nunca tira uma "folga" (nunca entra em modo de espera), ela toma o controle da CPU só para si e impede que outras tarefas — incluindo a tarefa ociosa (Idle Task) — consigam rodar.

Aviso do WDT: O aviso do WDT é o "alarme de segurança" do microcontrolador quando uma tarefa prende a CPU por tempo demais e impede que o sistema respire ou rode a tarefa ociosa.

## Q10 No ESP32 (2 núcleos), o que muda ao fixar (`xTaskCreatePinnedToCore`) a tarefa de controle no core 1 e deixar Wi-Fi/sistema no core 0? Cite um benefício e um cuidado.
> 💡 *Benefício: jitter — teoria-05, seção 3. Cuidado: dados compartilhados entre núcleos viram
> concorrência REAL (simultânea), não só intercalada — a semana 6 inteira se aplica dobrada.*

O que muda: Colocar a tarefa de controle no Core 1 e deixar o Wi-Fi/rede no Core 0 evita que o trabalho pesado da rede atrapalhe ou atrase o seu controle.

Benefício: O tempo de resposta fica muito mais estável e previsível (menos jitter), fazendo o controle funcionar com mais precisão.

Cuidado: Como os dois núcleos rodam ao mesmo tempo de verdade, mexer nas mesmas variáveis ou filas pode gerar bagunça nos dados. Por isso, é preciso proteger o acesso compartilhado com Mutexes ou Spinlocks.

## IPC e sincronização-semana 6
## Q11 *(estilo Exemplo 6.1)* Duas tarefas fazem `g++` (lê–incrementa–escreve) 1000× cada.
Explique o entrelaçamento que perde incrementos e dê o intervalo possível do valor final.
Corrija com mutex e com alternativa sem bloqueio (variável por tarefa + agregação).
> 💡 *A alternativa sem bloqueio é a "solução do jardim de infância": cada criança com seu
> brinquedo, soma no final. Quando ela NÃO serve? (Quando o dado precisa de leitura
> instantânea consistente, não só de soma final.)*

![Interleaving de instruções de duas tarefas mostrando a perda de um incremento](https://raw.githubusercontent.com/fabiobento/sis-emb-2026-2/main/assets/figuras/corrida_interleaving.png)


Perda de incrementos: Fazer g++ exige ler, somar 1 e escrever. Se a Tarefa A lê 0 e é interrompida antes de escrever, a Tarefa B pode ler 0, somar 1 e escrever 1. Quando a Tarefa A volta, ela ainda acha que o valor era 0, soma 1 e escreve 1 por cima. Foram duas somas, mas o valor só subiu 1.

Intervalo possível: O valor final fica entre 2 (no pior cenário com muita disputa/sobrescrita) e 2000 (se nada se perder), já que cada tarefa tenta somar 1000 vezes.

Correção:
xSemaphoreTake(mutex_g, portMAX_DELAY);
g++;
xSemaphoreGive(mutex_g);


Sem bloqueio: (variável por tarefa): Cada tarefa soma na sua própria variável (g_A++ e g_B++), sem disputar g. No fim, junta tudo: g = g_A + g_B.

## Q12 Monte uma tabela comparando **fila, semáforo binário, semáforo de contagem, mutex e
event group**: o que sinalizam/carregam, caso de uso típico no curso, e se podem ser usados de ISR.
> 💡 *A tabela da teoria-06, seção 2, é o ponto de partida — complete a coluna "caso de uso no
> curso" com os labs 6, 10 e 14.*

| Mecanismo | O que sinaliza / carrega | Caso de uso típico (Labs 6, 10 e 14) | Uso direto de ISR? |
| --- | --- | --- | --- |
| Fila (*Queue*) | Dados completos copiados por valor (*structs*, leituras, comandos, ponteiros) ou eventos simples | Envio de dados/comandos entre tarefas e/ou ISRs; fluxo assíncrono no **Lab 14** | **Sim** (`xQueueSendFromISR`, `xQueueReceiveFromISR`) |
| Semáforo Binário | Sinal de evento/sincronização (estado binário, sem carga de dados) | Acordar tarefa em evento/interrupção; sincronização nos **Labs 6 e 10** | **Sim** (`xSemaphoreGiveFromISR`) |
| Semáforo de Contagem | Contador de eventos ocorridos ou recursos livres (0 até `uxMaxCount`) | Contabilizar múltiplos eventos ou gerenciar `N` recursos; **Labs 10 e 14** | **Sim** (`xSemaphoreGiveFromISR`) |
| Mutex | Posse / exclusão mútua de recurso compartilhado (com herança de prioridade) | Proteger recurso compartilhado entre tarefas; **Labs 6 e 14** | **Não** |
| Event Group | Conjunto de bits/flags de evento combináveis por `AND` ou `OR` | Esperar múltiplas condições (`AND`) ou qualquer evento (`OR`); **Labs 10 e 14** | **Sim** (`xEventGroupSetBitsFromISR`) |

## Q13 *(estilo Exemplo 6.2)* ISR produz amostras de 4 B a 2 kHz; a tarefa consumidora acorda
a cada 40 ms. Dimensione a fila (itens e bytes) com folga de 2× e explique o que ocorre sem a
folga quando o Wi-Fi rouba CPU por 60 ms.

1. Tamanho da fila (com folga de 2×)

Produção: A $2\text{ kHz}$, a interrupção gera 2 amostras por milissegundo.

Em 40 ms: Chegam $2 \times 40 = \mathbf{80\text{ amostras}}$.Com folga de 2×: A fila precisa guardar $80 \times 2 = \mathbf{160\text{ itens}}$.

Em bytes: Como cada amostra tem $4\text{ bytes}$, a fila ocupa $160 \times 4 = \mathbf{640\text{ bytes}}$.

 O que acontece sem a folga se o Wi-Fi atrasar tudo por 60 ms?

. Sem a folga, a fila só cabe 80 amostras (o suficiente para 40 ms).

.  Se o Wi-Fi segurar a CPU por 60 ms, a tarefa que esvazia a fila atrasa, mas a interrupção continua gerando amostras sem parar.

Em 60 ms são geradas 120 amostras, mas a fila enche logo nos primeiros 40 ms (80 amostras). Nos 20 ms finais, 40 amostras são jogadas fora por falta de espaço, causando perda de dados.
## Q14 O que é inversão de prioridade? Descreva o caso Mars Pathfinder em 3 frases e explique como a **herança de prioridade** do mutex do FreeRTOS resolve (e por que semáforo binário não).

![Linha do tempo da inversão de prioridade](https://raw.githubusercontent.com/fabiobento/sis-emb-2026-2/main/assets/figuras/inversao_prioridade.png)

O que é: Uma tarefa importante (alta prioridade) fica travada esperando um recurso que está com uma tarefa menos importante (baixa prioridade), enquanto tarefas médias passam na frente e atrasam quem está segurando o recurso.

Mars Pathfinder (em 3 frases): Uma tarefa do tempo (baixa prioridade) pegou a trava do barramento de dados. Tarefas de comunicação (média prioridade) tomaram a CPU e impediram a tarefa do tempo de terminar e soltar a trava. Assim, a tarefa crítica (alta prioridade) ficou presa esperando, fazendo o Watchdog reiniciar a sonda várias vezes.

Herança de prioridade: O FreeRTOS aumenta na hora a prioridade de quem segura o mutex para o mesmo nível da tarefa importante que está esperando. Com isso, tarefas médias não passam por cima dela, permitindo que termine logo e libere o recurso.

Por que semáforo binário não resolve: O semáforo binário não tem "dono" — o sistema não sabe quem pegou (e outro lugar pode soltar), então não sabe de quem subir a prioridade.
    
## Q15. Escreva (pseudocódigo ou C) o padrão ISR→tarefa com semáforo binário para o botão da semana 4, incluindo `xSemaphoreGiveFromISR` e `portYIELD_FROM_ISR`, e justifique por que o processamento pesado fica na tarefa.

#include "FreeRTOS.h"
#include "task.h"
#include "semphr.h"

SemaphoreHandle_t sem_botao = NULL;

void isr_botao(void) {
    BaseType_t acordou_tarefa_maior = pdFALSE;

    xSemaphoreGiveFromISR(sem_botao, &acordou_tarefa_maior);
    portYIELD_FROM_ISR(acordou_tarefa_maior);
}

void tarefa_botao(void *param) {
    while (1) {
        if (xSemaphoreTake(sem_botao, portMAX_DELAY) == pdTRUE) {
            fazer_trabalho_pesado();
        }
    }
}

void setup_botao(void) {
    sem_botao = xSemaphoreCreateBinary();
    xTaskCreate(tarefa_botao, "TarefaBotao", 2048, NULL, 3, NULL);
    configurar_interrupcao_botao(isr_botao);
}

Justificativa:

. A interrupção tem que ser bem rápida para não atrasar outras interrupções importantes do sistema.

.Dentro da interrupção a gente não pode ficar esperando tempo, travando ou chamando função pesada.

.Na tarefa, o código pode rodar com calma, usar funções do FreeRTOS e ficar dormindo (xSemaphoreTake) sem gastar CPU quando ninguém apertou o botão.
