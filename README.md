# Documentação — Informática para Internet

**Plataforma educacional do Curso Técnico em Informática do IFC Campus Araquari**

Site publicado: [informatica-para-internet.vercel.app](https://informatica-para-internet.vercel.app)
Repositório: [github.com/Monitoria-IFC-Araquari/informatica_para_internet](https://github.com/informatica-para-internet-ifc)

---

## Sobre o Projeto

O **Informática para Internet** é uma plataforma web que centraliza atividades, listas, projetos e materiais do Curso Técnico em Informática. A interface é construída com **Vue 3 + Vite**, utiliza **Pinia** para gerenciamento de estado e **Vue Router** para navegação hierárquica. O backend é desenvolvido em **Python com Django + Django REST Framework** e hospedado no **Fabroku**.

O acesso aos conteúdos é **público** — qualquer visitante pode navegar sem criar conta. Professores possuem uma **área administrativa** (login por e-mail/senha) para criar, editar, duplicar e excluir atividades.

A organização conceitual é:

```
Home (lista de anos) → Ano → Disciplina → Atividade → Conteúdo (blocos)
```

---

## Índice da Documentação

### [Introdução](introducao/README.md)
Visão geral do projeto, proposta, público-alvo e estrutura geral.

### [Guia do Usuário](guia-do-usuario/README.md)
Fluxos de uso para visitantes (consulta pública) e professores (área administrativa).

### [Arquitetura do Sistema](arquitetura/README.md)
Estrutura do código Vue 3 e do backend Django, fluxo de dados, rotas e endpoints.

### [API e Camada de Dados](api/README.md)
Backend Django REST Framework: autenticação, catálogo, upload de mídia e exportação/importação.

### [Design e UX](design-ux/README.md)
Identidade visual, variáveis CSS, tema claro/escuro, animações e responsividade.

### [Requisitos Funcionais](requisitos-funcionais/README.md)
Funcionalidades do sistema: sistema de blocos, editor de atividades, autenticação, busca e rotas.

### [Requisitos Não Funcionais](requisitos-nao-funcionais/README.md)
Performance, acessibilidade, compatibilidade, responsividade e segurança.

### [Regras de Negócio](regras-de-negocio/README.md)
Políticas de acesso, validações, fluxos de criação/edição e upload de arquivos.

### [Modelagem do Banco](modelagem-banco/README.md)
Modelo de dados com Django Models, migrations e consultas.

### [Equipe](equipe/README.md)
Pessoas envolvidas no projeto.

---

## Stack e Ferramentas

| Tecnologia | Uso |
|------------|-----|
| Vue 3 (`^3.5`) | Framework front-end (Composition API) |
| Vite (`^8`) | Build tool e dev server |
| Vue Router (`^5.2`) | Navegação e rotas |
| Pinia (`^4`) | Gerenciamento de estado (auth, isMobile) |
| @mdi/font, @mdi/js | Ícones |
| ESLint + Oxlint + Prettier | Qualidade e formatação do código front-end |
| Python 3.10+ | Linguagem do backend |
| Django (`>=5.1`) + Django REST Framework | API REST (models, views, admin) |
| djangorestframework-simplejwt | Autenticação por JWT |
| drf-spectacular | Documentação OpenAPI (Swagger/ReDoc) |
| SQLite (dev) / PostgreSQL (produção, via `DATABASE_URL`) | Banco de dados relacional |
| PDM | Gerenciador de dependências e scripts do backend |
| Ruff + pylint | Lint e formatação do backend |
| Cloudinary (opcional) | Armazenamento de mídia em produção — padrão é guardar os arquivos no próprio banco de dados |
| Fabroku | Hospedagem e deploy (backend) |
| Vercel | Hospedagem e deploy (frontend) |

---

## Fluxo Conceitual

```
┌─────────────────────────────────────────────────┐
│                    HOME (/)                      │
│   Lista os anos do curso ("Escolha o seu ano")    │
└──────────────────────┬──────────────────────────┘
                        ▼
┌─────────────────────────────────────────────────┐
│          ANO (/ano/:anoId)                        │
│          Disciplinas daquele ano                  │
└──────────────────────┬──────────────────────────┘
                        ▼
┌─────────────────────────────────────────────────┐
│   DISCIPLINA (/disciplina/:anoId/:disciplinaId)   │
│   Atividades da disciplina, por categoria         │
│   (Questão / Atividade / Tutorial)                │
└──────────────────────┬──────────────────────────┘
                        ▼
┌─────────────────────────────────────────────────┐
│   ATIVIDADE (/atividade/:disciplinaId/            │
│                    :atividadeId)                  │
│   Blocos: Texto, Código, Imagem, Questão...        │
└─────────────────────────────────────────────────┘
```

> A rota `/anos` existe apenas como redirecionamento para `/` — a própria Home já exibe a lista de anos.

---

*Documentação atualizada em: Outubro 2026, a partir da leitura direta do código em `frontend/` e `backend/`.*
