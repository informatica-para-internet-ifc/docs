# Guia do Usuário

## Para Alunos (Consulta Pública)

### 1. Página Inicial

A Home (`/`) já exibe a lista de anos do curso como cards, com atalhos para **Buscar atividades** (`/buscar`) e **Sobre o projeto** (`/sobre`).

### 2. Selecionar o Ano

Clique em um card de ano para ir a `/ano/:anoId` (ex: `/ano/1` para o 1º Ano), que mostra as disciplinas daquele ano.

### 3. Selecionar a Disciplina

`/disciplina/:anoId/:disciplinaId` (ex: `/disciplina/1/logica`) mostra as atividades da disciplina, organizadas em abas por **categoria**: Questão, Atividade ou Tutorial — cada aba mostra a contagem de itens.

### 4. Selecionar a Atividade

Cada card de atividade exibe:
- Número de ordem
- Título e descrição
- Badge **"Fixada"**, quando aplicável
- Contagem de questões

### 5. Visualizar o Conteúdo

`/atividade/:disciplinaId/:atividadeId` abre a atividade com:
- Cabeçalho com breadcrumb (Ano → Disciplina → Atividade)
- Sumário (índice) dos blocos, quando há vários
- Barra de progresso de leitura, que preenche conforme a rolagem
- Os blocos de conteúdo propriamente ditos (veja "Sistema de Blocos" abaixo)
- Botão de impressão, usando estilos dedicados (`print.css`) que ocultam elementos de interface

---

## Para Professores (Área Administrativa)

### 1. Fazer Login

Acesse `/perfil` e entre com e-mail e senha cadastrados (`POST /api/token/` no backend). Não existe conta de demonstração — o login sempre depende da API responder.

### 2. Criar Atividade

Acesse `/criar-atividade`. O editor é dividido em:

**Capa e dados principais** (topo):
| Campo | Obrigatório | Descrição |
|-------|-------------|-----------|
| Capa | Não | Imagem de capa, enviada direto do seu computador |
| Título | Sim | Nome da atividade |
| Descrição | Não | Breve descrição exibida nos cards |

**Painel lateral "Organização / Detalhes / Publicação":**
| Campo | Obrigatório | Descrição |
|-------|-------------|-----------|
| Categoria | Não (padrão: Atividade) | Questão, Atividade ou Tutorial |
| Ano | Sim | Ano do curso (1º, 2º ou 3º) |
| Disciplina | Sim | Disciplina do ano selecionado |
| Dificuldade | Não | Fácil / Médio / Difícil |
| Tempo estimado | Não | Texto livre, ex: "45 min" |
| Tags | Não | Digite e pressione Enter |
| Pré-requisitos | Não | Texto livre |
| Status | Não (padrão: Rascunho) | Rascunho / Publicada / Arquivada |
| Prazo recomendado | Não | Data |
| Fixar atividade | Não | Só habilitado quando o status é "Publicada" |

> Hoje, mudar o status para "Publicada" ou "Arquivada" **não** controla quem vê a atividade — ela aparece normalmente para qualquer visitante independentemente do status escolhido (é um metadado ainda sem efeito na listagem pública).

### 3. Montar o Conteúdo em Blocos

Clique em **"Adicionar bloco"** e escolha entre os 21 tipos disponíveis (veja "Sistema de Blocos"). Cada bloco pode ser:
- Reordenado (**↑ / ↓** ou arrastando e soltando)
- Duplicado
- Excluído
- Colapsado/expandido individualmente, ou todos de uma vez

Blocos de Imagem, Galeria, Vídeo e Download têm um botão de **upload direto** — o arquivo é enviado ao servidor e a URL é preenchida automaticamente; também é possível colar uma URL manualmente em vez de enviar um arquivo.

### 4. Visualizar e Salvar

- Alterne entre **Editar ↔ Visualizar** (botão no topo do editor)
- Ao criar uma atividade **nova**, o conteúdo é salvo automaticamente como rascunho no navegador (`localStorage`) enquanto você edita — ao reabrir o editor, um aviso oferece restaurar ou descartar esse rascunho. Isso não se aplica quando você está editando uma atividade já existente.
- Atalho **Ctrl+S / Cmd+S** salva a atividade
- Ao salvar, a atividade é validada: título, ano, disciplina e **pelo menos 1 bloco preenchido** são obrigatórios
- Se você tentar sair com alterações não salvas, o sistema pede confirmação
- Depois de salva, a atividade aparece na página da disciplina e pode ser **editada**, **duplicada** ou **excluída** por quem está logado, direto na lista

---

## Sistema de Blocos

### Blocos de Conteúdo (21 tipos)

| Tipo | Descrição |
|------|-----------|
| Texto | Parágrafo explicativo |
| Título | Subtítulo para organizar seções |
| Markdown | Conteúdo formatado em Markdown |
| Código | Exemplos de programação formatados |
| Terminal | Comandos de terminal separados de código-fonte (`npm install`, `git clone`, etc.) |
| Imagem | Upload direto ou URL, com texto alternativo |
| Galeria | Múltiplas imagens, cada uma com upload próprio e legenda |
| Vídeo | Upload de arquivo de vídeo, ou link do YouTube/Vimeo incorporado |
| Embed | Iframe incorporado (CodePen, JSFiddle, etc.) |
| Lista | Itens ordenados ou não ordenados |
| Passo a Passo | Sequência numerada: 01 → 02 → 03 → 04 |
| Checklista | Lista de requisitos com checkbox: ☐ Item 1, ☐ Item 2 |
| Tabela | Tabela editável com linhas e colunas |
| Citação | Citação em destaque com autor |
| Aviso | Bloco colorido com tipos: Informação, Dica, Atenção, Importante, Erro comum |
| Link | Link único com rótulo e descrição |
| Links Externos | Referências para GitHub, documentação, vídeos, sites |
| Download | Upload direto ou URL de arquivo para baixar (PDF, DOCX, ZIP, etc.), com ícone, nome e tamanho |
| Acordeão | Itens colapsáveis (perguntas e respostas) |
| Divisor | Separador visual entre seções |
| Questão | Exercício com enunciado e alternativas |

### Blocos de Questão Expandidos

| Tipo | Estrutura |
|------|-----------|
| Múltipla Escolha | Alternativas + índice da resposta correta |
| Verdadeiro/Falso | Resposta booleana |
| Discursiva | Apenas enunciado, sem resposta armazenada |
| Programação | Linguagem + código esperado |

---

## Pesquisa

Acesse `/buscar` para pesquisar atividades. O atalho **Ctrl+K** abre a busca rapidamente.

**Filtros de busca:**
- Por título
- Por descrição
- Por enunciados de questão
- Por ano e disciplina

> A busca filtra apenas as atividades já carregadas no navegador (não consulta o endpoint de busca do backend), então os resultados dependem das disciplinas que você já visitou/carregadas pelo app.

---

## Mapa de Rotas

| Rota | Página | Acesso |
|------|--------|--------|
| `/` | Home (lista de anos) | Público |
| `/anos` | *(redireciona para `/`)* | Público |
| `/ano/:anoId` | Disciplinas do ano (ex: `/ano/1`) | Público |
| `/disciplina/:anoId/:disciplinaId` | Atividades da disciplina (ex: `/disciplina/1/logica`) | Público |
| `/atividade/:disciplinaId/:atividadeId` | Conteúdo da atividade em blocos | Público |
| `/buscar` | Busca de atividades (atalho Ctrl+K) | Público |
| `/sobre` | Página "Sobre" (conteúdo ainda placeholder) | Público |
| `/perfil` | Login / painel administrativo | Público (login) |
| `/criar-atividade` | Criar atividade | Autenticado |
| `/editar-atividade/:disciplinaId/:atividadeId` | Editar atividade | Autenticado |
| `/:pathMatch(.*)*` | Página 404 | Público |

> Rotas protegidas (`/criar-atividade` e `/editar-atividade/...`) redirecionam para `/perfil` quando não há sessão ativa, preservando o destino na query `?redirect=...`.
