# Regras de Negócio

Regras de Negócio definem as **políticas e restrições obrigatórias** que o sistema deve seguir, independentemente da interface.

---

## RN-01 — Acesso Público

1. **RN-01.1:** Qualquer visitante pode navegar e visualizar atividades sem autenticação.
2. **RN-01.2:** Apenas criação, edição, duplicação e exclusão de atividades exigem login.

---

## RN-02 — Autenticação

1. **RN-02.1:** Login via JWT com backend Django (`POST /api/token/`), usando **e-mail** (não username).
2. **RN-02.2:** Sessão (access token + refresh token + perfil) armazenada no `localStorage` (chave `sio-user`).
3. **RN-02.3:** Após login, redireciona para a rota que tentava acessar (`?redirect=...`), quando aplicável.
4. **RN-02.4:** Senhas criptografadas com PBKDF2 (padrão Django).
5. **RN-02.5:** Não há renovação automática do token (`/api/token/refresh/` existe na API mas não é chamado pelo front-end) nem conta de demonstração/"login rápido" — o login depende sempre da API responder.

---

## RN-03 — Navegação Hierárquica

1. **RN-03.1:** Home exibe a lista de anos diretamente (a rota `/anos` é apenas um redirect para `/`).
2. **RN-03.2:** Estrutura fixa: Ano → Disciplina → Atividade.
3. **RN-03.3:** Dentro da disciplina, atividades são agrupadas por categoria (Questão / Atividade / Tutorial), em abas.

---

## RN-04 — Disponibilidade dos Dados

1. **RN-04.1:** Anos e disciplinas têm uma lista fixa no código (`staticAnos`, em `src/data/disciplinas.js`) usada como estrutura inicial/fallback; ao carregar, a API substitui a lista de disciplinas de cada ano pelos dados reais.
2. **RN-04.2:** Atividades existem apenas via API — se o backend estiver indisponível, nenhuma atividade é exibida.

---

## RN-05 — Criação de Atividades

1. **RN-05.1:** Obrigatório: título, ano, disciplina, pelo menos 1 bloco (validado no front-end antes de enviar).
2. **RN-05.2:** 21 tipos de blocos disponíveis.
3. **RN-05.3:** Blocos podem ser adicionados em qualquer posição, removidos, duplicados e reordenados (botões ↑/↓ ou arrastar e soltar).
4. **RN-05.4:** *(não implementado)* Templates iniciais (Em branco, Lista de exercícios, Aula prática, Trabalho, Tutorial, Projeto) — toda atividade nova começa vazia.

---

## RN-06 — Metadados da Atividade

Todos os campos abaixo têm um controle correspondente no editor (`ActivitySidebar.vue` / `ActivityCoverForm.vue`), exceto `data_publicacao`.

1. **RN-06.1:** Tags são armazenadas como array JSON, digitadas uma a uma (Enter ou vírgula), sem limite de quantidade validado.
2. **RN-06.2:** Dificuldade: apenas `facil`, `medio`, `dificil` (choices do model).
3. **RN-06.3:** Tempo estimado: texto livre (ex: "45 min").
4. **RN-06.4:** Pré-requisitos: texto livre.
5. **RN-06.5:** `data_publicacao` existe no modelo e na API, mas **não há lógica, no backend ou no front-end, que oculte a atividade** até essa data — e não há campo para preenchê-la no editor hoje.
6. **RN-06.6:** Prazo recomendado: campo de data, sem bloqueio de acesso.
7. **RN-06.7:** Fixar: validado no `AtividadeSerializer` — só pode ser `true` quando `status = publicada`; o editor desabilita o controle enquanto o status não é "Publicada".

---

## RN-07 — Status

| Status | Alunos (pretendido) | Alunos (comportamento atual) | Admin | Mudança |
|--------|--------|-------|-------|---------|
| Rascunho | Invisível | **Visível** (sem filtro de status na listagem pública) | Visível | → Publicada ou Arquivada |
| Publicada | Visível | Visível | Visível | → Rascunho ou Arquivada |
| Arquivada | Invisível | **Visível** (sem filtro de status na listagem pública) | Visível | → Publicada |

> A API suporta o filtro `?status=` em `/api/atividades/`, mas `DisciplinaView.vue` chama `GET /api/atividades/?disciplina=<slug>` sem informar `status` — na prática, toda atividade criada aparece publicamente, independentemente do status escolhido.

---

## RN-08 — Categoria da Atividade

1. **RN-08.1:** Toda atividade tem uma `categoria`: Questão, Atividade ou Tutorial (padrão: Atividade).
2. **RN-08.2:** `DisciplinaView.vue` exibe as atividades em abas por categoria, com contagem.

---

## RN-09 — Duplicar Atividade

1. **RN-09.1:** Copia todos os blocos e metadados (exceto autor e identificador).
2. **RN-09.2:** Novo título: "Cópia de [título original]", status reiniciado para Rascunho.
3. **RN-09.3:** O autor da cópia é quem executou a duplicação, não o autor original.

---

## RN-10 — Blocos de Conteúdo

### Bloco de Download
1. **RN-10.1:** No editor, o bloco Download envia o arquivo direto pelo navegador (`POST /api/media/documents/`) **ou** aceita uma URL colada manualmente; nome e tamanho de exibição são preenchidos automaticamente a partir do arquivo enviado, mas continuam editáveis.
2. **RN-10.2:** Tipos aceitos no upload: PDF, DOC, DOCX, XLS, XLSX, PPT, PPTX, ZIP, TXT (validados pelo conteúdo real do arquivo, via `python-magic`).
3. **RN-10.3:** *(não implementado)* Limite de tamanho — não há validação de tamanho máximo no backend para esse upload.

### Bloco de Vídeo
1. **RN-10.4:** Aceita upload direto de arquivo de vídeo (MP4/WEBM/MOV/AVI/MKV) **ou** link do YouTube ou Vimeo.
2. **RN-10.5:** Vídeos do YouTube/Vimeo são exibidos via iframe; arquivos enviados usam a tag `<video>`.

### Bloco de Galeria
1. **RN-10.6:** Múltiplas imagens, cada uma com upload próprio (compressão automática, ver RN-11) ou URL colada, e legenda individual.

### Bloco de Questão
1. **RN-10.7:** 4 modos: múltipla escolha (índice da alternativa correta), verdadeiro/falso (`respostaVf`), discursiva (sem resposta armazenada) e programação (`linguagem` + `codigoEsperado`).

---

## RN-11 — Upload de Imagens

1. **RN-11.1:** Antes do upload, o navegador redimensiona e recomprime a imagem (`compressImage.js`), tentando chegar a ~900 KB; se mesmo assim passar de 950 KB, o envio é bloqueado com um aviso.
2. **RN-11.2:** GIFs não são recomprimidos (preservar animação).
3. **RN-11.3:** O servidor também valida o tipo MIME real do arquivo (`get_content_type`, via `python-magic`).

---

## RN-12 — Busca

1. **RN-12.1:** Busca por título, descrição e enunciados de questão — feita no cache local do front-end (`searchAtividades`), **sem** chamar a API.
2. **RN-12.2:** Filtros na busca local: ano, disciplina. O backend (`GET /api/busca/`) também aceita `dificuldade` e busca em `tags`, mas esse endpoint não é consumido pela interface.
3. **RN-12.3:** *(não implementado)* Filtrar por status "publicada" — a busca local não filtra por status (mesma limitação da RN-07).

---

## RN-13 — Upload de Arquivos (`/api/arquivos/`)

1. **RN-13.1:** Apenas usuários autenticados podem enviar (`POST /api/arquivos/`, `IsAuthenticated`).
2. **RN-13.2:** *(não implementado)* Tamanho máximo — não validado pelo `ArquivoSerializer`.
3. **RN-13.3:** *(não implementado)* Restrição de tipos de arquivo — não validada por este endpoint (diferente do app `uploader`, que valida pelo conteúdo real).
4. **RN-13.4:** Nome de exibição (`nome`) e extensão (`tipo_arquivo`) são extraídos automaticamente do arquivo enviado pelo serializer.
5. **RN-13.5:** Download público para qualquer visitante (`GET /api/arquivos/:id/`, `AllowAny`).
6. **RN-13.6:** **Este endpoint não está integrado à interface** — o bloco "Download" do editor usa `/api/media/documents/` (ver RN-10.1), não este. `/api/arquivos/` existe no backend, aparece no Django Admin, mas hoje não tem nenhuma tela correspondente.

---

## RN-14 — Exportação

**Implementado na API (`GET /api/atividades/:id/exportar/`), sem botão na interface ainda.**

1. **RN-14.1:** Exportar como Markdown: converte blocos de texto, título, markdown, código, lista, citação, questão e divisor para `.md`. Tipos de bloco sem regra específica (imagem, vídeo, tabela, etc.) geram apenas uma linha em branco.
2. **RN-14.2:** Exportar como PDF: endpoint existe (`?formato=pdf`) mas retorna `501 Not Implemented` — não implementado.
3. **RN-14.3:** Disponível para qualquer atividade, independentemente do status (a action `exportar` é pública).

---

## RN-15 — Importação

**Implementado na API (`POST /api/atividades/importar/`), sem tela/botão na interface ainda.**

1. **RN-15.1:** Requer `titulo` (opcional), `disciplina` (slug), `ano` e `markdown` (texto) no corpo da requisição.
2. **RN-15.2:** Um parser simples converte títulos (`#`), blocos de código (```` ``` ````), listas (`-`/`*`), citações (`>`) e parágrafos nos blocos correspondentes.
3. **RN-15.3:** Atividade criada como Rascunho, vinculada ao usuário autenticado que fez a chamada.

---

## RN-16 — Editor

1. **RN-16.1:** Salvamento automático no `localStorage` (chave `sio-draft-activity`, debounce de 600ms) — apenas ao **criar** uma atividade nova, não ao editar uma existente.
2. **RN-16.2:** O botão de salvar mostra "Salvando..." durante a requisição.
3. **RN-16.3:** Atalho implementado: **Ctrl+S / Cmd+S** salva a atividade. Desfazer/Refazer (Ctrl+Z / Ctrl+Shift+Z) e o atalho "/" para o menu de blocos **não** estão implementados.
4. **RN-16.4:** *(não implementado)* Preview responsivo (alternar Celular/Desktop) — o modo Visualizar usa o layout padrão da tela.
5. **RN-16.5:** Duplicar atividade inteira a partir da lista — **implementado**.
6. **RN-16.6:** Confirmação ao sair com alterações não salvas (`beforeunload` + guard de rota) — **implementado**.
