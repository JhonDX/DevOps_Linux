# Comando: `unshare`

O `unshare` é um utilitário do Linux usado para desagrupar (_disassociate_) os namespaces de um processo do sistema hospedeiro. Ele permite criar containers de forma manual no Linux, isolando recursos como tabela de processos, rede, usuários e pontos de montagem sem depender do Docker ou do Containerd.

## Sintaxe e Análise da Linha de Comando

Bash

```
unshare --mount --uts --ipc --net --pid --fork --user --map-root-user <COMANDO>
```

Se nenhum comando for especificado ao final, o `unshare` executa o shell padrão (geralmente `/bin/bash`) no ambiente isolado.

## Explicação Detalhada das Flags (Namespaces)

|**Flag**|**Namespace Criado**|**O que isola / O que faz**|
|---|---|---|
|**`--mount`**|Mount (`mnt`)|Cria uma tabela de pontos de montagem independente. Alterações com `mount`/`umount` não afetam o hospedeiro.|
|**`--uts`**|UTS|Isola a definição do nome de host (_hostname_) e domínio do sistema.|
|**`--ipc`**|IPC|Isola recursos de comunicação entre processos (_Inter-Process Communication_, como filas de mensagens e memória compartilhada).|
|**`--net`**|Network (`net`)|Isola a pilha de rede. O novo ambiente nasce apenas com a interface de _loopback_ desativada, sem acesso à internet do hospedeiro.|
|**`--pid`**|Process ID (`pid`)|Isola a árvore de processos. O primeiro comando executado neste ambiente torna-se o **PID 1**.|
|**`--fork`**|_(Auxiliar PID)_|Força o `unshare` a criar um novo processo filho (_fork_) antes de rodar o comando. Obrigatório ao usar `--pid`.|
|**`--user`**|User (`usr`)|Isola a tabela de usuários e grupos. Permite mapear privilégios sem precisar de acesso `sudo` no hospedeiro.|
|**`--map-root-user`**|_(Auxiliar User)_|Mapeia o usuário atual não-privilegiado do hospedeiro para o **`root` (UID 0)** dentro da nova namespace.|

## Uso Prático: Criando um Container Completo do Zero

Para combinar o isolamento do `unshare` com o sistema de arquivos de um diretório (como um criado via `debootstrap`), utiliza-se o `chroot`:

Bash

```
# Executando um container isolado em /mnt/debian
sudo unshare --mount --uts --ipc --net --pid --fork --user --map-root-user \
    chroot /mnt/debian /bin/bash
```

### Ativando a Tabela de Processos e a Rede no Container

Ao entrar na namespace, a interface de rede estará inativa e a tabela `/proc` não estará visível para o `ps`:

Bash

```
# 1. Montar a tabela de processos dentro do container
mount -t proc proc /proc

# 2. Verificar se o bash é o PID 1
ps -ef

# 3. Ativar a interface local de loopback
ip link set dev lo up
```

## Tabela Comparativa: Namespaces do Linux

|**Flag do unshare**|**O que torna invisível para o container?**|
|---|---|
|**`--pid`**|Os processos rodando fora do container no sistema hospedeiro.|
|**`--net`**|As interfaces de rede (eth0, wlan0) e as portas abertas no hospedeiro.|
|**`--mount`**|As montagens de disco feitas dentro do container não aparecem fora.|
|**`--user`**|O fato de você não ser o `root` de verdade no sistema principal.|