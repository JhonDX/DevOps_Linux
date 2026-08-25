# Conceito Fundamental: Namespaces do Kernel Linux

Os **Namespaces** são um recurso fundamental do Kernel do Linux que abstrai e isola recursos globais do sistema em instâncias independentes.

Enquanto o `chroot` isola apenas o sistema de arquivos, os **namespaces dividem o sistema operacional em visões isoladas**: processos rodando dentro de um namespace acreditam ter seus próprios recursos dedicados, sem enxergar os demais recursos do hospedeiro. É a tecnologia base que viabilizou motores de containers como **Docker, LXC e Podman**.

## Os 8 Namespaces do Kernel Linux

| **Namespace**       | **Flag no unshare** | **Data de Introdução** | **O que Isola / Recursos Gerenciados**                                                                                         |
| ------------------- | ------------------- | ---------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| **Mount (`mnt`)**   | `--mount`           | Linux 2.4.19 (2002)    | Pontos de montagem, discos e sistemas de arquivos (`mount`, `umount`).                                                         |
| **UTS**             | `--uts`             | Linux 2.6.19 (2006)    | Hostname e nome de domínio NIS do sistema (`hostname`).                                                                        |
| **IPC**             | `--ipc`             | Linux 2.6.19 (2006)    | Comunicação entre processos: System V IPC e filas de mensagens POSIX.                                                          |
| **PID**             | `--pid`             | Linux 2.6.24 (2008)    | Árvore de PIDs. O primeiro processo do namespace vira o **PID 1**.                                                             |
| **Network (`net`)** | `--net`             | Linux 2.6.29 (2009)    | Interfaces de rede, rotas, tabelas ARP, regras de firewall (`iptables`/`nftables`) e portas de conexões.                       |
| **User (`usr`)**    | `--user`            | Linux 3.8 (2013)       | IDs de Usuário e Grupo (UID/GID). Permite ter **UID 0 (`root`)** dentro do namespace sem ter privilégios `root` no hospedeiro. |
| **Cgroup**          | `--cgroup`          | Linux 4.6 (2016)       | Visão do diretório raiz dos Control Groups (`/proc/self/cgroup`).                                                              |
| **Time**            | `--time`            | Linux 5.6 (2020)       | Relógios de tempo do sistema (`CLOCK_MONOTONIC` e `CLOCK_BOOTTIME`).                                                           |

## Como o Kernel Gerencia Namespaces (`/proc`)

Cada processo no Linux possui um diretório em `/proc/[PID]/ns/` contendo links simbólicos que identificam a qual namespace ele pertence:

Bash

```
# Listar os namespaces do processo atual
ls -l /proc/self/ns/
```

Se dois processos possuem o mesmo ID numérico (i-node) no link de um namespace (ex: `net:[4026531992]`), significa que eles compartilham aquela mesma visão do sistema.

## Syscalls e Comandos de Manipulação

O Kernel disponibiliza 3 Chamadas de Sistema (_Syscalls_) e utilitários de espaço de usuário para manipular namespaces:

### 1. Chamadas de Sistema (API do Kernel)

- **`clone()`:** Cria um novo processo filho especificando flags de namespace (ex: `CLONE_NEWPID | CLONE_NEWNET`) para criá-lo já isolado.
    
- **`unshare()`:** Desassocia o processo atual de seus namespaces anteriores e cria novos.
    
- **`setns()`:** Anexa o processo atual a um namespace já existente no sistema (base para o comando `docker exec`).
    

### 2. Comandos de Terminal

- **`unshare`:** Executa um programa/shell em novos namespaces.
    
- **`nsenter`:** Entra nos namespaces de outro processo já em execução:
    
    Bash
    
    ```
    # Entrar no namespace de rede e PID do processo 1234
    sudo nsenter --target 1234 --net --pid /bin/bash
    ```
    
- **`lsns`:** Lista todos os namespaces ativos no sistema hospedeiro.
    

## Relação: Namespaces vs. Cgroups vs. Containers

Para criar um container funcional, o Linux utiliza duas tecnologias complementares do Kernel:

- **Namespaces (Isolamento):** Define **o que o container pode enxergar** (rede, arquivos, outros processos).
    
- **Cgroups - Control Groups (Limitação):** Define **quanto recurso o container pode consumir** (limites de memória RAM, cota de CPU, I/O de disco).