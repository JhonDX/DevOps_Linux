# vmstat (Virtual Memory Statistics)

O `vmstat` é uma ferramenta essencial no Linux para monitorar o desempenho do sistema em tempo real, reportando dados de memória, processos, paginação (swap), E/S (I/O) e CPU.

## Sintaxe Básica

Bash

```
vmstat [opções] [intervalo_em_segundos] [contagem]
```

- `vmstat`: Exibe a média do sistema desde o último boot.
    
- `vmstat 2`: Atualiza as estatísticas a cada 2 segundos continuamente.
    
- `vmstat 2 5`: Atualiza a cada 2 segundos, exatamente 5 vezes.
    
- `vmstat -s`: Exibe uma tabela sumarizada de estatísticas de memória.
    
- `vmstat -d`: Exibe estatísticas detalhadas de disco.
    

## Interpretando as Colunas Principais

Ao rodar `vmstat 1`, você verá uma saída dividida em 6 blocos:

### 1. procs (Processos)

|**Coluna**|**Descrição**|**O que observar**|
|---|---|---|
|**`r`**|Processos em fila de execução (running/runnable)|**Gargalo de CPU:** Se for maior que o número de núcleos de CPU.|
|**`b`**|Processos em estado de espera ininterrupta (blocked - aguardando I/O)|**Gargalo de Disco:** Valores altos indicam lentidão no armazenamento.|

### 2. memory (Memória)

|**Coluna**|**Descrição**|
|---|---|
|**`swpd`**|Quantidade de memória virtual (Swap) usada|
|**`free`**|Memória RAM completamente livre|
|**`buff`**|Memória usada como buffer temporário do SO|
|**`cache`**|Memória usada para cache de páginas de arquivos lidos do disco|

### 3. swap (Troca de Páginas)

> Atenção: Se estas colunas apresentarem valores altos e constantes, o sistema está sem RAM e o desempenho caiu vertiginosamente.

|**Coluna**|**Descrição**|
|---|---|
|**`si`**|_Swap In_ — Memória gravada do Swap para a RAM (KB/s)|
|**`so`**|_Swap Out_ — Memória gravada da RAM para o Swap (KB/s)|

### 4. io (Entrada e Saída)

|**Coluna**|**Descrição**|
|---|---|
|**`bi`**|_Blocks In_ — Blocos recebidos do dispositivo de disco (leitura)|
|**`bo`**|_Blocks Out_ — Blocos enviados para o dispositivo de disco (escrita)|

### 5. system (Chamadas do Sistema)

|**Coluna**|**Descrição**|
|---|---|
|**`in`**|Interrupções por segundo (hardware/software)|
|**`cs`**|Trocas de contexto (_Context Switches_) por segundo|

### 6. cpu (Uso da CPU - em % do tempo total)

|**Coluna**|**Descrição**|**O que observar**|
|---|---|---|
|**`us`**|Tempo gasto executando código de usuário (User)|Aplicações|
|**`sy`**|Tempo gasto em chamadas do Kernel (System)|Código do sistema operacional|
|**`id`**|Tempo ocioso (Idle)|Quanto mais alto, mais livre está a CPU|
|**`wa`**|Tempo esperando por I/O de disco (Wait)|**Gargalo de Disco:** Se elevado, a CPU está parada esperando leituras/escritas no disco.|
|**`st`**|Tempo "roubado" (Stolen) por uma máquina virtual hospedeira|Relevante em ambientes de nuvem/hypervisors|

## Diagnósticos Rápidos

- **Processador sobrecarregado:** Coluna `r` alta e `id` perto de 0.
    
- **Falta de RAM / Paginação excessiva:** Colunas `si` e `so` maiores que zero de forma contínua.
    
- **Lentidão no HD/SSD:** Coluna `wa` alta (acima de 15-20%) e `b` maior que 0.