### SYSTEMCTL — Gerenciador de serviços

`systemctl` é o comando utilizado para **controlar e consultar serviços do Linux** que utilizam o **systemd**.

|Comando|Função|
|---|---|
|`systemctl status serviço`|Verifica o estado do serviço|
|`systemctl start serviço`|Inicia o serviço|
|`systemctl stop serviço`|Para o serviço|
|`systemctl restart serviço`|Reinicia o serviço|
|`systemctl reload serviço`|Recarrega a configuração sem reiniciar|
|`systemctl enable serviço`|Inicia automaticamente no boot|
|`systemctl disable serviço`|Remove o início automático|
|`systemctl enable --now serviço`|Habilita e inicia imediatamente|
|`systemctl disable --now serviço`|Desabilita e para imediatamente|
|`systemctl is-active serviço`|Verifica se está ativo|
|`systemctl is-enabled serviço`|Verifica se inicia no boot|
|`systemctl list-units --type=service`|Lista serviços carregados|

### Exemplos

```
systemctl status nginx
```

Verifica o estado do **Nginx**.

```
sudo systemctl restart nginx
```

Reinicia o Nginx.

```
sudo systemctl enable nginx
```

Faz o Nginx iniciar automaticamente junto com o sistema.

```
sudo systemctl enable --now nginx
```

**Habilita e inicia** o Nginx imediatamente.

### Estados importantes

```
active (running)    → serviço funcionando
inactive            → serviço parado
failed              → serviço apresentou erro
activating          → serviço está iniciando
deactivating        → serviço está parando
```

### systemd × systemctl

- **systemd** → sistema que gerencia a inicialização e os serviços.
- **systemctl** → comando utilizado para controlar o `systemd`.

**Resumo:**

> `systemctl` = **gerenciar serviços do Linux**: iniciar, parar, reiniciar, consultar e configurar inicialização automática.