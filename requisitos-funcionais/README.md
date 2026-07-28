# Requisitos Funcionais

## Visão Geral

Requisitos Funcionais descrevem **o que o sistema deve fazer**. Baseado na implementação atual e funcionalidades planejadas.

---

## RF-01 — Navegação Hierárquica

**Descrição:** O sistema deve conduzir o usuário progressivamente pela estrutura acadêmica.

**Hierarquia:**
```
Home → Anos → Disciplina → Atividade → Conteúdo (Blocos)
```

**Critérios de Aceite:**
- Home exibe "Começar Agora" → `/`
- `/` lista anos com metadados (disciplinas, atividades)
- `//:` mostra disciplinas do ano
- `//:/:` mostra atividades
- `//:/:` exibe blocos
- Navegação de volta funciona em todos os níveis

---

## RF-02 — Sistema de Blocos de Conteúdo

**Descrição:** Uma atividade deve ser composta por múltiplos blocos de tipos diferentes.

**Tipos de Bloco (18 tipos):**

| Tipo | Campos | Descrição |
|------|--------|-----------|
| Texto | `texto` | Parágrafo explicativo |
| Título | `texto` | Subtítulo de seção |
| Markdown | `conteudo` | Conteúdo formatado |
| Código | `codigo`, `linguagem` | Exemplo de programação |
| Imagem | `url`, `legenda` | Imagem por URL |
| Lista | `itens[]`, `ordenada` | Lista de itens |
| Questão | `enunciado`, `tipo`, `alternativas[]` | Exercício (múltipla escolha, V/F, discursiva, programação) |
| Divisor | — | Separador visual |
| **Download** | `arquivo_url`, `nome`, `tipo_arquivo`, `tamanho` | Arquivo para baixar (PDF, DOCX, ZIP, etc.) |
| **Links Externos** | `links[{titulo, url}]` | Referências externas |
| **Vídeo** | `url`, `titulo` | Vídeo YouTube incorporado |
| **Galeria** | `imagens[{url, legenda}]` | Múltiplas imagens |
| **Aviso** | `tipo`, `texto` | Bloco colorido (informação, dica, atenção, importante, erro) |
| **Terminal** | `comandos[]` | Comandos de terminal |
| **Tabela** | `cabecalho[]`, `linhas[][]` | Tabela editável |
| **Passo a Passo** | `passos[{titulo, descricao}]` | Sequência numerada |
| **Checklista** | `itens[{texto, concluido}]` | Lista de verificação |

**Critérios de Aceite:**
- Adicionar qualquer tipo de bloco
- Reordenar blocos (arrastar e soltar ↑↓)
- Duplicar blocos
- Remover blocos
- Pelo menos um bloco obrigatório para salvar

---

## RF-03 — Questões Expandidas

**Descrição:** O bloco Questão deve suportar múltiplos tipos de avaliação.

**Tipos de Questão:**

| Tipo | Estrutura |
|------|-----------|
| Múltipla Escolha | `enunciado` + `alternativas[]` + `resposta_correta` |
| Verdadeiro/Falso | `enunciado` + `resposta` (true/false) |
| Discursiva | `enunciado` + `espaco_resposta` (texto livre) |
| Programação | `enunciado` + `linguagem` + `codigo_esperado` |

---

## RF-04 — Bloco de Download

**Descrição:** Permitir anexar arquivos para download (PDF, DOCX, PPTX, ZIP, etc.).

**Campos:**
- `arquivo_url` — URL do arquivo
- `nome` — Nome de exibição (ex: "Lista 03 — Banco de Dados")
- `tipo_arquivo` — Extensão (pdf, docx, zip, etc.)
- `tamanho` — Tamanho em bytes

**Exibe visualmente:**
```
Banco de Dados — Lista 03 · PDF · 2.4 MB · [Baixar]
```

---

## RF-05 — Bloco de Links Externos

**Descrição:** Criar bloco para referências externas (GitHub, documentação, vídeos, sites).

**Campos:**
- `links[]` — Array de `{titulo, url, descricao?}`

---

## RF-06 — Bloco de Vídeo

**Descrição:** Incorporar vídeo do YouTube diretamente na atividade.

**Campos:**
- `url` — URL do YouTube
- `titulo` — Título do vídeo

**Critérios de Aceite:**
- Player embutido (iframe)
- Funciona em desktop e mobile
- Responsivo

---

## RF-07 — Bloco de Galeria de Imagens

**Descrição:** Permitir múltiplas imagens com legenda em um único bloco.

**Campos:**
- `imagens[]` — Array de `{url, legenda}`
- `legenda_geral` — Legenda do conjunto

---

## RF-08 — Bloco de Aviso

**Descrição:** Bloco visual com tipos diferentes para chamar atenção.

**Tipos:**

| Tipo | Cor |
|------|-----|
| Informação | Azul | 
| Dica | Verde | 
| Atenção | Amarelo | 
| Importante | Vermelho | 
| Erro comum | Roxo | 

**Campos:**
- `tipo` — Um dos 5 tipos
- `texto` — Conteúdo do aviso

---

## RF-09 — Bloco de Terminal

**Descrição:** Separar comandos de terminal de código-fonte.

**Campos:**
- `comandos[]` — Lista de comandos (ex: `npm install`, `git clone`)

**Exibe visualmente:**
```
$ npm install
$ python manage.py runserver
```

---

## RF-10 — Bloco de Tabela

**Descrição:** Tabela editável com linhas e colunas.

**Campos:**
- `cabecalho[]` — Nomes das colunas
- `linhas[][]` — Dados das linhas

---

## RF-11 — Bloco de Passo a Passo

**Descrição:** Sequência numerada para atividades práticas.

**Campos:**
- `passos[]` — Array de `{titulo, descricao?}`

**Exibe:** 01 → 02 → 03 → 04

---

## RF-12 — Bloco de Checklista

**Descrição:** Lista de requisitos com checkbox.

**Campos:**
- `itens[]` — Array de `{texto, concluido}`

**Exibe:** ☐ Criar projeto · ☐ Implementar router · ☐ Enviar ao GitHub

---

## RF-13 — Editor de Atividades

**Descrição:** Editor visual completo para criar e editar atividades.

**Operações:**
- Criar (`/`)
- Editar (`/:/:`)
- Adicionar/remover/duplicar/reordenar blocos
- Modo preview (Editar ↔ Visualizar)
- Preview responsivo ( Celular / Desktop)
- Importar Markdown
- Exportar como Markdown ou PDF
- Atalhos de teclado (Ctrl+S, Ctrl+Z, Ctrl+Shift+Z, /)
- Desfazer/Refazer com histórico
- Salvamento automático visível ("✓ Salvo agora" / "Salvando...")
- Duplicar atividade inteira

---

## RF-14 — Templates de Atividade

**Descrição:** Ao criar atividade, o professor pode escolher um template pré-definido.

| Template | Blocos Iniciais |
|----------|-----------------|
| Em branco | Nenhum |
| Lista de exercícios | Título + Questões |
| Aula prática | Título + Texto + Código + Exercício |
| Trabalho | Título + Texto + Lista de tarefas |
| Tutorial | Título + Passo a passo + Código |
| Projeto | Título + Texto + Checklist + Requisitos |

---

## RF-15 — Metadados da Atividade

**Descrição:** Atividades devem possuir metadados para organização e busca.

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| Título | string | Sim |
| Descrição | string | Não |
| Ano | FK | Sim |
| Disciplina | FK | Sim |
| Dificuldade | enum (Fácil/Médio/Difícil) | Não |
| Tempo estimado | string (ex: "30 min") | Não |
| Tags | string[] | Não |
| Pré-requisitos | string | Não |
| Data de publicação | datetime | Não |
| Prazo recomendado | date | Não |
| Fixar atividade | boolean | Não |
| Status | enum (Rascunho/Publicada/Arquivada) | Automático |

---

## RF-16 — Status das Atividades

**Descrição:** Atividades devem ter status controlado.

| Status | Alunos | Admin |
|--------|--------|-------|
| Rascunho | Invisível | Visível |
| Publicada | Visível | Visível |
| Arquivada | Invisível | Visível (com aviso) |

---

## RF-17 — Fixar Atividade

**Descrição:** Professor pode marcar atividade como destaque.

**Critérios:**
- Atividade fixada aparece **primeira** na lista da disciplina
- Badge visual de destaque
- Apenas atividades publicadas podem ser fixadas

---

## RF-18 — Duplicar Atividade

**Descrição:** Professor pode duplicar uma atividade inteira.

**Critérios:**
- Cópia inclui todos os blocos e metadados
- Novo título: "Cópia de [título original]"
- Status inicial: Rascunho
- Funcionando na lista de atividades da disciplina

---

## RF-19 — Sumário Automático

**Descrição:** Se a atividade tiver múltiplos blocos de Título, gerar sumário no topo.

**Critérios:**
- Extraídos automaticamente dos blocos tipo Título
- Exibido como "Nesta atividade: Item 1 · Item 2 · Item 3"
- Atualizado automaticamente ao adicionar/remover títulos

---

## RF-20 — Autenticação

**Descrição:** Login via JWT com backend Django.

**Critérios:**
- Store Pinia controla estado + token
- Token salvo no `localStorage`
- Login com email + senha via `////`
- Quick login (conta Monitoria)
- Logout limpa token e estado

---

## RF-21 — Proteção de Rotas

**Descrição:** Rotas administrativas exigem autenticação.

**Rotas protegidas:**
- `/`

**Critérios:**
- Route guard verifica token JWT
- Não autenticado → `/` (preserva rota na query)

---

## RF-22 — Pesquisa

**Descrição:** Buscar atividades por palavra-chave, tags, dificuldade, disciplina.

**Rota:** `/`

**Filtros:**
- Texto (título, conteúdo)
- Tags
- Dificuldade
- Disciplina
- Ano

---

## RF-23 — Tema Claro e Escuro

**Descrição:** Interface adaptável via variáveis CSS.

---

## RF-24 — Layout Responsivo

**Descrição:** Header desktop/mobile, drawer, cards adaptados.

---

## RF-25 — Animações

**Descrição:** Transições entre páginas, animações na Home.

---

## RF-26 — Página 404

**Descrição:** Rota coringa exibe `NotFoundView.vue`.

---

## Matriz de Prioridade

| RF | Alta | Média | Baixa |
|----|------|-------|-------|
| RF-01 Navegação | X | | |
| RF-02 Blocos (18 tipos) | X | | |
| RF-03 Questões Expandidas | X | | |
| RF-04 Download | X | | |
| RF-05 Links Externos | X | | |
| RF-06 Vídeo | X | | |
| RF-07 Galeria | | X | |
| RF-08 Aviso | | X | |
| RF-09 Terminal | | X | |
| RF-10 Tabela | | X | |
| RF-11 Passo a Passo | | X | |
| RF-12 Checklista | | X | |
| RF-13 Editor Completo | X | | |
| RF-14 Templates | X | | |
| RF-15 Metadados | X | | |
| RF-16 Status | X | | |
| RF-17 Fixar Atividade | | X | |
| RF-18 Duplicar Atividade | | X | |
| RF-19 Sumário Automático | | | X |
| RF-20 Autenticação | X | | |
| RF-21 Proteção Rotas | X | | |
| RF-22 Pesquisa | X | | |
| RF-23 Temas | | X | |
| RF-24 Responsividade | X | | |
| RF-25 Animações | | | X |
| RF-26 Página 404 | | | X |
