
Usa **letras** para definir permissões:

|Símbolo|Significado|
|---|---|
|`u`|usuário/dono|
|`g`|grupo|
|`o`|outros|
|`a`|todos|

|Operador|Função|
|---|---|
|`+`|adiciona permissão|
|`-`|remove permissão|
|`=`|define exatamente a permissão|

|Permissão|Letra|
|---|---|
|`r`|leitura|
|`w`|escrita|
|`x`|execução|

**Exemplos:**

```
chmod u+x arquivo.sh
```

Adiciona execução para o **dono**.

```
chmod g+w arquivo.txt
```

Adiciona escrita para o **grupo**.

```
chmod o-r arquivo.txt
```

Remove leitura dos **outros**.

```
chmod a+r arquivo.txt
```

Adiciona leitura para **todos**.

```
chmod u=rwx,g=rx,o=r arquivo
```
