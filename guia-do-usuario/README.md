# Guia do Usuário

## Visão Geral

A plataforma **Informática para Internet** possui dois modos de uso: **consulta pública** (alunos) e **área administrativa** (professores). Este guia explica os dois fluxos e todas as funcionalidades disponíveis.

---

## Fluxo do Aluno (Acesso Público)

O aluno não precisa criar conta para acessar os materiais.

### 1. Acessar a Home

Acesse [informatica-para-internet.vercel.app](https://informatica-para-internet.vercel.app)

Clique em **"Começar Agora"**.

### 2. Selecionar o Ano

A página `/` mostra os anos do curso como cards com metadados (disciplinas, atividades).

### 3. Selecionar a Disciplina

`/` exibe as disciplinas daquele ano.

### 4. Selecionar a Atividade

`/` mostra as atividades.

Cada atividade pode exibir:
- **Badge de dificuldade**: Fácil, Médio ou Difícil
- **Tempo estimado**: ex: "30 min"
- **Tags**: ex: Vue, JavaScript, Git
- **Status**: Publicada ou Fixada (destaque)
- **Prazo recomendado**: ex: "14/08/2026"

### 5. Visualizar o Conteúdo

A atividade abre com seus blocos de conteúdo. Veja a seção "Sistema de Blocos" abaixo para todos os tipos disponíveis.

---

## Fluxo do Professor (Área Administrativa)

### 1. Fazer Login

Acesse `/` e faça login com email e senha.

### 2. Criar Atividade

Acesse `/`. Primeiro, selecione um **template**:

| Template | Bloco Inicial |
|----------|---------------|
| Em branco | Nenhum bloco |
| Lista de exercícios | Título + Questões |
| Aula prática | Título + Texto + Código + Exercício |
| Trabalho | Título + Texto + Lista de tarefas |
| Tutorial | Título + Passo a passo + Código |
| Projeto | Título + Texto + Checklist + Requisitos |

### 3. Preencher Dados Gerais

| Campo | Obrigatório | Descrição |
|-------|-------------|-----------|
| Título | Sim | Nome da atividade |
| Descrição | Não | Breve descrição |
| Ano | Sim | Ano do curso |
| Disciplina | Sim | Disciplina do ano |
| Dificuldade | Não | Fácil / Médio / Difícil |
| Tempo estimado | Não | ex: "30 min", "1 hora" |
| Tags | Não | Vue, JavaScript, Git, etc. |
| Pré-requisitos | Não | "Recomenda-se concluir X antes" |
| Data de publicação | Não | Programar publicação futura |
| Prazo recomendado | Não | Data limite sugerida |
| Fixar atividade | Não | Aparece primeiro na disciplina |

### 4. Montar o Conteúdo em Blocos

Clique em **"Adicionar Bloco"** e escolha o tipo.

### 5. Visualizar e Salvar

- Alterne entre **Editar ↔ Visualizar**
- No preview, teste em **Celular** ou **Desktop**
- Salve quando estiver pronto

---

## Sistema de Blocos

### Blocos de Conteúdo (existentes)

| Tipo | Descrição |
|------|-----------|
| Texto | Parágrafo explicativo ou enunciado |
| Título | Subtítulo para organizar seções |
| Markdown | Conteúdo formatado em Markdown |
| Código | Exemplos de programação formatados |
| Imagem | Imagem por URL com legenda |
| Lista | Itens ordenados ou não ordenados |
| Questão | Exercício com enunciado e alternativas |
| Divisor | Separador visual entre seções |
| Download | Arquivo para baixar (PDF, DOCX, ZIP, etc.) com ícone, nome e tamanho |
| Links Externos | Referências para GitHub, documentação, vídeos, sites |
| Vídeo | Vídeo do YouTube incorporado diretamente na atividade |
| Aviso | Bloco colorido com tipos: Informação, Dica, Atenção, Importante, Erro comum |
| Terminal | Comandos de terminal separados de código-fonte (npm install, git clone, etc.) |
| Tabela | Tabela editável com linhas e colunas |
| Passo a Passo | Sequência numerada: 01 → 02 → 03 → 04 |
| Checklista | Lista de requisitos com checkbox: ☐ Item 1, ☐ Item 2 |

### Blocos de Questão Expandidos

| Tipo | Descrição |
|------|-----------|
| Múltipla Escolha | Uma resposta correta entre alternativas |
| Verdadeiro/Falso | Afirmação para julgar |
| Discursiva | Resposta aberta em texto |
| Programação | Código como resposta (com validação futura) |


---

## Pesquisa

Acesse `/` para pesquisar atividades.

**Filtros de busca:**
- Por título
- Por tags
- Por dificuldade
- Por disciplina
- Por ano

---

## Páginas Disponíveis

| Rota | Página | Acesso |
|------|--------|--------|
| `/` | Home | Público |
| `/*` | Página 404 | Público |
