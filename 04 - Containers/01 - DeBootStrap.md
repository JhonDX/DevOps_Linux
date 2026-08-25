# Comando: debootstrap

O `debootstrap` é uma ferramenta dos sistemas baseados em Debian (como Ubuntu, Mint e Kali) utilizada para criar um sistema operacional base completo dentro de um diretório. Em vez de "clonar" uma instalação existente, ele baixa os pacotes `.deb` diretamente dos repositórios oficiais e extrai a estrutura padrão de diretórios (FHS - _Filesystem Hierarchy Standard_).

É a ferramenta ideal para criar _chroots_, containers minimalistas, imagens para máquinas virtuais ou para realizar a instalação limpa de um sistema a partir de um _live CD_.

## Sintaxe e Estrutura de Uso

Bash

```
debootstrap [opções] <SUÍTE/DISTRIBUIÇÃO> <DIRETÓRIO_ALVO> [ESPELHO/MIRROR]
```

### Argumentos da Sintaxe

- **`<SUÍTE/DISTRIBUIÇÃO>`:** A versão do sistema que deseja instalar (ex: `stable`, `testing`, `unstable`, `bookworm`, `trixie`, `focal`, `jammy`).
    
- **`<DIRETÓRIO_ALVO>`:** O caminho do diretório onde o sistema base será montado. Se não existir, a ferramenta o criará.
    
- **`[ESPELHO/MIRROR]`** _(Opcional)_: O repositório HTTP/FTP de onde os pacotes serão baixados. Se omitido, usa o repositório padrão do Debian.
    

## Exemplos Práticos de Execução

### 1. Criar um Debian Stable padrão

Bash

```
sudo debootstrap stable /mnt/debian http://deb.debian.org/debian
```

### 2. Criar uma versão específica (Debian 12 Bookworm)

Bash

```
sudo debootstrap bookworm /mnt/debian_12 http://deb.debian.org/debian
```

### 3. Criar uma base Ubuntu (Ubuntu 22.04 Jammy LTS)

Bash

```
sudo debootstrap jammy /mnt/ubuntu http://archive.ubuntu.com/ubuntu
```

## Opções Úteis (`[opções]`)

|**Opção**|**Descrição**|
|---|---|
|**`--arch=<arquitetura>`**|Define a arquitetura dos pacotes (ex: `amd64`, `arm64`, `i386`). Permite criar ambientes de outras arquiteturas.|
|**`--variant=minbase`**|Cria uma instalação extremamente reduzida (ideal para containers ou ambientes muito leves).|
|**`--include=<pacote1,pacote2>`**|Instala pacotes adicionais durante o processo (ex: `--include=vim,curl,net-tools`).|
|**`--exclude=<pacote1>`**|Remove pacotes padrão da lista de instalação para economizar espaço.|
|**`--foreign`**|Prepara o sistema para um estágio inicial em arquiteturas diferentes (usado para bootstrap em dois estágios).|

## Passo a Passo: Do Bootstrap ao Ambiente Funcional

Após rodar o `debootstrap`, a estrutura FHS estará criada no diretório, mas você precisa montar os sistemas de arquivos virtuais do Kernel antes de acessar o ambiente via `chroot`:

Bash

```
# 1. Criar o sistema base
sudo debootstrap stable /mnt/debian http://deb.debian.org/debian

# 2. Montar os diretórios virtuais do Kernel
sudo mount -t proc proc /mnt/debian/proc
sudo mount -t sysfs sys /mnt/debian/sys
sudo mount --bind /dev /mnt/debian/dev
sudo mount --bind /dev/pts /mnt/debian/dev/pts

# 3. Acessar o novo sistema
sudo chroot /mnt/debian /bin/bash
```

## Casos de Uso Comuns

- **Criação de Containers Manuais:** Base de arquivos leve para isolamento com `chroot` ou `unshare` (Namespace).
    
- **Recuperação de Sistemas (Rescue):** Instalar uma distribuição funcional a partir de uma máquina comprometida ou disco externo.
    
- **Instalação do Debian do Zero:** Sem utilizar o instalador gráfico convencional (_Debian Installer_).
    
- **Ambientes de Build/Compilação:** Criar sistemas limpos ("sandbox") para compilar softwares sem poluir o sistema hospedeiro.