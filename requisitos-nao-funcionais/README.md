# Requisitos Não Funcionais

Requisitos Não Funcionais descrevem **como o sistema deve funcionar** — restrições de qualidade, desempenho e segurança.

---

## RNF-01 — Performance

**Descrição:** A aplicação deve carregar e responder rapidamente.

| Requisito | Implementação |
|-----------|------|
| Lazy loading de views | Cada rota importa sua view com `() => import(...)` — code splitting automático do Vite |
| Animações | Baseadas em CSS/transform (GPU), sem bloquear a thread principal |
| Imagens enviadas no editor | Recomprimidas no navegador antes do upload (`compressImage.js`), alvo ~900 KB |
| Build de produção | Minificado, com hashes, via Vite |
| PWA / Service Worker | `vite-plugin-pwa` (Workbox) faz cache de assets estáticos e fontes do Google Fonts para carregamento mais rápido em visitas repetidas |

---

## RNF-02 — Compatibilidade de Navegadores

**Descrição:** A aplicação deve funcionar nos principais navegadores modernos, sem transpilação para navegadores muito antigos (depende de ES Modules nativos, usados pelo Vite).

| Navegador | Suporte |
|-----------|---------|
| Chrome / Edge (Chromium) | Suporte total |
| Firefox | Suporte total |
| Safari | Suporte total |
| Navegadores Android/iOS | Suporte total |

---

## RNF-03 — Responsividade

**Descrição:** A interface deve se adaptar a diferentes tamanhos de tela.

| Dispositivo | Resolução | Comportamento |
|-------------|-----------|---------------|
| Mobile | < 480px | Layout compacto, menu hambúrguer + drawer lateral |
| Tablet | 480px – 768px | Grid adaptado |
| Desktop | > 768px | Layout completo, menu horizontal |

---

## RNF-04 — Acessibilidade (WCAG básico)

**Descrição:** Seguir boas práticas de acessibilidade.

| Requisito | Implementação |
|-----------|---------------|
| Contraste de cores | Mínimo 4.5:1 (texto normal) |
| Navegação por teclado | Links e botões acessíveis; atalhos Ctrl+K (busca) e Ctrl+S (salvar no editor) |
| Skip link | "Pular para o conteúdo" visível ao focar via Tab (`App.vue`) |
| Hierarquia de títulos | H1 → H2 → H3 sequencial |
| Texto alternativo | `alt` em imagens (campo editável no bloco de Imagem) |
| Labels em formulários | `label`/placeholder associados aos campos |
| Focus visível | Indicador de foco em elementos interativos |
| Redução de movimento | Diretiva `v-reveal` respeita `prefers-reduced-motion` |

---

## RNF-05 — SEO e Metadados

**Descrição:** Facilitar a indexação e o compartilhamento.

| Requisito | Implementação |
|-----------|---------------|
| Título da página | `<title>` dinâmico por rota, montado no `router.afterEach` |
| URLs amigáveis | Hierarquia legível (`/ano/1`, `/disciplina/1/logica`) |
| Robots.txt | `public/robots.txt` |
| Progressive Web App | Manifest e ícones gerados por `vite-plugin-pwa` (instalável em dispositivos compatíveis) |

> Não há meta description dinâmica por atividade nem sitemap gerado hoje.

---

## RNF-06 — Qualidade de Código

| Requisito | Implementação |
|-----------|---------------|
| Estrutura de pastas | Separada por responsabilidade (`views`, `components`, `composables`, `stores`, `api`...) |
| Linting (front-end) | ESLint + Oxlint |
| Formatação (front-end) | Prettier |
| Linting/formatação (backend) | Ruff (`ruff check`, `ruff format`) + pylint |

---

## RNF-07 — Persistência Local (localStorage)

**Descrição:** Alguns dados são preservados no navegador do usuário — nunca o catálogo em si.

| Dados | Chave localStorage | Finalidade |
|-------|--------------------|------------|
| Sessão do usuário | `sio-user` | Token JWT (access + refresh) e perfil, para manter o login entre recarregamentos |
| Rascunho de atividade (só ao criar, não ao editar) | `sio-draft-activity` | Não perder trabalho em progresso no editor |
| Tema (claro/escuro) | — (gerenciado por `useTheme.js`) | Lembrar a preferência do usuário |

> Anos, disciplinas e atividades **não** são persistidos no `localStorage`: a lista de anos/disciplinas tem um fallback fixo no código (`staticAnos`), substituído pela resposta da API em memória; atividades são buscadas da API a cada carregamento e mantidas apenas em memória (`reactive`), em `src/data/disciplinas.js`.

---

## RNF-08 — Deploy Contínuo

**Descrição:** O projeto suporta deploy automático para frontend e backend, em serviços separados.

```
Frontend                              Backend
--------                              -------
Push na branch main                   Push na branch main
        │                                      │
        ▼                                      ▼
Vercel builda (vite build)            Fabroku detecta a alteração
        │                                      │
        ▼                                      ▼
Site atualizado em                    Instala dependências, roda
informatica-para-internet.vercel.app  migrations e collectstatic
                                               │
                                               ▼
                                       Backend atualizado (URL de
                                       produção configurada no Fabroku)
```

---

## RNF-09 — Leveza

**Descrição:** A aplicação deve ser leve e com poucas dependências de runtime.

| Aspecto | Prática |
|---------|---------|
| Dependências de runtime (front-end) | Vue, Vue Router, Pinia, @mdi |
| Renderização de Markdown | Implementação própria (`useMarkdown.js`), sem lib externa |
| Build | Vite separa o código por view (code splitting) |
| Backend | Dependências declaradas no `pyproject.toml` (PDM) |

---

## RNF-10 — Segurança do Backend

**Descrição:** Backend deve seguir boas práticas de segurança.

| Requisito | Implementação |
|-----------|---------------|
| Senhas criptografadas | Django usa PBKDF2 por padrão |
| Proteção CSRF | Django CSRF middleware |
| Proteção XSS | Template escaping automático + escape no renderizador de Markdown próprio |
| SQL Injection | Django ORM (queries parametrizadas) |
| Autenticação JWT | `djangorestframework-simplejwt` (access: 3h, refresh: 1 dia) |
| Autorização por ViewSet | `DEFAULT_PERMISSION_CLASSES = AllowAny`; cada ViewSet do catálogo libera leitura e exige `IsAuthenticated` para escrita via `get_permissions()` |
| CORS configurado | `django-cors-headers` (`FRONTEND_URLS`) |
| HTTPS | Fabroku (SSL automático) |
| Variáveis sensíveis | `.env` (nunca no código; `.env.example` documenta as chaves esperadas) |
| ALLOWED_HOSTS | `['*']` — atualmente aberto (sem restrição de host) |
| SECRET_KEY | Deve ser definida via `.env` em produção (há um valor inseguro padrão para desenvolvimento) |
| Upload de arquivos | Validação do tipo real do arquivo via `python-magic` nos endpoints `/api/media/*`; `/api/arquivos/` e o bloco de Download **não** validam tamanho máximo |
| Armazenamento de mídia | Padrão: binário no próprio banco de dados; opcional: Cloudinary (`USE_CLOUDINARY`) |
