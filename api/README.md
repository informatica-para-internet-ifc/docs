# API e Camada de Dados

## Visão Geral

Backend Django com Django REST Framework. A API REST serve o front-end Vue 3.

- **Autenticação:** JWT (`djangorestframework-simplejwt`), header `Authorization: Bearer <access_token>`. Access token válido por 3h, refresh por 1 dia (`SIMPLE_JWT` em `app/settings.py`). O front-end guarda os dois no `localStorage`, mas **não implementa a renovação automática** via `/api/token/refresh/` — o refresh token fica salvo sem uso.
- **Documentação interativa:** Swagger UI (`/api/doc/`), ReDoc (`/api/redoc/`) e schema OpenAPI 3 (`/api/schema/`), gerados por `drf-spectacular`.
- **Paginação:** `CustomPagination` (`app/pagination.py`), página de 10 itens por padrão, parâmetro `page_size` (máx. 100). Resposta: `{page, page_size, total_pages, results}`. O front-end lida com isso via `unwrapPaged()` (`src/api/client.js`), que também aceita um array puro (sem paginação).
- **Permissões:** `DEFAULT_PERMISSION_CLASSES` é `AllowAny` globalmente; cada ViewSet restringe escrita (`create`/`update`/`partial_update`/`destroy`) a `IsAuthenticated` via `get_permissions()`, mantendo leitura (`list`/`retrieve`) pública.
- **CORS:** `django-cors-headers`; origens liberadas por `FRONTEND_URLS` (variável de ambiente, padrão `http://localhost:5173,http://127.0.0.1:5173`).
- **Arquivos de mídia enviados no editor:** por padrão, guardados como binário no próprio banco de dados (ver [arquitetura/README.md](../arquitetura/README.md#armazenamento-de-arquivos-enviados)); opcionalmente no Cloudinary via `USE_CLOUDINARY=true`.

---

## Base URL

```
Local:  http://localhost:8000/api/
```

```
Swagger UI:   http://localhost:8000/api/doc/
ReDoc:        http://localhost:8000/api/redoc/
OpenAPI 3:    http://localhost:8000/api/schema/
Django Admin: http://localhost:8000/admin/
```

> O front-end de desenvolvimento roda em `http://localhost:5173` (autorizado via `FRONTEND_URLS`). A URL base consumida pelo front-end vem de `VITE_API_URL` (`.env` do Vite); se não definida, `src/api/client.js` usa `http://127.0.0.1:8000` como padrão.
>
> **Em produção**, além de `VITE_API_URL` (frontend) apontar para a URL real do backend, o próprio backend precisa da variável `BACKEND_URL` configurada com seu domínio público — ela é usada para montar as URLs absolutas dos arquivos de mídia enviados pelo editor.

---

## Endpoints

### Autenticação

```
POST /api/token/               → Obter access + refresh token
POST /api/token/refresh/       → Renovar access token
POST /api/token/verify/        → Validar um token
```

Request de `/api/token/`:
```json
{ "email": "professor@ifc.edu.br", "password": "••••••••" }
```
Resposta (200):
```json
{ "access": "eyJ...", "refresh": "eyJ..." }
```

### Usuários

```
POST   /api/registro/          → Criar usuário (público)
GET    /api/usuarios/          → Listar usuários (autenticado)
POST   /api/usuarios/          → Criar usuário (autenticado)
GET    /api/usuarios/me/       → Dados do usuário autenticado
GET    /api/usuarios/{id}/     → Detalhar usuário (autenticado)
PUT    /api/usuarios/{id}/     → Atualizar usuário (autenticado)
PATCH  /api/usuarios/{id}/     → Atualização parcial (autenticado)
DELETE /api/usuarios/{id}/     → Remover usuário (autenticado)
```

O modelo `User` usa **e-mail** como identificador de login (`USERNAME_FIELD = 'email'`), não um `username` tradicional. Registro exige `email`, `name` (opcional) e `password` (mínimo 8 caracteres).

### Anos

```
GET /api/anos/        → lista anos com disciplinas aninhadas (ReadOnlyModelViewSet)
GET /api/anos/{id}/
```

### Disciplinas

```
GET /api/disciplinas/           # filtro por ?ano=
GET /api/disciplinas/{id}/
```

### Atividades

```
GET    /api/atividades/          # filtros: ?disciplina=<slug>&ano=<id>&status=&categoria=&autor=<id>
POST   /api/atividades/          # autenticado — cria com blocos aninhados
GET    /api/atividades/{id}/     # retorna blocos aninhados
PUT    /api/atividades/{id}/     # autenticado — blocos são substituídos por completo (delete + recria)
PATCH  /api/atividades/{id}/     # autenticado
DELETE /api/atividades/{id}/     # autenticado
```

Campos de `Atividade` (ver [modelagem-banco/README.md](../modelagem-banco/README.md)): `titulo`, `descricao`, `capa` (URL), `ano`, `disciplina` (slug), `categoria` (`questao`/`atividade`/`tutorial`), `dificuldade`, `tempo_estimado`, `tags`, `pre_requisitos`, `data_publicacao`, `prazo_recomendado`, `fixada`, `status` (`rascunho`/`publicada`/`arquivada`).

Validações do `AtividadeSerializer`:
- A disciplina informada precisa pertencer ao ano informado.
- `fixada=true` só é aceito quando `status='publicada'`.

**Ações extras (`@action`) no `AtividadeViewSet`:**

```
GET  /api/atividades/{id}/blocos/            → lista blocos ordenados da atividade
POST /api/atividades/{id}/blocos/            → cria vários blocos de uma vez (autenticado)
POST /api/atividades/{id}/duplicar/          → duplica a atividade inteira (autenticado)
                                                 novo título "Cópia de {original}", status = rascunho,
                                                 autor = quem duplicou (não o autor original)
GET  /api/atividades/{id}/exportar/?formato=markdown   → gera .md a partir dos blocos (público)
GET  /api/atividades/{id}/exportar/?formato=pdf        → 501 Not Implemented (ainda não existe)
POST /api/atividades/importar/               → converte markdown em blocos e cria a atividade
                                                 (autenticado; body: {titulo, ano, disciplina, markdown})
```

### Blocos

```
GET    /api/blocos/              # filtro por ?atividade=<id>
POST   /api/blocos/              # autenticado
GET    /api/blocos/{id}/
PATCH  /api/blocos/{id}/         # autenticado
DELETE /api/blocos/{id}/         # autenticado
```

### Arquivos vinculados a atividade (`/api/arquivos/`)

```
GET  /api/arquivos/              # lista (público)
POST /api/arquivos/              # upload multipart, autenticado
                                    calcula nome/extensão/tamanho a partir do arquivo enviado
GET  /api/arquivos/{id}/         # download público (FileResponse, as_attachment=True)
```

> **Não usado pelo front-end hoje.** O bloco "Download" do editor usa `/api/media/documents/` (ver abaixo), não este endpoint. Não há validação de tamanho máximo nem de extensões permitidas no `ArquivoSerializer` — qualquer arquivo enviado por um usuário autenticado é aceito.

### Upload de mídia para os blocos (app `uploader`)

```
POST /api/media/images/          # autenticado — multipart, campo "file"
POST /api/media/documents/       # autenticado — multipart, campo "file"
POST /api/media/videos/          # autenticado — multipart, campo "file"
GET  /api/media/documents/{id}/  # download forçado (Content-Disposition: attachment) — público
GET  /media/<path>               # arquivo cru guardado no banco (imagens/vídeos); suporta Range — público
```

É este grupo de endpoints que o editor de atividades usa de verdade: blocos de Imagem, Galeria, Vídeo e Download, além da capa da atividade, enviam o arquivo aqui (`src/api/uploader.js` → `uploadImage` / `uploadDocument` / `uploadVideo`) e colam a `url` retornada no campo `url` do bloco.

Tipos de arquivo aceitos (validados por tipo MIME real via `python-magic`, não pela extensão):

| Endpoint | Tipos aceitos |
|---|---|
| `images/` | JPEG, PNG, WEBP, GIF |
| `documents/` | PDF, DOC, DOCX, XLS, XLSX, PPT, PPTX, ZIP, TXT |
| `videos/` | MP4, WEBM, MOV (QuickTime), AVI, MKV |

Resposta de um upload (ex.: `POST /api/media/images/`):
```json
{
  "attachment_key": "b1f2...-uuid",
  "description": "",
  "uploaded_on": "2026-10-01T12:00:00Z",
  "url": "https://seu-backend.com/media/images/5c9a....jpg"
}
```

Antes de enviar uma imagem, o front-end tenta **comprimi-la no navegador** (`src/utils/compressImage.js`): redimensiona e reduz a qualidade progressivamente até caber em ~900 KB; se ainda assim passar de **950 KB**, o upload é recusado no cliente com um aviso (ver `useBlockUpload.js`). Documentos e vídeos não passam por essa compressão e não têm limite de tamanho imposto pelo front-end ou pelo `ArquivoSerializer`/`uploader` serializers — o limite prático é o do servidor/proxy em produção.

### Busca

```
GET /api/busca/?q=termo
GET /api/busca/?q=termo&disciplina=<slug>&dificuldade=<facil|medio|dificil>&ano=<id>
```

Busca por `titulo`, `descricao`, `tags` e conteúdo dos `blocos.dados` (case-insensitive), com `q` de pelo menos 2 caracteres. **Implementada no backend, mas não é chamada pelo front-end** — `SearchView.vue` filtra o cache local em memória (`searchAtividades`, em `src/data/disciplinas.js`), pesquisando apenas título, descrição e enunciados de questão, com filtros de ano e disciplina.

---

## Exemplos de Request/Response

### Criar uma atividade

`POST /api/atividades/` (autenticado):
```json
{
  "titulo": "Lista 03 — Laços de Repetição",
  "descricao": "Exercícios sobre for/while.",
  "capa": "",
  "ano": 1,
  "disciplina": "logica",
  "categoria": "atividade",
  "dificuldade": "medio",
  "tempo_estimado": "45 min",
  "tags": ["javascript", "loops"],
  "pre_requisitos": "",
  "status": "publicada",
  "prazo_recomendado": null,
  "fixada": false,
  "blocos": [
    { "tipo": "heading", "dados": { "content": "Introdução", "level": 2 } },
    { "tipo": "code", "dados": { "content": "for (let i = 0; i < 10; i++) {}", "language": "javascript" } }
  ]
}
```

Resposta (201): o mesmo objeto, com `id`, `autor`, `autor_nome`, `disciplina_slug`, `ano_numero`, `criado_em`, `atualizado_em` e `blocos` já com `id` e `ordem` preenchidos.

### Exportar como Markdown

```
GET /api/atividades/12/exportar/?formato=markdown
```
Resposta: `text/markdown`, convertendo blocos de texto, título, markdown, código, lista, citação, questão e divisor; outros tipos (imagem, vídeo, tabela etc.) ainda não têm regra de exportação e geram uma linha em branco.

---

## Fluxo de Autenticação (Front-End)

1. `ProfileView.vue` envia e-mail/senha para `auth.login()` (store Pinia).
2. A store chama `POST /api/token/`; em caso de sucesso, guarda `access`/`refresh` e tenta `GET /api/usuarios/me/` para obter o nome do usuário (opcional — falha silenciosa se não conseguir).
3. A sessão (`{token, refresh, user}`) é salva em `localStorage` sob a chave `sio-user`.
4. `src/api/client.js` lê esse token em toda requisição e injeta `Authorization: Bearer <token>`.
5. Não há tela de "login rápido"/demo nem fallback para sessão simulada — login sempre depende da API responder.
