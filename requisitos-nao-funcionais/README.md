# Requisitos Não Funcionais

## Visão Geral

Requisitos Não Funcionais descrevem **como o sistema deve funcionar** — restrições de qualidade, performance, usabilidade e compatibilidade.

---

## RNF-01 — Performance

**Descrição:** A aplicação deve carregar e responder rapidamente.

---

## RNF-02 — Compatibilidade de Navegadores

**Descrição:** A aplicação deve funcionar nos principais navegadores modernos.

---

## RNF-03 — Responsividade

**Descrição:** A interface deve ser totalmente funcional em qualquer dispositivo.

| Dispositivo | Resolução | Comportamento |
|-------------|-----------|---------------|
| Mobile | < 480px | Layout compacto, hamburger menu |
| Tablet | 480px - 768px | Grid adaptado |
| Desktop | > 768px | Layout completo |

---

## RNF-04 — Acessibilidade

**Descrição:** A aplicação deve seguir boas práticas de acessibilidade.

| Requisito | Implementação |
|-----------|---------------|
| Contraste de cores | Mínimo 4.5:1 (texto normal) |
| Navegação por teclado | Links e botões acessíveis |
| Hierarquia de títulos | H1 → H2 → H3 sequencial |
| Texto alternativo | `alt` em imagens |
| Labels em formulários | `label` associado a inputs |
| Focus visível | Indicador de foco em elementos interativos |

---

## RNF-05 — SEO Básico

**Descrição:** A aplicação deve ter boas práticas básicas de SEO.

| Requisito | Implementação |
|-----------|---------------|
| Título da página | `<title>` dinâmico por rota |
| Meta description | Descrição por página |
| URLs amigáveis | Hierarquia legível (`/ano/1`) |
| Open Graph | Tags para compartilhamento |
| Robots.txt | Configurado |
| Sitemap | Gerado (Vercel) |

---

## RNF-06 — Manutenibilidade

**Descrição:** O código deve ser fácil de entender, modificar e estender.

**Padrões Adotados:**

| Aspecto | Padrão |
|---------|--------|
| Indentação | 2 espaços |
| Nomes de arquivos | PascalCase para `.vue` (`HomeView.vue`) |
| Nomes de variáveis | camelCase |
| Constantes | UPPER_SNAKE_CASE |
| Estrutura de pastas | Separada por responsabilidade |
| Linting | ESLint + Oxlint |
| Formatação | Prettier |

---

## RNF-07 — Ferramentas de Qualidade

**Descrição:** O projeto deve utilizar ferramentas automatizadas para garantir qualidade.

| Ferramenta | Função |
|------------|--------|
| ESLint | Detecção de erros e boas práticas |
| Oxlint | Linting rápido (Rust-based) |
| Prettier | Formatação consistente de código |

---

## RNF-08 — Persistência Local

**Descrição:** Dados importantes devem ser preservados no navegador do usuário.

| Dados | Chave localStorage | Finalidade |
|-------|-------------------|------------|
| Usuário logado | `...` | Manter sessão entre recarregamentos |
| Rascunho de atividade | `...` | Não perder trabalho em progresso |

---

## RNF-9 — Deploy Contínuo

**Descrição:** O projeto deve suportar deploy automático para frontend e backend.

**Pipeline Frontend (Vercel):**
```
Push na branch main
        │
        ▼
Vercel detecta alteração
        │
        ▼
Build automático (npm run build)
        │
        ▼
Deploy em produção
        │
        ▼
Site atualizado em informatica-para-internet.vercel.app
```

**Pipeline Backend (Fabroku):**
```
Push na branch main
        │
        ▼
Fabrigo detecta alteração
        │
        ▼
Instala dependências (pip install)
        │
        ▼
Aplica migrações (python manage.py migrate)
        │
        ▼
Coleta estáticos (python manage.py collectstatic)
        │
        ▼
Reinicia o servidor Django
        │
        ▼
Backend atualizado em https://
```

---

## RNF-10 — Tamanho do Projeto

**Descrição:** A aplicação deve ser leve e com poucas dependências.

---

## RNF-11 — Segurança do Backend

**Descrição:** O backend Django deve seguir boas práticas de segurança.

**Requisitos:**

| Requisito | Implementação |
|-----------|---------------|
| Senhas criptografadas | Django usa PBKDF2 por padrão |
| Proteção CSRF | Django CSRF middleware |
| Proteção XSS | Template escaping automático |
| SQL Injection | Django ORM (queries parametrizadas) |
| Autenticação JWT | djangorestframework-simplejwt |
| CORS configurado | django-cors-headers |
| HTTPS | Fabrigo (SSL automático) |
| Variáveis sensíveis | `.env` (nunca no código) |
| ALLOWED_HOSTS | Configurado no `.env` |
| SECRET_KEY | Gerada e armazenada no `.env` |

---
