# Introdução

## O Problema

O curso de Técnico em Informática gera uma grande quantidade de materiais distribuídos entre Google Drive, grupos de WhatsApp, Classroom e outros meios dispersos, dificultando a consulta e organização por parte dos alunos.

## A Solução

O projeto resolve isso oferecendo uma **biblioteca acadêmica estruturada** com navegação hierárquica:

```
HOME (/) — lista de anos
  ↓
ANO (/ano/:anoId) — 1º, 2º, 3º
  ↓
DISCIPLINA (/disciplina/:anoId/:disciplinaId) — ex: Lógica de Programação, Banco de Dados
  ↓
ATIVIDADE (/atividade/:disciplinaId/:atividadeId) — categorizada como Questão, Atividade ou Tutorial
  ↓
CONTEÚDO (blocos de texto, código, imagens, vídeos, questões...)
```

## Público-Alvo

- **Alunos:** consultam o material livremente, sem necessidade de conta.
- **Professores:** autenticam-se (e-mail/senha) para criar, editar, duplicar e excluir atividades pelo editor de blocos.

## Objetivos do Projeto

1. Centralizar o material didático do curso em um único lugar.
2. Oferecer um editor de atividades flexível, baseado em blocos de conteúdo reordenáveis.
3. Garantir acesso público e rápido, sem barreiras de login para quem só quer consultar.
4. Ser leve, responsivo e acessível em qualquer dispositivo.

## Stack e Ferramentas

| Tecnologia | Uso |
|------------|-----|
| Vue 3 (`^3.5`) | Framework front-end (Composition API) |
| Vite (`^8`) | Build tool e dev server |
| Vue Router (`^5.2`) | Navegação e rotas |
| Pinia (`^4`) | Gerenciamento de estado |
| @mdi/font, @mdi/js | Ícones |
| ESLint + Oxlint + Prettier | Qualidade e formatação do código |
| Python 3.10+ | Linguagem do backend |
| Django (`>=5.1`) + Django REST Framework | Framework backend (models, views, admin, API) |
| SQLite (dev) / PostgreSQL (produção) | Banco de dados relacional |
| Cloudinary (opcional) | Armazenamento de mídia em produção |
| Fabroku | Hospedagem e deploy (backend) |
| Vercel | Hospedagem e deploy (frontend) |

Veja o detalhamento completo em [Arquitetura do Sistema](../arquitetura/README.md).
