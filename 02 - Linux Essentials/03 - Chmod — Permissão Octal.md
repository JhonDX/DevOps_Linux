

**`chmod`** = altera as permissões de arquivos e diretórios.

### Valores

```
r = 4 → leitura
w = 2 → escrita
x = 1 → execução
```

### Como funciona

```
chmod 755 arquivo
```

A ordem é:

```
7    5    5
│    │    │
Dono Grupo Outros
```

Cada número é uma soma:

```
7 = 4+2+1 = rwx
6 = 4+2   = rw-
5 = 4+1   = r-x
4 = 4     = r--
3 = 2+1   = -wx
2 = 2     = -w-
1 = 1     = --x
0 = 0     = ---
```

### Principais exemplos

```
777 → rwxrwxrwx
755 → rwxr-xr-x
700 → rwx------
644 → rw-r--r--
600 → rw-------
```

### Exemplos de uso

```
chmod 755 script.sh
chmod 644 arquivo.txt
chmod 600 chave.pem
chmod 700 pasta/
```

### Regra para decorar

```
4 = r
2 = w
1 = x

Dono | Grupo | Outros
```
