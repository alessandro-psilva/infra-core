# Infra-Core: Plataforma de Provisionamento Terraform

Bem-vindo ao **Infra-Core**, o repositório central para provisionamento e gerenciamento de infraestrutura como código (IaC).

Este projeto utiliza uma arquitetura de monorepo com um motor Terraform genérico. O objetivo é permitir que múltiplos projetos e departamentos provisionem recursos de forma padronizada, segura e escalável.

## Princípio da Arquitetura

A plataforma opera com uma clara separação de responsabilidades:

* **YAML (`projects/*.yaml`): O MANIFESTO**
    * Responde: "O que é este projeto, quais módulos ele usa e em quais ambientes ele pode ser implantado?" É uma declaração de intenções de alto nível.

* **TFVARS (`environments/*.tfvars`): OS PARÂMETROS**
    * Responde: "Como os módulos de um projeto devem ser configurados em um ambiente específico (dev, prod, etc.)?" Contém os detalhes e valores de cada variável.

* **Makefile: O PAINEL DE CONTROLE**
    * Responde: "Qual ação (plan, apply) quero executar, para qual projeto (manifesto) e com qual configuração (parâmetros de ambiente)?"

## Estrutura de Pastas

```
.
├── Makefile          # O Painel de Controle
├── main.tf           # O Motor Terraform
├── ...
│
├── environments/
│   ├── dv.tfvars     # Parâmetros para Desenvolvimento
│   └── ...
│
├── modules/
│   ├── modulo_a/
│   └── ...
│
└── projects/
    ├── aaaa.yaml     # Manifesto do Projeto AAAA
    └── bbbb.yaml     # Manifesto do Projeto BBBB
```

## Pré-requisitos

* [Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Make](https://www.gnu.org/software/make/)

## Como Usar

Todos os comandos são executados da **raiz do repositório** via `Makefile`, que requer 2 variáveis principais:

* `PROJECT_ID`: O identificador único do projeto (o nome do arquivo YAML sem a extensão).
* `ENV`: O ambiente (`dv`, `ho`, ou `pr`) no qual a ação será executada.

### Comandos Principais

```bash
# Planejar as mudanças para o projeto 'aaaa' no ambiente de dev
make plan PROJECT_ID=aaaa ENV=dv

# Aplicar as mudanças para o projeto 'bbbb' no ambiente de prod
make apply PROJECT_ID=bbbb ENV=pr
```

### Adicionando um Novo Projeto

1.  **Crie o Manifesto YAML:** Na pasta `projects/`, crie um novo arquivo (ex: `cccc.yaml`).
2.  **Defina o Manifesto:** Dentro do YAML, adicione as chaves:
    * `department`: "nome do departamento"
    * `project`: "nome do projeto"
    * `resources`: `[ "lista-de-modulos" ]`
    * `environments`: `[ "dv", "pr" ]` (lista de ambientes permitidos)
3.  **Defina os Parâmetros:** Abra cada arquivo `.tfvars` na pasta `environments/` e adicione a configuração de parâmetros para o seu novo departamento/projeto.
4.  **Execute:** Use `make apply` com o novo `PROJECT_ID`.

## Funcionalidades de Segurança

### Validação de Ambientes

O sistema impede automaticamente a implantação de um projeto em um ambiente não autorizado. Se você tentar executar `make apply` para um `ENV` que não está na lista `environments` do arquivo YAML do projeto, o Terraform irá parar com um erro, prevenindo configurações incorretas.

### Gestão de Estado (State Management)

Cada combinação de `PROJECT_ID-AMBIENTE` gera seu próprio arquivo de estado na pasta `terraform-states/` (ex: `aaaa-pr.tfstate`), garantindo isolamento máximo entre todas as implantações.
