# Relatório de Sistemas Embarcados - Semana 2

##  Integrantes
* Alexandre de Oliveira Júnior
* Luisa Martins Schineider
* Miguel Arcanjo N. Meriguete

---

##  Entregas

### 1. Foto da montagem + print do monitor serial
![]
---

### 2. Tabela Total sizes
<img width="1403" height="989" alt="image" src="https://github.com/user-attachments/assets/4b5a3595-cd55-40e4-ae2b-ccb166f002ad" />

---

### 3. Tabela de medições da Parte C preenchida + a corrente calculada.
| **Medição** | **Onde** | **Valor esperado** | **Medido** |
| --- | --- | --- | --- |
| Alimentação do módulo | pino 5V ↔ GND | 4,75–5,25 V | 6,04 V |  
| Nível alto no GPIO | GPIO 2 ↔ GND (LED aceso) | ~3,3 V | 3,20 V |
| Queda no LED | anodo ↔ catodo (aceso) | ~1,8–2,1 V | 2,54 V |

**C.3** Com a queda do LED medindo 2,54 V e o GPIO 3,20, a corrente calculada é igual a 3mA. 

---

### 4. Resposta da Parte B.3 (atomicidade W1TS/W1TC)
A principal vantagem de utilizar os registradores W1TS e W1TC é garantir a atomicidade das operações, evitando falhas críticas de concorrência com ISR, permitindo alterar um pino em uma única instrução de hardware (operação atômica) apenas escrevendo no registrador, sem precisar ler o estado anterior e garantindo que nenhum pino tenha seu estado corrompido ou sobrescrito indevidamente.