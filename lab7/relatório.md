# Relatório de Sistemas Embarcados - Semana 7

##  Integrantes
* Alexandre de Oliveira Júnior
* Luisa Martins Schineider
* Miguel Arcanjo N. Meriguete

---

## Entregas

### 1. Tabela de calibração (Parte A, item 8, com R medido e desvio calculado) + comentário sobre o desvio da teoria (item 9).

| condição | R do LDR (multímetro) | V esperado (fórmula) | V medido (`V=`) | raw medido |
|---|---|---|---|---|
| luz da sala (≈ "claro") | 3.3k | 2.48V| ~1.3V | ~1700 |
| lanterna encostada | 4.2 k |  2.32V| ~2.9V | ~38000 |
| coberto com a mão (≈ "escuro") | 20k | 1.1V | ~0.07V | ~60 |

### 2. Tabela de ruído × M (Parte B) + o trecho de log do spike + 3 linhas sobre o compromisso ruído/atraso.


| M (janela) | desvio típico | atraso teórico (M−1)/2 amostras @ 20 Hz |
|---|---|---|
| 1 (sem filtro) | 7.6 | 0 ms |
| 4 | 3.7 | 75 ms |
| 8 | 2.2 | 175 ms |
| 32 | 1.2 | 775 ms |
---
Log do spike

![](https://raw.githubusercontent.com/MinguelANM/sis-emb/refs/heads/main/imagens/log%20de%20spike.png)


### 3. Registro da saturação (item 14) e resposta de projeto (item 15, com a conta do divisor).

![](https://raw.githubusercontent.com/MinguelANM/sis-emb/refs/heads/main/imagens/saturacao.png)

### 4. Valor eficaz medido (ou descrição do áudio) na Parte D + o valor que o multímetro mostrou em VDC.

### 5. (Opcional, Parte E) A tabela do item 24 preenchida, com o gráfico de pelo menos um dos casos (95 ou 100 Hz), e a resposta do item 25.