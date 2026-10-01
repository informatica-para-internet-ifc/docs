# Arquitetura do Sistema

## Visão Geral

O projeto é uma **SPA em Vue 3** consumindo uma **API REST em Django + Django REST Framework**, hospedadas separadamente (Vercel e Fabroku).

```
┌─────────────────────────────────────────────────────────┐
│                    FRONT-END (Vue 3 + Vite)              │
│  ┌─────────────────────────────────────────────────┐    │
│  │  Views (páginas) → Components → Composables       │    │
│  │                     ↓                              │    │
│  │   data/disciplinas.js (cache reativo em memória)   │    │
│  │                     ↓                              │    │
│  │              Vue Router (navegação)                │    │
│  └──────────────────────┬──────────────────────────┘    │
│                          │ HTTP/JSON (fetch)              │
└──────────────────────────┼───────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────┐
│                 BACK-END (Django + DRF)                  │
│  ┌─────────────────────────────────────────────────┐    │
│  │  Serializers → Views/ViewSets → Models             │    │
│  │                                    ↓                │    │
│  │                  SQLite (dev) / PostgreSQL (prod)   │    │
│  └─────────────────────────────────────────────────┘    │
│                                                            │
│  ┌─────────────────────────────────────────────────┐    │
│  │          Django Admin (painel de gestão)           │    │
│  └─────────────────────────────────────────────────┘    │
│                                                            │
│  Hospedagem: Fabroku                                      │
└─────────────────────────────────────────────────────────┘
```

> **Importante:** `src/data/disciplinas.js` nasce com uma lista **fixa, escrita no código** dos anos e disciplinas (`staticAnos`, em torno de 12 disciplinas distribuídas em 1º/2º/3º ano). No boot da aplicação (`initCatalog()`), o front-end busca `GET /api/anos/` e substitui a lista de disciplinas de cada ano pela resposta da API — mas se a API estiver fora do ar, a aplicação continua funcionando com a lista fixa (sem atividades, já que estas vêm só da API). Criar/renomear um ano ou disciplina no banco exige também ajustar `staticAnos` em `src/data/disciplinas.js` para a navegação inicial não ficar desatualizada.

---

## Camadas

### Front-End (Vue 3)

```
src/
├── api/
│   ├── client.js                  # fetch wrapper: base URL, Authorization, parse de erros,
│   │                               #   uploadFile() (multipart), fixMediaUrl()
│   ├── catalog.js                 # funções da API de catálogo + mapeamento API ↔ formato do front
│   └── uploader.js                # uploadImage / uploadDocument / uploadVideo (POST /api/media/...)
├── assets/
│   ├── css/
│   │   ├── reset.css              # reset de estilos do navegador
│   │   ├── tokens-shared.css      # variáveis comuns (espaçamento, tipografia, sombras)
│   │   ├── tokens-light.css       # variáveis do tema claro
│   │   ├── tokens-dark.css        # variáveis do tema escuro
│   │   ├── theme.css              # base do tema + data-theme
│   │   ├── components.css         # classes utilitárias e de componentes
│   │   ├── transitions.css        # transições e animações (inclui v-reveal)
│   │   └── print.css              # estilos de impressão (classe `.no-print`)
│   └── img/logo/logo.svg
├── components/
│   ├── atividade/                 # header, TOC, renderizador de blocos (visualização)
│   │   └── blocks/                # um .vue por tipo de bloco (ImageBlock, FileBlock, ...)
│   ├── createActivity/            # editor: capa, sidebar de metadados, lista de blocos
│   │   └── blocks/                # um .vue por tipo de bloco, com upload embutido quando aplicável
│   ├── layout/
│   │   ├── AppFooter.vue
│   │   └── header/
│   │       ├── AppHeader.vue, AppHeaderDesktop.vue, AppHeaderMobile.vue, SidebarDrawer.vue
│   └── layout/ui/
│       ├── BackToTop.vue, ToastStack.vue, logo/AppLogo.vue,
│       │   search/SearchButton.vue, themeButton/ThemeToggle.vue, user/UserButton.vue
│   └── ui/
│       ├── AppButton.vue, AppListCard.vue, ConfirmDialog.vue
├── composables/
│   ├── useBlockUpload.js          # upload de arquivos no editor: compressão de imagem + toasts
│   ├── useMarkdown.js             # renderizador de Markdown próprio (sem dependência externa)
│   ├── useTheme.js                # controle do tema (light/dark) via localStorage
│   ├── useToast.js                # fila reativa de toasts (success/error/info)
│   ├── useClipboard.js            # copiar texto (Clipboard API com fallback `execCommand`)
│   └── useInstallPrompt.js        # prompt de instalação do PWA
├── data/
│   └── disciplinas.js             # staticAnos (fallback fixo) + cache reativo de anos/atividades
├── directives/
│   └── vReveal.js                 # diretiva `v-reveal`: fade/slide-in via IntersectionObserver
├── router/
│   └── index.js                   # rotas, títulos dinâmicos e guard de autenticação
├── stores/
│   ├── auth.js                    # Pinia: sessão do usuário (JWT), login/logout via API
│   └── isMobile.js                # Pinia: detecção de tela mobile
├── utils/
│   ├── activityBlocks.js          # catálogo de tipos de bloco, opções de categoria/dificuldade/status
│   └── compressImage.js           # recompressão de imagens no navegador antes do upload
├── views/
│   ├── HomeView.vue               # landing page: lista de anos
│   ├── AnoView.vue                # disciplinas do ano
│   ├── DisciplinaView.vue         # atividades da disciplina, por categoria
│   ├── AtividadeView.vue          # visualização da atividade (blocos)
│   ├── CreateActivityView.vue     # editor de atividades (criar/editar)
│   ├── SearchView.vue             # busca (Ctrl+K)
│   ├── SobreView.vue              # página "Sobre" — placeholder, sem conteúdo
│   ├── ProfileView.vue            # login / painel administrativo
│   └── NotFoundView.vue           # 404
├── App.vue                        # componente raiz (skip link, header, router-view, footer, toasts)
└── main.js                        # bootstrap: Pinia, router, diretiva v-reveal, initCatalog()
```

### Back-End (Django)

```
backend/
├── app/                           # projeto Django
│   ├── settings.py                # DRF, JWT, CORS, banco, armazenamento de mídia
│   ├── urls.py                    # rotas da API
│   ├── pagination.py              # paginação padrão
│   └── wsgi.py / asgi.py
├── core/                          # aplicação de catálogo
│   ├── models/                    # User, Ano, Disciplina, Atividade, Bloco, Arquivo
│   ├── serializers/                # User/UserRegistration, Ano, Disciplina, Atividade/Bloco, Arquivo
│   ├── views/                     # token, user, ano, disciplina, atividade/bloco, arquivo, busca
│   ├── admin.py                   # admin (inlines de Disciplina/Bloco)
│   └── migrations/
├── uploader/                      # aplicação de upload de mídia para os blocos
│   ├── models/                    # Image, Document, Video, StoredFile
│   ├── serializers/, views.py, router.py, storage.py, helpers/
│   └── admin.py
├── http/drf/                      # coleção Bruno para testar a API
├── scripts/cria_api.py            # gerador de CRUDs (scaffolding)
├── manage.py
├── pyproject.toml                 # PDM: dependências e scripts
├── requirements.txt
└── .env / .env.example
```

---

## Dois sistemas de arquivo, propósitos diferentes

O backend tem **duas** aplicações que lidam com arquivos — não confundir:

| | `core.Arquivo` | app `uploader` (`Image`, `Document`, `Video`) |
|---|---|---|
| Endpoint | `GET/POST /api/arquivos/` | `POST /api/media/{images,documents,videos}/` |
| Usado pelo front-end hoje? | **Não** — nenhuma tela chama esse endpoint | **Sim** — é o upload usado nos blocos de Imagem/Galeria/Vídeo/Download e na capa da atividade |
| Vinculado a uma Atividade? | Sim, via FK | Não — o arquivo fica solto; a URL retornada é colada manualmente no campo `url` do bloco |
| Permissões | leitura pública, escrita autenticada | leitura/criação: `list` público, `create` autenticado (ver `uploader/views.py`) |

`core.Arquivo` parece ter sido pensado como o modelo "oficial" de anexos (é ele que aparece no Django Admin, com `nome`, `tipo_arquivo`, `tamanho`), mas na prática o editor de atividades usa exclusivamente o app `uploader`.

### Armazenamento de arquivos enviados

Por padrão (`USE_CLOUDINARY` não definido), **todo arquivo enviado é guardado como binário dentro do próprio banco de dados** (tabela `uploader.StoredFile`), não em disco nem no Cloudinary — importante porque o disco do container é apagado a cada deploy. Esses arquivos são servidos de volta pela rota `media/<path:path>` (`uploader.views.serve_stored_file`), que suporta `Range` (necessário para vídeo).

Com `USE_CLOUDINARY=true` e `CLOUDINARY_URL` configurados, imagens e vídeos passam a ser enviados ao Cloudinary; documentos (`Document`, `core.Arquivo`) só vão para o Cloudinary se, além disso, `CLOUDINARY_RAW_ENABLED=true` — por padrão, contas gratuitas do Cloudinary bloqueiam a entrega de arquivos "raw" (PDF, ZIP, DOCX...), então o padrão é mantê-los no banco mesmo com o Cloudinary ativo.

> **Ponto de atenção operacional:** a URL absoluta do arquivo é montada a partir de `settings.BACKEND_URL` (variável de ambiente; padrão `http://127.0.0.1:8000`) no momento da resposta da API. Se `BACKEND_URL` não estiver configurado corretamente no deploy, a URL retornada pelo upload aponta para `127.0.0.1` — e essa URL fica **gravada dentro do JSON do bloco** (campo `dados.url`) quando o professor salva a atividade, já que o front-end copia `result.url` direto para o bloco. Corrigir `BACKEND_URL` depois resolve uploads futuros, mas blocos já salvos continuam com o link antigo até o arquivo ser reenviado. Como rede de segurança, `fixMediaUrl()` (`src/api/client.js`) reescreve, só no navegador e só na exibição, qualquer URL `127.0.0.1`/`localhost` encontrada em blocos de imagem/vídeo/arquivo/galeria para o endereço configurado em `VITE_API_URL`.

---

## Fluxo de Dados (Front-End)

```
1. main.js chama initCatalog() no boot da aplicação
         │
         ▼
2. initCatalog() → GET /api/anos/ (fetchAnos) substitui as disciplinas de cada
   ano (mantendo o fallback staticAnos se a API falhar)
         │        → refreshAtividades() dispara GET /api/atividades/?disciplina=
         │          para cada disciplina conhecida e preenche `atividades[slug]`
         ▼
3. Views (HomeView, AnoView, DisciplinaView, AtividadeView...) leem
   `anos` / `atividades` via getAno / getDisciplina / getAtividades / getAtividade
         │
         ▼
4. Professor autenticado cria/edita/duplica/exclui atividades:
   addAtividade / editAtividade / duplicarAtividade / deleteAtividade
   → chamam src/api/catalog.js (POST/PUT/DELETE /api/atividades/...)
   → atualizam o cache reativo local com a resposta da API
         │
         ▼
5. Login/logout são controlados pela store Pinia `auth` (src/stores/auth.js),
   que chama POST /api/token/ e GET /api/usuarios/me/, persistindo a sessão
   (access token + refresh token + perfil) em localStorage (sio-user)
```

> **Observação:** a busca em `/buscar` (`SearchView.vue`) filtra o cache local em memória (`searchAtividades`, em `src/data/disciplinas.js`) e **não** chama o endpoint `GET /api/busca/` do backend — os dois existem em paralelo hoje, mas apenas o primeiro está em uso.

---

## Rotas do Frontend

| Rota | View | Autenticação | Título |
|------|------|--------------|--------|
| `/` | `HomeView` | — | Início |
| `/anos` | *(redirect para `/`)* | — | — |
| `/ano/:anoId` | `AnoView` | — | Nome do ano (ex: 1º Ano) |
| `/disciplina/:anoId/:disciplinaId` | `DisciplinaView` | — | Nome da disciplina |
| `/atividade/:disciplinaId/:atividadeId` | `AtividadeView` | — | Título da atividade |
| `/buscar` | `SearchView` | — | Buscar |
| `/sobre` | `SobreView` | — | Sobre |
| `/criar-atividade` | `CreateActivityView` | Sim | Criar Atividade |
| `/editar-atividade/:disciplinaId/:atividadeId` | `CreateActivityView` | Sim | Editar Atividade |
| `/perfil` | `ProfileView` | — | Minha Conta |
| `/:pathMatch(.*)*` | `NotFoundView` | — | Página não encontrada |

O título da aba é montado no `afterEach`: `{título} · Informática para Internet`.

Rotas protegidas (`meta.requiresAuth`) usam `beforeEach` + store `auth`; sem sessão, redirecionam para `/perfil` preservando o destino em `?redirect=...`.

---

## Endpoints da API Django

Detalhes de request/response em [api/README.md](../api/README.md).

| Método | Endpoint | Descrição | Permissão |
|--------|----------|-----------|-----------|
| POST | `/api/token/` | Obter par de tokens JWT (access + refresh) | Pública |
| POST | `/api/token/refresh/` | Renovar access token | Pública |
| POST | `/api/token/verify/` | Verificar validade de um token | Pública |
| POST | `/api/registro/` | Criar novo usuário | Pública |
| GET | `/api/usuarios/` | Listar usuários | Autenticado |
| GET/PUT/PATCH/DELETE | `/api/usuarios/:id/` | Detalhar/atualizar/remover usuário | Autenticado |
| GET | `/api/usuarios/me/` | Dados do usuário autenticado | Autenticado |
| GET | `/api/anos/`, `/api/anos/:id/` | Listar/detalhar anos (com disciplinas aninhadas) — somente leitura | Pública |
| GET | `/api/disciplinas/` (filtro `?ano=`), `/api/disciplinas/:id/` | Listar/detalhar disciplinas — somente leitura | Pública |
| GET | `/api/atividades/` | Listar atividades (filtros `?disciplina=&ano=&status=&categoria=&autor=`) | Pública |
| GET | `/api/atividades/:id/` | Detalhar atividade (com blocos aninhados) | Pública |
| POST/PUT/PATCH/DELETE | `/api/atividades/:id/` | Criar/editar/remover atividade | Autenticado |
| GET/POST | `/api/atividades/:id/blocos/` | Listar / criar blocos em lote de uma atividade | Pública (GET) / Autenticado (POST) |
| POST | `/api/atividades/:id/duplicar/` | Duplicar atividade (blocos + metadados, vira rascunho) | Autenticado |
| GET | `/api/atividades/:id/exportar/?formato=` | Exportar em Markdown (`pdf` retorna `501`, não implementado) | Pública |
| POST | `/api/atividades/importar/` | Converter Markdown em blocos e criar atividade (rascunho) | Autenticado |
| GET/POST | `/api/blocos/` | Listar / criar bloco avulso (filtro `?atividade=`) | Pública (GET) / Autenticado (POST) |
| GET/PATCH/DELETE | `/api/blocos/:id/` | Detalhar/editar/remover bloco | Pública (GET) / Autenticado (demais) |
| GET/POST | `/api/arquivos/` | Listar / enviar arquivo vinculado a atividade — **não usado pelo front-end hoje** | Pública (GET) / Autenticado (POST) |
| GET | `/api/arquivos/:id/` | Download (`FileResponse`, `as_attachment`) | Pública |
| POST | `/api/media/images/`, `/api/media/documents/`, `/api/media/videos/` | Upload usado pelo editor de blocos (multipart, campo `file`) | Autenticado |
| GET | `/api/media/documents/:id/` | Download de documento (força `Content-Disposition: attachment`) | Pública |
| GET | `/media/<path>` | Arquivo guardado no banco (imagens/vídeos/docs sem Cloudinary); suporta `Range` | Pública |
| GET | `/api/busca/?q=&disciplina=&dificuldade=&ano=` | Busca de atividades no backend — **não consumida pelo front-end hoje** | Pública |
| GET | `/api/schema/`, `/api/doc/`, `/api/redoc/` | OpenAPI 3 / Swagger UI / ReDoc | Pública |
| GET | `/admin/` | Django Admin | Staff |

> `DEFAULT_PERMISSION_CLASSES` está configurado como `AllowAny` globalmente; a proteção real de escrita vem de `get_permissions()` em cada ViewSet (leitura pública, escrita `IsAuthenticated`).

---

## Sistema de Blocos

Cada atividade é composta por blocos ordenados (`Bloco.ordem`). O conteúdo variável de cada tipo é guardado em `Bloco.dados` (JSON); o front-end "achata" esses dados junto com `tipo`/`ordem` ao exibir (`mapBloco`, em `src/api/catalog.js`).

| Tipo | `type` | Campos do conteúdo |
|------|--------|--------------------|
| Texto | `text` | `content` |
| Título | `heading` | `content`, `level` |
| Markdown | `markdown` | `content` |
| Código | `code` | `content`, `language` |
| Terminal | `terminal` | `commands[]` |
| Imagem | `image` | `url`, `alt` — upload direto (`image/*`) ou URL colada |
| Galeria | `gallery` | `images[{url, caption}]`, `caption` — cada imagem com upload próprio |
| Vídeo | `video` | `url`, `title` — upload de arquivo de vídeo ou link do YouTube/Vimeo |
| Embed | `embed` | `url`, `title`, `height` |
| Lista | `list` | `items[]`, `ordered` |
| Passo a Passo | `steps` | `steps[{title, desc}]` |
| Checklista | `checklist` | `items[{text, done}]` |
| Tabela | `table` | `rows[][]`, `hasHeader` |
| Citação | `quote` | `content`, `author` |
| Aviso | `alert` | `content`, `tipo` (`info`/`success`/`warning`/`danger`/`erro`) |
| Link | `link` | `url`, `label`, `desc` |
| Links Externos | `links` | `links[{title, url, desc}]` |
| Download | `file` | `label`, `url`, `size`, `desc` — upload direto (PDF/DOC/XLS/PPT/ZIP/TXT) ou URL colada |
| Acordeão | `accordion` | `items[{title, content}]` |
| Divisor | `divider` | — |
| Questão | `question` | `enunciado`, `modo`, `alternativas[]`, `correta`, `respostaVf`, `linguagem`, `codigoEsperado` |

**Mecânica de edição (`CreateActivityView.vue` + `BlockEditorList.vue`):**

- Adicionar bloco em qualquer posição, remover, duplicar
- Reordenar com botões ↑ / ↓ e também arrastando e soltando
- Colapsar/expandir blocos individualmente ou todos de uma vez
- Rascunho automático no `localStorage` (chave `sio-draft-activity`, debounce de 600ms) enquanto cria uma atividade nova — não se aplica ao editar uma já existente
- Atalho **Ctrl+S / Cmd+S** para salvar
- Confirmação ao sair com alterações não salvas (`beforeunload` + guard de rota `onBeforeRouteLeave`)
- Modo **Editar ↔ Visualizar** com preview em tempo real
- Painel lateral (`ActivitySidebar.vue`) com **todos** os metadados da atividade — categoria, ano, disciplina, dificuldade, tempo estimado, tags, pré-requisitos, status, prazo recomendado e "fixar" (habilitado só quando status = publicada) — e capa (`ActivityCoverForm.vue`, upload de imagem)

> Diferente do que uma leitura rápida do modelo de dados sugere, **não há campos "só por Admin"**: todo campo de `Atividade` (exceto `autor`, preenchido automaticamente, e `data_publicacao`, que existe no modelo mas não é usado em lugar nenhum da interface) tem um controle correspondente no editor.
