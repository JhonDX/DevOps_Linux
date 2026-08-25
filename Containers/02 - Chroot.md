# Comando: `chroot` (Change Root)

O `chroot` é um utilitário do Linux que altera o diretório raiz (`/`) aparente para o processo atual e seus filhos. O processo executado dentro dessa "prisão" (_chroot jail_) enxerga apenas a árvore de diretórios a partir do caminho especificado, isolando-o do restante do sistema hospedeiro.

## Sintaxe e Estrutura de Uso

Bash

```
chroot [opções] <NOVO_RAIZ> [COMANDO] [ARGUMENTOS]
```

- **`<NOVO_RAIZ>`:** O caminho do diretório contendo uma estrutura FHS válida (ex: `/mnt/debian`).
    
- **`[COMANDO]`:** O programa/shell a ser executado dentro do novo ambiente (se omitido, o padrão é o shell padrão definido em `$SHELL` ou `/bin/sh`).
    

## Exemplos Práticos de Execução

### 1. Acessar o ambiente de um container/sistema clonado

Bash

```
sudo chroot /mnt/debian /bin/bash
```

### 2. Executar um comando específico sem abrir uma sessão interativa

Bash

```
sudo chroot /mnt/debian /usr/bin/apt-get update
```

### 3. Acessar especificando usuário e grupo

Bash

```
sudo chroot --userspec=www-data:www-data /var/www/jail /bin/bash
```

## Pré-requisito Obrigatório: Montagens de Pontes de Sistema

Antes de acessar uma estrutura clonada via `chroot`, os sistemas de arquivos virtuais do Kernel do hospedeiro devem ser montados no diretório de destino. Sem isso, comandos que dependem de dispositivos, processos ou rede falharão (como `ps`, `apt`, `ping`).

Bash

```
# Script de preparação para chroot
TARGET="/mnt/debian"

# Montar os sistemas de arquivos virtuais do Kernel
sudo mount -t proc proc "$TARGET/proc"
sudo mount -t sysfs sys "$TARGET/sys"
sudo mount --bind /dev "$TARGET/dev"
sudo mount --bind /dev/pts "$TARGET/dev/pts"

# Entrar no chroot
sudo chroot "$TARGET" /bin/bash
```

## Processo de Desmontagem e Saída

Ao terminar o trabalho no chroot, você deve sair da sessão e desmontar os pontos de montagem na ordem inversa:

Bash

```
# 1. Sair da prisão chroot
exit

# 2. Desmontar em ordem inversa
sudo umount /mnt/debian/dev/pts
sudo umount /mnt/debian/dev
sudo umount /mnt/debian/sys
sudo umount /mnt/debian/proc
```

## Limitações Importantes de Segurança

- **Não é uma sandbox completa:** O `chroot` isola apenas o sistema de arquivos. Ele **não** isola redes, tabela de processos (PIDs), memória, IPC ou usuários do Kernel.
    
- **Fuga de chroot (_Chroot Escape_):** Um processo rodando como `root` dentro do `chroot` consegue facilmente quebrar o isolamento e acessar o sistema hospedeiro se não for combinado com Namespaces do Linux (`unshare`).
    
- **Dependência de bibliotecas:** Qualquer executável rodando dentro do `chroot` precisa ter todas as suas dependências (como a `libc`) presentes no novo diretório raiz.
    

## Comparativo: `chroot` vs `unshare`

|**Recurso**|**chroot puro**|**unshare (Namespaces)**|
|---|---|---|
|**Isolamento de Arquivos**|Sim|Sim|
|**Isolamento de Processos (PID)**|Não|Sim (`--pid`)|
|**Isolamento de Rede**|Não|Sim (`--net`)|
|**Isolamento de Usuários**|Não|Sim (`--user`)|
|**Complexidade**|Muito Baixa|Média|
