`ls` = **list** → lista arquivos e diretórios.

### Principais opções

|Comando|Função|
|---|---|
|`ls`|Lista arquivos e diretórios|
|`ls -l`|Lista com detalhes|
|`ls -a`|Mostra arquivos ocultos|
|`ls -la`|Detalhes + arquivos ocultos|
|`ls -h`|Tamanhos legíveis (`KB`, `MB`, `GB`)|
|`ls -lh`|Detalhes + tamanhos legíveis|
|`ls -R`|Lista recursivamente|
|`ls -t`|Ordena por data de modificação|
|`ls -S`|Ordena por tamanho|
|`ls -r`|Inverte a ordem|
|`ls -d */`|Mostra somente diretórios|

### Mais usados

```
ls
```

Lista arquivos.

```
ls -l
```

Mostra permissões, proprietário, grupo, tamanho e data.

```
ls -la
```

Mostra **tudo**, incluindo arquivos ocultos, com detalhes.

```
ls -lh
```

Mostra detalhes com tamanho mais fácil de ler.

```
ls -lah
```

**Muito usado:** mostra arquivos ocultos + detalhes + tamanhos legíveis.

### Exemplo do `ls -l`

```
-rwxr-xr-- 1 joao dev 2.5K ago 23 script.sh
```

- `-` → arquivo
- `rwx` → permissões do dono
- `r-x` → permissões do grupo
- `r--` → permissões de outros
- `joao` → proprietário
- `dev` → grupo
- `2.5K` → tamanho
- `script.sh` → nome