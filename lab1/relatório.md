# Relatório de Sistemas Embarcados - Semana 1

##  Integrantes
* Alexandre de Oliveira Júnior
* Luisa Martins Schineider
* Miguel Arcanjo N. Meriguete

---

##  Entregas

### 1. Simulação
*  [Projeto no Wokwi](https://wokwi.com/projects/471650543538109441)

---

### 2. Experimento 1 - Período

| Período Testado (ms) | Piscar Perceptível? |
| :---: | :---: |
| **200** | Sim |
| **100** | Sim |
| **80**  | Não |
| **60**  | Não |
| **40**  | Não |
| **20**  | Não |

---

### 3. Experimento 2 - Assimetria

```c
int nivel = 0;

while (1) {                                         
    nivel = !nivel;                                
    gpio_set_level(PINO_LED, nivel);                
    printf("LED = %d\n", nivel);                   
    vTaskDelay(pdMS_TO_TICKS(900));
    
    nivel = !nivel;
    gpio_set_level(PINO_LED, nivel);                
    printf("LED = %d\n", nivel);                   
    vTaskDelay(pdMS_TO_TICKS(100));                  
}
```

---

### 4. Resposta do Experimento 3

A função `vTaskDelay()` coloca a tarefa no estado **Bloqueado**, liberando a CPU para executar outras tarefas do sistema ou entrar em modo de baixo consumo. 

Em contrapartida, um laço `for`vazio realiza uma espera ocupada, mantendo o processador a 100% de uso e consumindo energia desnecessariamente. 

> **Exemplo Prático:** Como observado no Exemplo 1.1, a falta de gerenciamento de energia (como manter o rádio ligado sem necessidade) pode reduzir a autonomia de um dispositivo alimentado por bateria de **10 meses para apenas 1 dia**.


### 5. Versão utilizada do Esp-IDF

5.2