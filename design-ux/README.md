# Design e Experiência do Usuário (UX)

## Visão Geral

O projeto possui uma identidade visual **minimalista, moderna e tecnológica**. A interface trabalha com tons neutros e azul como cor de destaque, criando uma estética associada ao ambiente acadêmico e à tecnologia.

---

## Identidade Visual

### Características do Design

- Fundos neutros
- Superfícies em diferentes níveis (cards, modais)
- Azul como destaque (botões, ícones, elementos selecionados)
- Bordas discretas
- Cantos arredondados
- Sombras suaves
- Ícones Material Design (`@mdi`)
- Animações pequenas e sutis
- Espaçamento generoso entre elementos
- Hierarquia tipográfica forte

---

## Sistema de Temas (CSS Variables)

O projeto utiliza variáveis CSS para desacoplar componentes de cores específicas. Isso permite trocar entre tema claro e escuro sem alterar os componentes.

### Variáveis Principais

```css
:root {
  /* Texto */
  --color-text-1: /* texto principal (escuro no light, claro no dark) */;
  --color-text-2: /* texto secundário */;
  --color-text-3: /* texto terciário (mais suave) */;

  /* Superfícies */
  --color-surface-1: /* fundo da página */;
  --color-surface-2: /* fundo de cards */;
  --color-surface-3: /* fundo elevado (modais, dropdowns) */;

  /* Borda */
  --color-border-1: /* borda principal */;
  --color-border-2: /* borda secundária */;

  /* Destaque */
  --color-navy-accent: /* azul de destaque */;

  /* Sombras */
  --shadow-sm: /* sombra leve */;
  --shadow-md: /* sombra média */;
  --shadow-lg: /* sombra forte */;

  /* Bordas arredondadas */
  --radius-sm: /* cantos pequenos */;
  --radius-md: /* cantos médios */;
  --radius-lg: /* cantos grandes */;
  --radius-xl: /* cantos extras grandes */;
}

```

---

## Paleta de Cores

| Cor | Uso | Contexto |
|-----|-----|----------|
| Azul Navy | Destaque principal | Botões, links, ícones ativos, badges |
| Branco | Fundo (light) | Superfícies principais |
| Cinza Escuro | Fundo (dark) | Superfícies no tema escuro |
| Cinza Claro | Fundo de cards | Superfícies secundárias |
| Cinza Médio | Bordas e divisores | Elementos de separação |
| Gradiente azul | Título "Internet" | Efeito luminoso no hero |

---

## Tipografia

O projeto utiliza hierarquia tipográfica forte:

| Elemento | Estilo |
|----------|--------|
| Título principal (Home) | Grande, bold, gradiente no destaque |
| Subtítulos | Médios, bold, hierarquia clara |
| Corpo de texto | Tamanho regular, boa legibilidade |
| Badges | Pequenos, uppercase ou com ícone |
| Botões | Médios, peso semi-bold |

---

---

## Responsividade

### Breakpoints

| Dispositivo | Largura | Comportamento |
|-------------|---------|---------------|
| Mobile | < 480px | Layout compacto, botão full-width |
| Tablet | 480px - 768px | Grid adaptado, cards menores |
| Desktop | > 768px | Layout completo, sidebar opcional |

### Adaptações por Página

**Home (mobile < 480px):**
- Título reduzido
- Descrição compactada
- Botão "Começar Agora" ocupa 100% da largura
- Espaçamentos menores

**Anos (mobile):**
- Cards mais compactos
- Número do ano menor
- Informações organizadas verticalmente

**Header:**
- Desktop → `AppHeaderDesktop.vue` (menu horizontal)
- Mobile → `AppHeaderMobile.vue` + `SidebarDrawer.vue` (hamburger + drawer)

### Grid

```
Desktop:  Cards em grid de 2-3 colunas
Tablet:   Cards em grid de 2 colunas
Mobile:   Cards em coluna única (empilhados)
```

---

