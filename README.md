# Infra-Core: Plataforma de Provisionamento Terraform

Bem-vindo ao **Infra-Core**, o repositório central para provisionamento e gerenciamento de infraestrutura como código (IaC).

Este projeto utiliza uma arquitetura de monorepo com um motor Terraform genérico, alimentado por arquivos de configuração YAML, permitindo que múltiplos projetos e departamentos sejam gerenciados de forma padronizada e escalável.

## Estrutura de Pastas

```
.
├── Makefile          # Painel de Controle Central
├── main.tf           # Motor Terraform genérico
├── ...
│
├── environments/
│   ├── dv.tfvars
│   └── ...
│
├── modules/
│   ├── modulo_a/
│   └── ...
│
└── projects/
    ├── financeiro-relatorios_anuais.yaml
    └── rh-folha_de_pagamento.yaml
```

## Pré-requisitos

* [Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli), [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git), [Make](https://www.gnu.org/software/make/)

## Como Usar

Todos os comandos são executados da **raiz do repositório** via `Makefile`, que requer 3 variáveis:

* `DEPT`: O nome do departamento (ex: `financeiro`).
* `PROJECT`: O nome do projeto (ex: `relatorios_anuais`).
* `ENV`: O ambiente (`dv`, `ho`, ou `pr`). O padrão é `dv`.

### Comandos Principais

```bash
# Planejar as mudanças
make plan DEPT=financeiro PROJECT=relatorios_anuais ENV=dv

# Aplicar as mudanças
make apply DEPT=rh PROJECT=folha_de_pagamento ENV=pr
```

### Adicionando um Novo Projeto

1.  **Crie o arquivo YAML:** Na pasta `projects/`, crie um novo arquivo seguindo o padrão `DEPARTAMENTO-PROJETO.yaml`.
2.  **Liste os Módulos:** Dentro do YAML, liste os recursos (módulos) que o projeto necessita.
3.  **Defina os Parâmetros:** Abra os arquivos `environments/*.tfvars` e adicione a configuração de parâmetros para o seu novo departamento/projeto.
4.  **Execute:** Use `make apply` com os novos `DEPT` e `PROJECT
