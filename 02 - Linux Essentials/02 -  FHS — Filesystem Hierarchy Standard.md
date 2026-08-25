
# Resumo do FHS — Filesystem Hierarchy Standard

O **FHS (Filesystem Hierarchy Standard)** define uma organização padrão para os diretórios do Linux. A ideia é que, independentemente da distribuição, você consiga entender **onde ficam arquivos do sistema, programas, configurações, usuários e dados**.

## `/` — Raiz

É o **diretório principal** do Linux. Todos os outros diretórios ficam dentro dele.

Exemplo:

```bash
/
├── bin
├── boot
├── dev
├── etc
├── home
├── lib
├── proc
├── root
├── run
├── sbin
├── tmp
├── usr
└── var
```

---

## `/bin` — Comandos essenciais

Contém **programas essenciais** utilizados pelo sistema e pelos usuários.

Exemplos:

```bash
ls
cp
mv
cat
mkdir
rm
```

São comandos necessários para operações básicas do sistema.

> Em sistemas modernos, `/bin` geralmente é um link para `/usr/bin`.

---

## `/sbin` — Administração do sistema

Contém comandos voltados principalmente para **administração e manutenção do sistema**.

Exemplos:

```bash
mount
fsck
reboot
shutdown
```

Normalmente são utilizados pelo `root` ou por usuários com `sudo`.

> Assim como `/bin`, em muitas distribuições modernas `/sbin` é integrado a `/usr/sbin`.

---

## `/boot` — Inicialização

Contém arquivos necessários para o **processo de inicialização do Linux**.

Exemplos:

```text
vmlinuz
initrd
grub/
```

Aqui podemos encontrar o **kernel Linux** e arquivos utilizados pelo **GRUB**.

---

## `/dev` — Dispositivos

Contém arquivos que representam **dispositivos de hardware**.

Exemplos:

```text
/dev/sda
/dev/nvme0n1
/dev/null
/dev/tty
/dev/psaux
```

No Linux, muitos dispositivos são tratados como arquivos.

Por exemplo:

```text
/dev/sda
```

pode representar um disco.

```text
/dev/psaux
```

é um dispositivo relacionado à interface de mouse/PS/2.

---

## `/etc` — Configurações

É um dos diretórios mais importantes para administração Linux.

Contém **arquivos de configuração do sistema e dos serviços**.

Exemplos:

```text
/etc/passwd
/etc/shadow
/etc/hosts
/etc/hostname
/etc/fstab
/etc/ssh/
```

Por exemplo:

```bash
cat /etc/os-release
```

mostra informações sobre a distribuição Linux.

---

## `/home` — Usuários

É onde ficam os **diretórios pessoais dos usuários comuns**.

Exemplo:

```text
/home/jhz
/home/joao
/home/maria
```

Dentro do seu `/home/jhz` ficam seus arquivos pessoais:

```text
Documentos/
Downloads/
Imagens/
Projetos/
```

Também existem arquivos ocultos de configuração:

```text
~/.ssh/
~/.config/
~/.bashrc
```

---

## `/root` — Home do root

É o diretório pessoal do usuário **root**.

Não deve ser confundido com `/`, que é a raiz do sistema.

```text
/root
```

é o **home do root**.

Enquanto:

```text
/
```

é a **raiz de todo o sistema de arquivos**.

---

## `/lib` — Bibliotecas essenciais

Contém **bibliotecas compartilhadas** necessárias para programas essenciais do sistema.

É semelhante à ideia de DLLs no Windows.

Exemplo conceitual:

```text
programa → biblioteca → kernel
```

Em sistemas modernos, `/lib` geralmente está integrado ao `/usr/lib`.

---

## `/media` — Dispositivos removíveis

É utilizado como ponto de montagem para **mídias removíveis**, como:

- Pendrives
    
- HDs externos
    
- CDs/DVDs
    

Exemplo:

```text
/media/jhz/PENDRIVE
```

Quando você conecta um pendrive, o sistema pode montá-lo nesse tipo de localização.

---

## `/mnt` — Montagem temporária

Tradicionalmente utilizado para **montagens temporárias feitas manualmente pelo administrador**.

Exemplo:

```bash
sudo mount /dev/sdb1 /mnt
```

Nesse caso, o conteúdo de `/dev/sdb1` ficará acessível através de:

```text
/mnt
```

---

## `/opt` — Programas adicionais

Utilizado para instalar **softwares adicionais que não fazem parte diretamente da instalação padrão do sistema**.

Exemplo:

```text
/opt/meu-programa/
```

É comum encontrar aplicações de terceiros instaladas nesse local.

---

## `/proc` — Informações do kernel e processos

É um **sistema de arquivos virtual** criado pelo kernel.

Não são arquivos normais armazenados no disco.

Ele fornece informações sobre:

- Processos
    
- CPU
    
- Memória
    
- Kernel
    
- Sistema
    

Exemplos:

```bash
cat /proc/cpuinfo
cat /proc/meminfo
```

Também podemos consultar processos:

```text
/proc/1
/proc/1000
```

Cada diretório numérico normalmente representa um **PID**.

---

## `/run` — Dados temporários de execução

Contém informações utilizadas pelos **processos e serviços durante a execução do sistema**.

Exemplos:

```text
/run/sshd/
/run/systemd/
/run/user/
```

Normalmente fica armazenado em memória (`tmpfs`) e pode ser recriado durante o boot.

---

## `/srv` — Dados de serviços

Destinado a dados que são **servidos por serviços do sistema**.

Por exemplo, um servidor web poderia utilizar:

```text
/srv/www/
```

Ou um serviço FTP:

```text
/srv/ftp/
```

Não é tão utilizado quanto `/var` ou `/var/www` em muitas distribuições, mas faz parte da estrutura do FHS.

---

## `/tmp` — Arquivos temporários

Utilizado para **arquivos temporários** criados por programas e usuários.

Exemplo:

```bash
/tmp/arquivo.tmp
```

Arquivos aqui podem ser removidos durante a inicialização ou por mecanismos de limpeza do sistema.

**Não coloque arquivos importantes permanentemente em `/tmp`.**

---

## `/usr` — Programas e recursos do sistema

É um dos diretórios mais importantes.

Contém grande parte dos **programas, bibliotecas e recursos utilizados pelo sistema**.

Principais subdiretórios:

```text
/usr/bin
/usr/sbin
/usr/lib
/usr/share
/usr/local
```

Por exemplo:

```text
/usr/bin/ls
/usr/bin/python3
/usr/bin/git
```

Em distribuições modernas, muitos diretórios tradicionalmente separados (`/bin`, `/sbin`, `/lib`) são integrados ao `/usr`.

---

## `/usr/local` — Software instalado localmente

Destinado a programas instalados **manualmente pelo administrador**, sem substituir os arquivos gerenciados pelo sistema.

Exemplo:

```text
/usr/local/bin
/usr/local/lib
/usr/local/share
```

É uma boa localização para softwares compilados ou instalados manualmente.

---

## `/var` — Dados que mudam constantemente

O `/var` armazena dados que **crescem ou mudam durante a utilização do sistema**.

Exemplos:

```text
/var/log
/var/cache
/var/lib
/var/tmp
```

### `/var/log`

Guarda logs:

```text
/var/log/syslog
/var/log/auth.log
/var/log/journal/
```

Muito importante para **diagnóstico e troubleshooting**.

### `/var/lib`

Guarda dados persistentes utilizados por serviços.

Exemplo:

```text
/var/lib/docker
/var/lib/mysql
```

### `/var/cache`

Guarda arquivos de cache utilizados por programas.

---

# 🧠 Forma fácil de memorizar

|Diretório|Função principal|
|---|---|
|`/`|Raiz do sistema|
|`/bin`|Comandos essenciais|
|`/sbin`|Administração do sistema|
|`/boot`|Inicialização/kernel|
|`/dev`|Dispositivos|
|`/etc`|Configurações|
|`/home`|Usuários|
|`/root`|Home do root|
|`/lib`|Bibliotecas|
|`/media`|Mídias removíveis|
|`/mnt`|Montagens temporárias|
|`/opt`|Softwares adicionais|
|`/proc`|Kernel/processos|
|`/run`|Dados de execução|
|`/srv`|Dados de serviços|
|`/tmp`|Temporários|
|`/usr`|Programas e recursos|
|`/var`|Dados variáveis/logs|

### Regra prática

Se você estiver administrando um servidor Linux, os diretórios que mais vai encontrar no dia a dia são:

**`/etc` → configurações**  
**`/var/log` → logs**  
**`/var/lib` → dados de serviços**  
**`/home` → usuários**  
**`/usr/bin` → programas**  
**`/dev` → dispositivos**  
**`/proc` → informações do kernel/processos**  
**`/tmp` → temporários**  
**`/boot` → inicialização**