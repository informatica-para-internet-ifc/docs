# Requisitos Funcionais

## RF-01 — Navegação Hierárquica

**Descrição:** O sistema deve permitir navegar de forma hierárquica entre ano, disciplina e atividade.

```
Home (lista de anos) → Ano → Disciplina → Atividade → Conteúdo (Blocos)
```

**Critérios de Aceite:**
- Home (`/`) já exibe a lista de anos — `/anos` existe só como redirecionamento para `/`
- `/ano/:anoId` mostra disciplinas do ano
- `/disciplina/:anoId/:disciplinaId` mostra atividades da disciplina, agrupadas por categoria (abas: Questão / Atividade / Tutorial)
- `/atividade/:disciplinaId/:atividadeId` exibe blocos
- Navegação de volta funciona em todos os níveis

---

## RF-02 — Sistema de Blocos

**Descrição:** Uma atividade deve ser composta por múltiplos blocos de tipos diferentes.

**Tipos de Bloco (21 tipos):**

| Tipo | Campos | Descrição |
|------|--------|-----------|
| Texto | `content` | Parágrafo explicativo |
| Título | `content`, `level` | Subtítulo de seção |
| Markdown | `content` | Conteúdo formatado |
| Código | `content`, `language` | Exemplo de programação |
| Terminal | `commands[]` | Comandos de terminal |
| Imagem | `url`, `alt` | Upload direto ou URL |
| Galeria | `images[{url, caption}]`, `caption` | Múltiplas imagens, cada uma com upload próprio |
| Vídeo | `url`, `title` | Upload de arquivo ou vídeo YouTube/Vimeo incorporado |
| Embed | `url`, `title`, `height` | Iframe incorporado (CodePen, JSFiddle...) |
| Lista | `items[]`, `ordered` | Lista de itens |
| Passo a Passo | `steps[{title, desc}]` | Sequência numerada |
| Checklista | `items[{text, done}]` | Lista de verificação |
| Tabela | `rows[][]`, `hasHeader` | Tabela editável |
| Citação | `content`, `author` | Citação em destaque |
| Aviso | `content`, `tipo` | Bloco colorido (informação, dica, atenção, importante, erro) |
| Link | `url`, `label`, `desc` | Link único com rótulo |
| Links Externos | `links[{title, url, desc}]` | Referências externas |
| **Download** | `label`, `url`, `size`, `desc` | Upload direto ou URL de arquivo para baixar |
| Acordeão | `items[{title, content}]` | Itens colapsáveis (perguntas e respostas) |
| Divisor | — | Separador visual |
| Questão | `enunciado`, `modo`, `alternativas[]`, `correta`, `respostaVf`, `linguagem`, `codigoEsperado` | Exercício (múltipla escolha, V/F, discursiva, programação) |

**Critérios de Aceite:**
- Adicionar qualquer tipo de bloco, em qualquer posição
- Remover, duplicar e reordenar (botões ↑/↓ ou arrastar e soltar)
- Colapsar/expandir blocos individualmente ou todos de uma vez

---

## RF-03 — Bloco de Questão Expandido

**Descrição:** O bloco Questão deve suportar múltiplos tipos de avaliação.

**Tipos de Questão (`modo`):**

| Tipo | Estrutura |
|------|-----------|
| Múltipla Escolha | `enunciado` + `alternativas[{texto}]` + `correta` (índice da resposta) |
| Verdadeiro/Falso | `enunciado` + `respostaVf` (true/false) |
| Discursiva | `enunciado`, sem resposta armazenada |
| Programação | `enunciado` + `linguagem` + `codigoEsperado` |

---

## RF-04 — Bloco de Download

**Descrição:** Anexar arquivos para download (PDF, DOCX, PPTX, XLSX, ZIP, TXT).

**Campos:**
- `label` — Nome de exibição (ex: "Lista 03 — Banco de Dados")
- `url` — URL do arquivo (preenchida automaticamente após upload, ou colada manualmente)
- `size` — Tamanho formatado (ex: "2.4 MB") — preenchido automaticamente após upload
- `desc` — Descrição opcional

**Critérios de Aceite:**
- Enviar o arquivo diretamente pelo editor (`POST /api/media/documents/`), **ou** colar uma URL externa
- Tipo do arquivo validado pelo conteúdo real (não pela extensão)

---

## RF-05 — Bloco de Links Externos

**Campos:** `links[]` — array de `{title, url, desc?}`

---

## RF-06 — Bloco de Vídeo

**Descrição:** Incorporar vídeo do YouTube/Vimeo ou enviar um arquivo de vídeo.

**Campos:** `url`, `title`

**Critérios de Aceite:**
- Upload direto de arquivo (MP4/WEBM/MOV/AVI/MKV) **ou** link do YouTube/Vimeo
- Player embutido (iframe `youtube-nocookie`/Vimeo, ou `<video>` para arquivo enviado)
- Responsivo

---

## RF-07 — Bloco de Galeria

**Campos:** `images[]` — array de `{url, caption}`; `caption` — legenda do conjunto. Cada imagem tem upload próprio.

---

## RF-08 — Bloco de Aviso

**Tipos (`tipo`):**

| Tipo | Cor |
|------|-----|
| `info` (Informação) | Azul |
| `success` (Dica) | Verde |
| `warning` (Atenção) | Amarelo |
| `danger` (Importante) | Vermelho |
| `erro` (Erro comum) | Roxo |

**Campos:** `tipo`, `content`

---

## RF-09 — Bloco de Terminal

**Campos:** `commands[]` — lista de comandos (ex: `npm install`, `git clone`)

---

## RF-10 — Bloco de Tabela

**Campos:** `rows[][]` — dados das células; `hasHeader` — define se a primeira linha é cabeçalho

---

## RF-11 — Bloco de Passo a Passo

**Campos:** `steps[]` — array de `{title, desc?}`

---

## RF-12 — Bloco de Checklista

**Campos:** `items[]` — array de `{text, done}`

---

## RF-13 — Editor de Atividades

**Descrição:** Editor visual completo para criar e editar atividades (`CreateActivityView.vue`).

**Implementado:**
- Criar (`/criar-atividade`) e editar (`/editar-atividade/:disciplinaId/:atividadeId`)
- Adicionar bloco em qualquer posição, remover, duplicar e reordenar (botões ↑/↓ ou arrastar e soltar)
- Modo preview (**Editar ↔ Visualizar**)
- Painel lateral com todos os metadados: categoria, ano, disciplina, dificuldade, tempo estimado, tags, pré-requisitos, status, prazo recomendado, fixar atividade
- Capa da atividade (upload de imagem)
- Rascunho automático no `localStorage` ao criar uma atividade nova (debounce de 600ms)
- Atalho **Ctrl+S / Cmd+S** para salvar
- Aviso de confirmação ao sair com alterações não salvas (`beforeunload` + guard de rota)
- Duplicar atividade inteira (a partir da lista de atividades da disciplina)

**Implementado na API, sem interface ainda:**
- Exportar atividade como Markdown (`GET /api/atividades/:id/exportar/?formato=markdown`) — PDF retorna `501 Not Implemented`
- Importar Markdown e gerar blocos automaticamente (`POST /api/atividades/importar/`)

**Não implementado:**
- Templates pré-definidos ao criar uma atividade nova
- Atalhos de teclado Ctrl+Z / Ctrl+Shift+Z (desfazer/refazer) e "/" (menu de blocos)
- Preview responsivo (alternar Celular/Desktop) separado do layout padrão

---

## RF-14 — Templates de Atividade *(não implementado)*

**Descrição:** Roadmap — ao criar uma atividade, o professor poderá escolher um template pré-definido.

| Template | Blocos Iniciais |
|----------|-----------------|
| Em branco | Nenhum bloco |
| Lista de exercícios | Título + Questões |
| Aula prática | Título + Texto + Código + Exercício |
| Trabalho | Título + Texto + Lista de tarefas |
| Tutorial | Título + Passo a passo + Código |
| Projeto | Título + Texto + Checklist + Requisitos |

---

## RF-15 — Metadados da Atividade

**Descrição:** O modelo `Atividade` possui os campos abaixo, todos editáveis pela interface, exceto `data_publicacao`.

| Campo | Tipo | Obrigatório | Editável na UI |
|-------|------|-------------|----------------|
| Título | string | Sim | Sim |
| Descrição | string | Não | Sim |
| Capa | URL (upload) | Não | Sim |
| Categoria | enum (questao/atividade/tutorial) | Não (padrão: atividade) | Sim |
| Ano | FK | Sim | Sim |
| Disciplina | FK | Sim | Sim |
| Dificuldade | enum (facil/medio/dificil) | Não | Sim |
| Tempo estimado | string (ex: "30 min") | Não | Sim |
| Tags | string[] (JSON) | Não | Sim |
| Pré-requisitos | string | Não | Sim |
| Data de publicação | datetime | Não | **Não** (sem campo no editor e sem efeito no sistema) |
| Prazo recomendado | date | Não | Sim |
| Fixar atividade | boolean | Não | Sim (só habilitado quando status = Publicada) |
| Status | enum (rascunho/publicada/arquivada) | Automático (default: rascunho) | Sim |

---

## RF-16 — Status das Atividades

**Descrição:** O modelo `Atividade` possui um campo `status` e a API aceita o filtro `?status=` em `/api/atividades/`. **Esse filtro não é aplicado por padrão**: a listagem por disciplina (`DisciplinaView.vue`) busca `GET /api/atividades/?disciplina=<slug>` sem informar `status`, então atividades em qualquer status (inclusive rascunho) aparecem normalmente para qualquer visitante hoje.

| Status | Comportamento pretendido (alunos) | Comportamento atual |
|--------|-----------|----------------------|
| Rascunho | Invisível | Visível para todos (sem filtro de status na UI) |
| Publicada | Visível | Visível |
| Arquivada | Invisível | Visível para todos |

---

## RF-17 — Fixar Atividade

**Descrição:** Professor pode marcar atividade como destaque.

**Implementado ponta a ponta:**
- Campo `fixada` (boolean) em `Atividade`, com controle no editor (checkbox, só habilitado quando status = Publicada)
- Validação no serializer: só pode ser `true` quando `status = publicada`
- `Atividade.Meta.ordering = ['-fixada', '-atualizado_em']` — fixadas vêm primeiro em qualquer listagem
- Badge visual "Fixada" na lista de atividades da disciplina (`DisciplinaView.vue`)

---

## RF-18 — Duplicar Atividade

**Descrição:** Professor pode duplicar uma atividade inteira. Implementado ponta a ponta (`POST /api/atividades/:id/duplicar/` + botão na lista de atividades da disciplina).

**Critérios:**
- Cópia inclui todos os blocos e metadados
- Novo título: "Cópia de [título original]"
- Status inicial: Rascunho
- Autor da cópia: usuário autenticado que duplicou (não o autor original)

---

## RF-19 — Excluir Atividade

**Descrição:** Professor pode excluir uma atividade, com modal de confirmação, tanto na lista da disciplina quanto na própria página da atividade.

---

## RF-20 — Autenticação

**Descrição:** Login via JWT com backend Django.

**Critérios:**
- Store Pinia (`src/stores/auth.js`) controla a sessão (access token + refresh token + perfil)
- Sessão salva no `localStorage` (chave `sio-user`)
- Login com e-mail + senha via `POST /api/token/`, seguido de `GET /api/usuarios/me/` para carregar o nome do usuário
- Logout limpa a sessão do `localStorage` e do estado da store
- **Não implementado:** renovação automática do access token via `POST /api/token/refresh/` (o refresh token é salvo, mas não usado); não existe conta de demonstração nem fallback para sessão local simulada

---

## RF-21 — Proteção de Rotas

**Descrição:** Rotas administrativas exigem autenticação.

**Rotas protegidas:**
- `/criar-atividade`
- `/editar-atividade/:disciplinaId/:atividadeId`

**Critérios:**
- Route guard (`router.beforeEach`) verifica sessão na store Pinia
- Não autenticado → `/perfil` (preserva rota na query `?redirect=...`)

---

## RF-22 — Busca

**Descrição:** Buscar atividades por palavra-chave, ano e disciplina.

**Rota:** `/buscar` (atalho **Ctrl+K**)

**Implementação atual:** `SearchView.vue` filtra o cache local em memória (`searchAtividades`, em `src/data/disciplinas.js`), **sem** chamar a API. O backend já expõe `GET /api/busca/` com filtros adicionais (dificuldade), mas esse endpoint ainda não está integrado ao front-end.

**Filtros:**
- Texto (título, descrição, enunciados de questão)
- Ano
- Disciplina
- *(roadmap)* Tags, dificuldade, status

---

## RF-23 — Tema Claro/Escuro

**Descrição:** Alternar entre tema claro e escuro, com persistência da preferência (`useTheme.js`, `localStorage`).

---

## RF-24 — Responsividade

**Descrição:** Interface adaptada para mobile, tablet e desktop (ver [design-ux/README.md](../design-ux/README.md)).

---

## RF-25 — Animações

**Descrição:** Transições e animações de entrada de elementos (diretiva `v-reveal`), respeitando `prefers-reduced-motion`.

---

## RF-26 — Página 404

**Descrição:** Rota coringa (`/:pathMatch(.*)*`) exibe uma página 404 dedicada.

---

## RF-27 — Página Sobre

**Descrição:** Rota `/sobre` (`SobreView.vue`) reservada para informações institucionais do projeto. **Status atual: placeholder** — a view só renderiza um `<h1>Sobre</h1>`, sem conteúdo.

---

## Matriz de Prioridade

| RF | Alta | Média | Baixa |
|----|------|-------|-------|
| RF-01 Navegação | X | | |
| RF-02 Blocos (21 tipos) | X | | |
| RF-03 Questões Expandidas | X | | |
| RF-04 Download | X | | |
| RF-05 Links Externos | | X | |
| RF-06 Vídeo | | X | |
| RF-07 Galeria | | X | |
| RF-08 Aviso | | X | |
| RF-09 Terminal | | X | |
| RF-10 Tabela | | X | |
| RF-11 Passo a Passo | | X | |
| RF-12 Checklista | | X | |
| RF-13 Editor de Atividades | X | | |
| RF-14 Templates | | | X |
| RF-15 Metadados | X | | |
| RF-16 Status | X | | |
| RF-17 Fixar Atividade | | X | |
| RF-18 Duplicar Atividade | | X | |
| RF-19 Excluir Atividade | X | | |
| RF-20 Autenticação | X | | |
| RF-21 Proteção de Rotas | X | | |
| RF-22 Busca | X | | |
| RF-23 Tema Claro/Escuro | | X | |
| RF-24 Responsividade | X | | |
| RF-25 Animações | | | X |
| RF-26 Página 404 | | | X |
| RF-27 Página Sobre | | | X |
