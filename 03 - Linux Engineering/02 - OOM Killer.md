# Comando / Mecanismo: OOM Killer (Out Of Memory Killer)

O **OOM Killer** é um subsistema do Kernel do Linux acionado quando o sistema operacional fica completamente sem memória RAM e Swap disponível. Para evitar o travamento total do sistema (_kernel panic_), ele seleciona e encerra (_kill -9_) um ou mais processos para liberar memória.

## Sintaxe e Comandos de Diagnóstico

Não existe um comando direto `oomkiller`, pois trata-se de um processo interno do Kernel. Para analisar a sua atuação, utilizam-se os comandos abaixo:

Bash

```
# Verificar logs de execuções recentes do OOM Killer
dmesg -T | grep -i oom

# Buscar eventos nos logs do sistema
journalctl -k | grep -i oom

# Consultar a pontuação OOM atual de um processo específico (ex: PID 1234)
cat /proc/1234/oom_score

# Consultar o ajuste manual de prioridade (adj) de um processo
cat /proc/1234/oom_score_adj
```

## Como o Kernel Seleciona o Alvo: `oom_score`

O Kernel calcula uma pontuação chamada `oom_score` (de 0 a 1000) para cada processo em execução. **Quanto maior o valor, maior a chance de o processo ser encerrado.**

### Critérios de Cálculo da Pontuação

- **Consumo de Memória:** Processos que usam maiores porcentagens de RAM/Swap sobem na pontuação.
    
- **Privilégios (`root`):** Processos rodando como root recebem uma pequena redução no score para evitar a queda de serviços essenciais.
    
- **Tempo de Execução:** Processos antigos e com alto tempo de CPU costumam ter uma pontuação ligeiramente menor se comparados a processos novos gastando muita RAM.
    

## Configuração e Proteção de Processos

É possível ajustar manualmente a tolerância do OOM Killer para processos críticos (como bancos de dados ou agentes de backup) alterando o arquivo `/proc/[PID]/oom_score_adj`.

Os valores variam de **-1000** (nunca matar) a **1000** (primeiro a ser morto).

Bash

```
# Proteger um processo crítico para que ele NUNCA seja morto pelo OOM Killer
echo -1000 > /proc/1234/oom_score_adj

# Aumentar as chances de um processo secundário ser morto primeiro
echo 500 > /proc/1234/oom_score_adj
```

## Configurações Globais via `sysctl`

As regras globais do comportamento de alocação de memória podem ser ajustadas em `/etc/sysctl.conf`:

|**Parâmetro**|**Valores Comuns**|**Descrição**|
|---|---|---|
|**`vm.panic_on_oom`**|`0` _(Padrão)_<br><br>  <br><br>`1`|`0`: Chama o OOM Killer para matar um processo.<br><br>  <br><br>`1`: Gera Kernel Panic e reinicia a máquina imediatamente ao faltar memória.|
|**`vm.overcommit_memory`**|`0` _(Padrão)_<br><br>  <br><br>`1`<br><br>  <br><br>`2`|`0`: Kernel estima se há memória suficiente.<br><br>  <br><br>`1`: Permite alocação ilimitada (overcommit agressivo).<br><br>  <br><br>`2`: Impede overcommit com base no limite de Swap + RAM.|

## Diagnósticos Rápidos

- **Aplicação caindo misteriosamente sem log próprio:** Verifique `dmesg -T | grep -i oom` para confirmar se o processo foi morto pelo Kernel.
    
- **Out of Memory constante:** O sistema está com redimensionamento incorreto de RAM ou sofrendo de vazamento de memória (_memory leak_).
    
- **Serviço essencial morrendo:** Aplique `-1000` no `oom_score_adj` do serviço e configure o gerenciador de processos (systemd) para reiniciar a aplicação em caso de falha.