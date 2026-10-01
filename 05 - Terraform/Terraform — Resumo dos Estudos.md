

## 1. O que é o Terraform?

O Terraform é uma ferramenta de **Infrastructure as Code (IaC)** que permite criar e administrar infraestrutura por meio de arquivos de configuração.

Em vez de criar manualmente uma VPC, subnet, Internet Gateway ou servidor pela interface da AWS, descrevemos o que queremos nos arquivos `.tf` e o Terraform transforma essa configuração em recursos reais.

O fluxo básico é:

```text
Código Terraform
      ↓
terraform init
      ↓
terraform plan
      ↓
terraform apply
      ↓
Infraestrutura na AWS
```

---

# 2. Variáveis (`variable`)

As variáveis permitem deixar o código Terraform **flexível e reutilizável**.

Por exemplo, em vez de colocar diretamente:

```hcl
cidr_block = "10.0.0.0/16"
```

podemos criar:

```hcl
variable "vpc_cidr" {
  type    = string
  default = "10.0.0.0/16"
}
```

E utilizar:

```hcl
cidr_block = var.vpc_cidr
```

### Intuição

A variável funciona como um **campo que podemos preencher**.

É parecido com uma função:

```text
função(valor)
```

No Terraform:

```text
var.vpc_cidr
```

significa:

> "Pegue o valor que foi definido para a variável `vpc_cidr`."

---

# 3. `var.nome` e `module.nome`

Uma dúvida importante foi entender a diferença entre:

```hcl
var.vpc_cidr
```

e:

```hcl
module.vpc.vpc_id
```

A diferença é:

### `var`

Usamos para acessar **variáveis**:

```hcl
var.vpc_cidr
var.region
var.environment
```

### `module`

Usamos para acessar **informações disponibilizadas por um módulo**:

```hcl
module.vpc.vpc_id
module.vpc.public_subnet_id
```

Uma forma simples de lembrar:

```text
var    → entrada/configuração
module → resultado produzido por um módulo
```

---

# 4. Módulos

Módulos servem para **organizar e reutilizar código Terraform**.

Por exemplo, podemos criar:

```text
modules/
├── vpc/
├── subnet/
├── security_group/
└── ec2/
```

Cada diretório pode representar uma parte da infraestrutura.

No nosso projeto, por exemplo:

```text
Desaster_Recovery/
│
├── main.tf
├── variables.tf
├── outputs.tf
│
└── modules/
    └── vpc/
        ├── vpc.tf
        ├── variables.tf
        └── outputs.tf
```

O módulo `vpc` fica responsável pela criação da VPC e seus recursos relacionados.

---

# 5. Como chamar um módulo

No arquivo principal podemos fazer:

```hcl
module "vpc" {
  source = "./modules/vpc"

  vpc_cidr = var.vpc_cidr
}
```

Aqui:

```hcl
module "vpc"
```

é o **nome que demos ao módulo**.

Já:

```hcl
source = "./modules/vpc"
```

informa ao Terraform **onde está o código do módulo**.

### Intuição

É como chamar uma função:

```text
criar_vpc(...)
```

O módulo recebe algumas informações, executa sua lógica e pode devolver resultados.

---

# 6. `source`

O `source` indica a origem do módulo.

Exemplo:

```hcl
source = "./modules/vpc"
```

Significa:

> "O código desse módulo está neste diretório."

Também existem módulos externos, por exemplo, provenientes do Terraform Registry ou Git.

Portanto, `source` não é o nome da variável e nem o resultado do módulo.

Ele simplesmente informa **de onde vem o módulo**.

---

# 7. Outputs

Os `outputs` servem para **expor informações geradas pelo Terraform**.

Por exemplo, dentro do módulo VPC podemos ter:

```hcl
output "vpc_id" {
  value = aws_vpc.this.id
}
```

Isso significa:

> "Depois que a VPC for criada, disponibilize o ID dela como `vpc_id`."

No módulo principal podemos acessar:

```hcl
module.vpc.vpc_id
```

### Intuição

Pense em um módulo como uma função:

```text
Módulo VPC
   ↓
cria VPC
   ↓
retorna VPC ID
```

O `output` é justamente uma forma de **retornar uma informação para quem chamou o módulo**.

---

# 8. Relação entre Variable → Module → Output

Essa foi uma das partes mais importantes para entender.

Podemos imaginar:

```text
                 ENTRADA
                    ↓
              var.vpc_cidr
                    ↓
              ┌───────────┐
              │  Módulo   │
              │    VPC    │
              └───────────┘
                    ↓
                 OUTPUT
                    ↓
              module.vpc.vpc_id
```

Ou seja:

```text
Variable
   ↓
entra no módulo
   ↓
módulo cria recursos
   ↓
Output
   ↓
outros recursos podem utilizar o resultado
```

Exemplo:

```hcl
module "vpc" {
  source   = "./modules/vpc"
  vpc_cidr = var.vpc_cidr
}
```

O módulo cria a VPC e disponibiliza:

```hcl
output "vpc_id" {
  value = aws_vpc.this.id
}
```

Depois podemos utilizar:

```hcl
module.vpc.vpc_id
```

---

# 9. Uma dúvida importante: variável dentro do módulo

Uma variável criada no arquivo principal não fica automaticamente disponível dentro do módulo.

Por exemplo:

```hcl
# root variables.tf

variable "vpc_cidr" {
  default = "10.0.0.0/16"
}
```

O módulo não deve simplesmente assumir que conhece:

```hcl
var.vpc_cidr
```

Para passar o valor para ele:

```hcl
module "vpc" {
  source   = "./modules/vpc"
  vpc_cidr = var.vpc_cidr
}
```

E o módulo precisa declarar sua própria entrada:

```hcl
# modules/vpc/variables.tf

variable "vpc_cidr" {
  type = string
}
```

Assim temos:

```text
Root
 │
 │ var.vpc_cidr
 ↓
module "vpc"
 │
 │ vpc_cidr = ...
 ↓
Módulo VPC
 │
 │ variable "vpc_cidr"
 ↓
aws_vpc
```

Isso ajuda a manter o módulo independente e reutilizável.

---

# 10. Variável `source` e a dúvida sobre `const`

Durante o projeto apareceu uma configuração semelhante a:

```hcl
variable "module_source_vpc" {
  type    = string
  default = "./modules/vpc"
  const   = true
}
```

A ideia inicial era transformar o caminho do módulo em algo fixo.

Porém, é importante entender que **o `source` de um módulo não funciona como uma variável Terraform comum**.

O Terraform precisa conhecer a origem do módulo durante a inicialização:

```bash
terraform init
```

Por isso, o caminho:

```hcl
source = "./modules/vpc"
```

normalmente deve ser declarado diretamente no bloco do módulo.

A principal lição foi:

> Nem tudo que parece uma configuração do Terraform pode ser transformado em uma variável dinâmica.

---

# 11. `terraform init`

O comando:

```bash
terraform init
```

prepara o projeto.

Ele pode:

- baixar providers;
    
- inicializar módulos;
    
- preparar o backend;
    
- criar a estrutura necessária para o Terraform trabalhar.
    

Por isso, normalmente é o primeiro comando executado em um projeto novo.

---

# 12. `terraform plan`

O:

```bash
terraform plan
```

não cria a infraestrutura.

Ele apenas mostra:

> "Se eu aplicar esse código, estas serão as alterações."

Exemplo:

```text
+ create
```

O `+` significa que o Terraform pretende **criar** o recurso.

Isso permite revisar a infraestrutura antes de realmente modificar a AWS.

---

# 13. `terraform apply`

Depois de verificar o `plan`, podemos executar:

```bash
terraform apply
```

Esse comando efetivamente aplica as alterações.

Fluxo recomendado:

```bash
terraform init
terraform plan
terraform apply
```

---

# 14. `terraform plan -out`

Também vimos:

```bash
terraform plan -out=tfstate
```

O `-out` salva o plano gerado em um arquivo.

Exemplo:

```bash
terraform plan -out=tfplan
```

Depois:

```bash
terraform apply tfplan
```

Isso permite aplicar exatamente o plano que foi analisado anteriormente.

É diferente do `terraform.tfstate`.

### Importante

```text
tfplan
```

→ plano de execução salvo.

```text
terraform.tfstate
```

→ estado atual conhecido pelo Terraform sobre a infraestrutura administrada.

São coisas diferentes.

---

# 15. Terraform State

O Terraform mantém um estado para saber quais recursos estão sendo administrados.

Por exemplo:

```text
Código Terraform
      ↓
Terraform State
      ↓
AWS
```

O state permite ao Terraform comparar:

```text
O que eu quero
      VS
O que existe
```

e determinar quais alterações precisam ser feitas.

---

# 16. Dependências entre recursos

Outra ideia importante foi entender que o Terraform consegue identificar dependências.

Por exemplo:

```text
VPC
 ↓
Subnet
 ↓
EC2
```

A EC2 precisa da subnet.

Se escrevermos:

```hcl
subnet_id = module.vpc.public_subnet_id
```

o Terraform entende que a EC2 depende daquele resultado.

Assim, ele consegue organizar a criação dos recursos.

---

# 17. A ideia principal para guardar

Uma forma simples de memorizar toda a estrutura é:

```text
VARIABLES
   ↓
Fornecem valores
   ↓
MODULES
   ↓
Criam/organizam recursos
   ↓
OUTPUTS
   ↓
Expõem resultados
   ↓
OUTROS RECURSOS
```

Exemplo completo:

```text
var.vpc_cidr
     ↓
module "vpc"
     ↓
aws_vpc
     ↓
output "vpc_id"
     ↓
module.vpc.vpc_id
     ↓
security group / subnet / EC2
```

---

# 18. Estrutura mental do Terraform

A principal evolução no entendimento foi deixar de enxergar o Terraform apenas como uma lista de comandos e começar a enxergá-lo como um **sistema de entrada, processamento e saída**:

```text
                  INPUT
                    │
              Variables
                    │
                    ▼
                 Module
                    │
              Resources
                    │
                    ▼
                  Output
                    │
                    ▼
             Outros módulos
```

### Resumo rápido

|Conceito|Função|
|---|---|
|`variable`|Recebe/configura valores|
|`var.nome`|Acessa uma variável|
|`module`|Organiza e reutiliza infraestrutura|
|`source`|Define de onde vem o módulo|
|`output`|Expõe um resultado|
|`module.nome.output`|Acessa o resultado do módulo|
|`terraform init`|Inicializa o projeto|
|`terraform plan`|Mostra o que será alterado|
|`terraform apply`|Aplica as alterações|
|`terraform.tfstate`|Guarda o estado da infraestrutura|
|`-out`|Salva o plano de execução|

## Conceito-chave

> **Variables entram, Modules processam/criam, Outputs devolvem informações.**

Essa é uma das formas mais simples de entender a comunicação entre as diferentes partes de um projeto Terraform.