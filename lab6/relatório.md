# Relatório de Sistemas Embarcados - Semana 6

##  Integrantes
* Alexandre de Oliveira Júnior
* Luisa Martins Schineider
* Miguel Arcanjo N. Meriguete

---

## Entregas

### 1.(CANCELADA)
---
### 2. Números da Parte B (itens por rajada do item 14, perdas com 700 ms do item 15) + a conta e o novo tamanho da fila do item 16.
itens por rajada do item 14 = 30 itens.  
6 perdas por rajada do item 15.  
70 × 2 = 140 → potência de 2 seguinte: 256 itens para o item 16.  

---

### 3. Tabela de latências do item 22 + código da sua ISR e da tarefa (só os dois blocos).
| método | latência média | latência máx |
|---|---|---|
| Lab 4: flag + polling da tarefa |102,00 ms |105,20 ms |
| Lab 6: semáforo + take bloqueante |21,9 µs|51 µs|

**Código da ISR e Tarefa:**
```c
static void IRAM_ATTR isr_botao(void *arg)
{
    int64_t agora = esp_timer_get_time();
    if (agora - s_ultimo_evento_us <= DEBOUNCE_US) {
        return;                               // bounce: ignora, não conta, não dá
    }
    s_ultimo_evento_us = agora;
    s_t_isr = agora;

    BaseType_t woken = pdFALSE;
    xSemaphoreGiveFromISR(sem, &woken);        // ~1 µs: só sinaliza "aconteceu"
    portYIELD_FROM_ISR(woken);                 // troca de contexto JÁ, se preciso
}

static void tarefa_botao(void *arg)
{
    uint32_t eventos = 0;
    while (1) {
        xSemaphoreTake(sem, portMAX_DELAY);    // dorme sem gastar CPU até o give
        int64_t latencia_us = esp_timer_get_time() - s_t_isr;
        printf("evento #%lu | latencia ISR->tarefa: %lld us\n",
               (unsigned long)(++eventos), latencia_us);
    }
}
```

---

### 4. Resposta do item 23 (binário × contador).

 No semáforo binário (USAR_CONTADOR 0) ele possui apenas dois estados (disponível ou ocupado). Se várias interrupções ocorrerem rapidamente, os give consecutivos podem se juntar, fazendo com que alguns eventos não sejam contados isoladamente.  
 Já no semáforo contador (USAR_CONTADOR 1) ele utiliza xSemaphoreCreateCounting(10, 0), permitindo acumular até 10 liberações pendentes. Assim, cada give é contabilizado individualmente, permitindo que a tarefa processe os cinco eventos recebidos.

---

### 5. Uma frase honesta: qual primitiva você usaria para proteger o barramento I2C que duas tarefas compartilharão na semana 9 — e por que não um semáforo binário? (Dica: Mars Pathfinder.)

Para garantir o acesso seguro ao barramento I2C compartilhado entre as tarefas, usariamos o Mutex, pois ele oferece o mecanismo de herança de prioridade (priority inheritance). Diferentemente do semáforo binário, esse recurso ajuda a evitar problemas de inversão de prioridade, como o que ocorreu na missão Mars Pathfinder.