# Infra-Core: Plataforma de Provisionamento Terraform

Bem-vindo ao **Infra-Core**, o repositório central para provisionamento e gerenciamento de infraestrutura como código (IaC) da nossa organização.

Este projeto utiliza uma arquitetura de monorepo com um motor Terraform genérico. O objetivo é permitir que múltiplos projetos e departamentos provisionem recursos de forma padronizada, segura e escalável, simplesmente definindo suas necessidades em arquivos YAML.

## Estrutura de Pastas

```
.
├── Makefile          # Painel de Controle Central para todos os comandos
├── main.tf           # Motor Terraform genérico
├── backend.tf        # Configuração de State
├── variables.tf      # Variáveis do Motor
├── .gitignore        # Arquivos a serem ignorados pelo Git
├── README.md         # Esta documentação
│
├── environments/
│   ├── dv.tfvars     # Parâmetros para o ambiente de Desenvolvimento
│   ├── ho.tfvars     # Parâmetros para o ambiente de Homologação
│   └── pr.tfvars     # Parâmetros para o ambiente de Produção
│
├── modules/
│   ├── modulo_a/
│   └── modulo_b/
│
├── projects/
│   └── marketing/
│       └── campanha_x.yaml
│
└── terraform-states/ # (Ignorado pelo .gitignore) Pasta local para os arquivos de estado
```

## Pré-requisitos

* [Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Make](https://www.gnu.org/software/make/)

## Como Usar

Todos os comandos devem ser executados a partir da **raiz do repositório**. A orquestração é feita via `Makefile`, que requer 3 variáveis:

* `DEPT`: O nome do departamento (ex: `marketing`).
* `PROJECT`: O nome do projeto (ex: `campanha_x`).
* `ENV`: O ambiente (`dv`, `ho`, ou `pr`). O padrão é `dv`.

### Comandos Principais

**Planejar as mudanças para um projeto:**
```bash
make plan DEPT=marketing PROJECT=campanha_x ENV=dv
```

**Aplicar as mudanças para um projeto:**
```bash
make apply DEPT=marketing PROJECT=campanha_x ENV=pr
```

**Destruir os recursos de um projeto:**
```bash
make destroy DEPT=marketing PROJECT=campanha_x ENV=pr
```

### Adicionando um Novo Projeto

1.  **Crie o arquivo YAML:** Adicione um novo arquivo de definição na estrutura `projects/DEPARTAMENTO/PROJETO.yaml`.
2.  **Liste os Módulos:** Dentro do YAML, liste os recursos (módulos) que o projeto necessita.
3.  **Defina os Parâmetros:** Abra os arquivos `environments/*.tfvars` e adicione a configuração de parâmetros para o seu novo departamento/projeto.
4.  **Execute:** Use `make apply` com os novos `DEPT` e `PROJECT`.

## Gestão de Estado (State Management)

A estratégia de estado garante isolamento máximo. Cada combinação de `DEPARTAMENTO-PROJETO-AMBIENTE` gera seu próprio arquivo de estado na pasta `terraform-states/` (ex: `marketing-campanha_x-pr.tfstate`). Isso previne qualquer conflito entre implantações.
