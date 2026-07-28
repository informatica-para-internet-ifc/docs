# Introdução

## O que é o Projeto

O **Informática para Internet** é uma plataforma web educacional desenvolvida para o **Curso Técnico em Informática do IFC Campus Araquari**. Ela centraliza todas as atividades, listas, projetos e materiais utilizados durante os três anos do curso em um único ambiente digital.

A plataforma se divide em duas áreas:

- **Consulta pública** — qualquer aluno acessa os materiais sem precisar criar conta
- **Área administrativa** — professores autenticados criam e editam atividades

## Proposta

O curso de Técnico em Informática gera uma grande quantidade de materiais distribuídos em três anos, múltiplas disciplinas e diversos tipos de atividade. Sem uma plataforma centralizada, esses materiais ficam espalhados pelo sigaa, classroom, sites pessoais e github.

O projeto resolve isso oferecendo uma **biblioteca acadêmica estruturada** com navegação hierárquica:

```
HOME
  ↓
ANO (1º, 2º, 3º)
  ↓
DISCIPLINA (ex: Programação, Desenvolvimento Web)
  ↓
ATIVIDADE (ex: Lista de Exercícios, Projeto Prático)
  ↓
CONTEÚDO (blocos de texto, código, imagens, questões, tutoriais)
```

## Público-Alvo

| Perfil | Uso |
|--------|-----|
| **Alunos do curso** | Consultar atividades, listas e materiais de estudo |
| **Professores** | Criar, editar e organizar conteúdos das disciplinas |
| **Monitores** | Apoio pedagógico e auxílio na organização dos materiais |


## Stack Tecnológica

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

## Tecnologias Puras

- **HTML5** → Estrutura semântica
- **CSS3** → Estilos com variáveis CSS (temas, responsividade)
- **JavaScript (ES6+)** → Lógica da aplicação
