# Documentação — Informática para Internet

**Plataforma educacional do Curso Técnico em Informática do IFC Campus Araquari**

Site publicado: [informatica-para-internet.vercel.app]()
Repositório: [github.com/Pauloartur-23/informatica_para_internet]()

---

## Sobre o Projeto

O **Informática para Internet** é uma plataforma web que centraliza atividades, listas, projetos e materiais do Curso Técnico em Informática. A interface é construída com **Vue 3 + Vite**, utiliza **Pinia** para gerenciamento de estado e **Vue Router** para navegação hierárquica. O backend é desenvolvido em **Python com Django** e hospedagem no **Fabroku**.

O acesso aos conteúdos é **público** — qualquer aluno pode navegar sem criar conta. Professores possuem uma **área administrativa** com autenticação para criar e editar atividades.

A organização conceitual é:

```
Home → Ano → Disciplina → Atividade → Conteúdo
```

---

## Índice da Documentação

### [Introdução](introducao/README.md)
Visão geral do projeto, proposta, público-alvo e estrutura geral.

### [Guia do Usuário](guia-do-usuario/README.md)
Fluxos de uso para alunos (consulta pública) e professores (área administrativa).

### [Arquitetura do Sistema](arquitetura/README.md)
Estrutura do código Vue 3, componentes, stores, rotas e organização do `src/`.

### [API e Camada de Dados](api/README.md)
Backend Django, endpoints da API REST, integração com o front-end e estrutura da API.

### [Design e UX](design-ux/README.md)
Identidade visual, variáveis CSS, tema claro/escuro, animações e responsividade.

### [Requisitos Funcionais](requisitos-funcionais/README.md)
Funcionalidades do sistema: sistema de blocos, editor de atividades, autenticação, busca e rotas.

### [Requisitos Não Funcionais](requisitos-nao-funcionais/README.md)
Performance, acessibilidade, compatibilidade, responsividade e escalabilidade.

### [Regras de Negócio](regras-de-negocio/README.md)
Políticas de acesso, validações, fluxos de criação/edição e proteção de rotas.

### [Modelagem do Banco](modelagem-banco/README.md)
Modelo de dados com Django Models (PostgreSQL), migrate e consultas.

### [Equipe](equipe/README.md)
Pessoas envolvidas no projeto.

---

## Stacks e Ferramentas

| Tecnologia | Uso |
|------------|-----|
| Vue 3 | Framework front-end (Composition API) |
| Vite | Build tool e dev server |
| Vue Router 5 | Navegação e rotas |
| Pinia 3 | Gerenciamento de estado |
| @mdi (Material Design Icons) | Ícones |
| ESLint + Oxlint + Prettier | Qualidade e formatação do código |
| Python 3 | Linguagem do backend |
| Django | Framework backend (models, views, admin) |
| PostgreSQL | Banco de dados relacional |
| Django REST Framework | API REST (planejado) |
| Fabroku | Hospedagem e deploy (backend) |
| Vercel | Hospedagem e deploy (frontend) |

---

## Fluxo Conceitual

```
┌─────────────────────────────────────────────────┐
│                    HOME                         │
│          Landing page de entrada                │
└──────────────────────┬──────────────────────────┘
                       │ Começar Agora
                       ▼
┌─────────────────────────────────────────────────┐
│              ANOS (/)                       │
│       Selecionar ano do curso                   │
└──────────────────────┬──────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────┐
│         DISCIPLINAS (//:)               │
│     Disciplinas daquele ano                     │
└──────────────────────┬──────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────┐
│       ATIVIDADES (//:/:)       │
│    Lista de atividades da disciplina            │
└──────────────────────┬──────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────┐
│      CONTEÚDO (//:/:)    │
│   Blocos: Texto, Código, Imagem, Questão...     │
└─────────────────────────────────────────────────┘
```

---

*Documentação atualizada em: Julho 2026*
