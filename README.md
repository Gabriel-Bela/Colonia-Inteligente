# Colônia Inteligente
Este arquivo contém as instruções para execução do programa da colônia, onde pode ser verificadas diversas variáveis para manter a vida fora de nosso planeta.

# sistema da Colônia Inteligente 

Sistema em Python (arquivo único `colonia.py`, sem dependências) que organiza os dados de uma colônia, analisa energia, prevê geração eólica, define prioridades entre sistemas e toma decisões automáticas.

Todos os dados são digitados no terminal, pelo menu.

## Como executar

```
python colonia.py
```

Requer Python 3.8 ou superior. Para sair a qualquer momento, escolha `0` no menu ou use Ctrl+C.

## Regras gerais de digitação

- Números aceitam vírgula ou ponto decimal (`10,5` ou `10.5`).
- Se o valor for inválido, o programa avisa e repete a pergunta.
- Listas são digitadas com números separados por espaço (ex.: `8 10 12`).
- Os dados da colônia ficam só na memória: ao fechar o programa, tudo volta aos valores iniciais.

## Dados iniciais da colônia

| Item | Valor |
|---|---|
| Solar / Eólico | 45 / 25 (geração 70) |
| Reserva | 30 (limite de 100 no ciclo completo) |
| Vento / Temperatura | 11 / -20 |

| Prioridade | Sistema | Consumo | Essencial |
|---|---|---|---|
| P1 | suporte_vida | 30 | sim |
| P2 | habitacao | 20 | sim |
| P3 | comunicacao | 10 | não |
| P4 | laboratorio | 15 | não |
| P5 | entretenimento | 10 | não |

P1 é a prioridade mais importante. Suporte à vida e habitação nunca são desligados.

## Menu: como usar cada item

### 1 - Ver painel da colônia

Mostra um resumo do estado atual: geração solar e eólica, geração total, consumo (só dos sistemas ligados), reserva, vento, temperatura e uma análise da energia. Não pede nenhum dado.

Com os valores iniciais, o painel mostra `Geração: 70 | Consumo: 85 | Reserva: 30` e `ATENÇÃO: consumo maior que geração (coberto pela reserva)`.

### 2 - Navegar pelos subsistemas

Consulta qualquer parte da colônia por caminho, separando os níveis com `/`.

- Digite o caminho. Exemplos: `energia/solar/geracao` (resultado 45), `energia/reserva`, `clima/vento`, `sistemas/suporte_vida`.
- Se o caminho não existir, aparece `Erro: Caminho inexistente: ...` e você volta ao menu.

### 3 - Analisar uso de energia

Compara geração, consumo e reserva que você informa (não usa os dados da colônia).

1. Digite a geração.
2. Digite o consumo.
3. Digite a reserva (0 se não houver).

| Situação | Mensagem |
|---|---|
| Consumo maior que geração e reserva cobre a diferença | ATENÇÃO: consumo maior que geração (coberto pela reserva) |
| Consumo maior que geração e reserva não cobre | ALERTA: consumo maior que geração |
| Geração maior que consumo | SUGESTÃO: armazenar energia excedente |
| Geração igual ao consumo | OK: geração e consumo equilibrados |

Os valores não podem ser negativos.

### 4 - Prever energia eólica (regressão)

Estima a energia eólica a partir da velocidade do vento, com uma reta ajustada sobre um histórico (y = a·x + b).

1. Responda `s` para usar o histórico padrão (vento `8 10 12`, energia `20 25 30`) ou `n` para informar o seu.
2. Se escolheu `n`, digite o histórico de vento e o de energia eólica, com o mesmo número de valores (mínimo 2).
3. Digite a velocidade do vento a prever.

Exemplo: histórico padrão e vento 11 dão `Previsão de energia eólica ≈ 27.5`.

Erros tratados: listas de tamanhos diferentes, menos de 2 pontos ou todos os ventos iguais mostram `Erro na regressão`. Previsões negativas viram 0.

### 5 - Decidir ação (energia, consumo)

Aplica as regras de decisão a uma energia e um consumo que você informa. As regras seguem uma ordem de prioridade e vale a primeira que se aplicar.

| Ordem | Condição | Ação | Modo sugerido |
|---|---|---|---|
| 1 | energia < 20 | CRÍTICO: manter só suporte à vida e desligar sistemas não essenciais | emergencia |
| 2 | energia < 50 e consumo >= 80 | ALERTA: ativar modo economia | economia |
| 3 | energia < 50 | ALERTA: reduzir consumo | reduzido |
| 4 | consumo >= 80 | AVISO: consumo alto, monitorar | normal |
| - | nenhuma das anteriores | OK: operação normal | normal |

Exemplos: energia 40 e consumo 70 dão `ALERTA: reduzir consumo`; energia 40 e consumo 85 dão `ALERTA: ativar modo economia`.

Este item só mostra a decisão. Ele não liga nem desliga nenhum sistema.

### 6 - Distribuir energia por prioridade

Liga os sistemas, da prioridade P1 em diante, enquanto a energia que você informar for suficiente.

1. O programa mostra a tabela de sistemas.
2. Digite a energia disponível.
3. O programa liga os essenciais sempre. Para os não essenciais, liga cada um se couber na energia restante. Quando um não cabe, ele e todos os menos importantes ficam desligados.
4. Mostra a nova tabela, a energia restante e os sistemas desligados.

Exemplo: com energia 70, ficam desligados `laboratorio` e `entretenimento` e restam 10. Com energia 45, aparece o alerta de que a energia não cobre nem os sistemas essenciais.

O resultado fica guardado e aparece no painel (item 1).

### 7 - Definir modo de operação

Liga e desliga os sistemas de uma vez conforme o modo escolhido. Não considera a energia disponível.

| Modo | Fica ligado | Fica desligado |
|---|---|---|
| normal | todos | nenhum |
| reduzido | até P4 | entretenimento |
| economia | até P3 | laboratorio, entretenimento |
| emergencia | só essenciais (P1 e P2) | comunicacao, laboratorio, entretenimento |

Digite o nome do modo (maiúsculas e minúsculas não importam; `emergencia` é sem acento). Um nome inexistente mostra `Modo inválido.` O modo `normal` religa tudo.

### 8 - Simular ciclo completo

Roda todas as etapas em sequência, usando os dados atuais da colônia.

1. Digite a velocidade do vento atual.
2. Previsão: calcula a geração eólica pelo histórico padrão e a grava na colônia.
3. Análise: compara geração, demanda total (todos os sistemas) e reserva.
4. Decisão: as regras do item 5 escolhem a ação e o modo, usando como energia a geração somada à reserva.
5. Prioridades: aplica o limite do modo e a distribuição por prioridade (item 6).
6. Reserva: nova reserva = reserva + geração - consumo final, limitada entre 0 e 100.

Exemplo com os dados iniciais e vento 11: eólica ≈ 27.5, geração 72.5, demanda 85, reserva 30. A decisão é `AVISO: consumo alto, monitorar` (modo normal), nenhum sistema é desligado e a reserva passa de 30 para 17.5.

Se a energia não cobrir nem os sistemas essenciais, o ciclo mostra `ENERGIA INSUFICIENTE até para os sistemas essenciais`.

### 9 - Alterar um dado numérico

Muda um valor da colônia para montar cenários.

1. Digite o caminho. Exemplos: `energia/reserva`, `energia/solar/geracao`, `clima/vento`, `clima/temperatura`, `sistemas/laboratorio/consumo`, `sistemas/laboratorio/prioridade`.
2. Digite o novo valor (o valor atual aparece na pergunta).

Caminhos inexistentes ou que não sejam números (grupos como `energia/solar`, ou o campo `ligado`) mostram uma mensagem e voltam ao menu.

Observações:
- Este item não limita valores: aceita negativos, e a reserva pode passar de 100.
- Alterar a prioridade muda a ordem dos sistemas nos itens 6, 7 e 8.
- O item 8 recalcula `energia/eolico/geracao` pela previsão, então uma alteração manual nesse valor é perdida no ciclo.

Exemplo de cenário crítico: zere `energia/reserva`, reduza `energia/solar/geracao` para 5 e rode o item 8. O resultado é o modo `emergencia`.

### 0 - Sair

Encerra o programa.

## Receitas rápidas

| Quero... | Use |
|---|---|
| Ver como a colônia está agora | item 1 |
| Saber o que fazer com certa energia e consumo | item 5 |
| Ver quem fica ligado com uma energia disponível | item 6 |
| Forçar um modo (ex.: economia) | item 7 |
| Testar um cenário completo | item 9 e depois item 8 |
