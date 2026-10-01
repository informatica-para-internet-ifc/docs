# Modelagem do Banco

## Visão Geral

Banco **SQLite** (desenvolvimento, padrão) ou **PostgreSQL** (produção, via `DATABASE_URL`/`dj-database-url`), gerenciado via **Django ORM**. Models criados via migrations.

> **Estado atual:** todos os modelos abaixo (`User`, `Ano`, `Disciplina`, `Atividade`, `Bloco`, `Arquivo`, e os do app `uploader`: `Image`, `Document`, `Video`, `StoredFile`) estão implementados, migrados e servidos pela API REST (ver [api/README.md](../api/README.md)).

---

## Relacionamentos

```
Ano (1) ──< Disciplina (N)
                  │
Ano (1) ──< Atividade (N) >── (1) Disciplina
                  │
       (1) ──< Bloco (N)
                  │
       (1) ──< Arquivo (N)   (não usado pelo front-end hoje)
                  │
        autor >── User (1)

Image / Document / Video (app uploader) — soltos, sem FK para Atividade.
A ligação com um bloco é apenas a URL copiada manualmente para `Bloco.dados.url`.
Seu conteúdo binário fica em StoredFile quando não há Cloudinary configurado.
```

---

## Models

### 1. Ano

```python
class Ano(models.Model):
    """Ano do curso (1º, 2º ou 3º)."""

    numero = models.PositiveSmallIntegerField(unique=True, verbose_name='Número do ano')
    descricao = models.TextField(blank=True, verbose_name='Descrição')

    class Meta:
        ordering = ['numero']

    def __str__(self):
        return f'{self.numero}º Ano'
```

### 2. Disciplina

```python
class Disciplina(models.Model):
    """Disciplina pertencente a um ano do curso."""

    ano = models.ForeignKey(Ano, on_delete=models.CASCADE, related_name='disciplinas')
    slug = models.SlugField(max_length=50, unique=True, verbose_name='Slug')
    nome = models.CharField(max_length=120, verbose_name='Nome')
    descricao = models.TextField(blank=True, verbose_name='Descrição')
    icone = models.CharField(max_length=50, blank=True, verbose_name='Ícone (@mdi)')

    class Meta:
        ordering = ['ano', 'nome']
        unique_together = ('ano', 'nome')

    def __str__(self):
        return f'{self.ano} · {self.nome}'
```

### 3. User

Autenticação por e-mail (substitui o `username` padrão do Django).

```python
class User(AbstractBaseUser, PermissionsMixin):
    """Usuário do sistema."""

    email = models.EmailField(max_length=255, unique=True)
    name = models.CharField(max_length=255, blank=True, null=True)
    is_active = models.BooleanField(default=True)
    is_staff = models.BooleanField(default=False)

    objects = UserManager()

    USERNAME_FIELD = 'email'
    REQUIRED_FIELDS = []
```

### 4. Atividade (com todos os metadados)

```python
class Atividade(models.Model):
    """Atividade acadêmica com blocos de conteúdo."""

    class Dificuldade(models.TextChoices):
        FACIL = 'facil', 'Fácil'
        MEDIO = 'medio', 'Médio'
        DIFICIL = 'dificil', 'Difícil'

    class Status(models.TextChoices):
        RASCUNHO = 'rascunho', 'Rascunho'
        PUBLICADA = 'publicada', 'Publicada'
        ARQUIVADA = 'arquivada', 'Arquivada'

    class Categoria(models.TextChoices):
        QUESTAO = 'questao', 'Questão'
        ATIVIDADE = 'atividade', 'Atividade'
        TUTORIAL = 'tutorial', 'Tutorial'

    titulo = models.CharField(max_length=200)
    descricao = models.TextField(blank=True)
    capa = models.URLField(blank=True, max_length=500, verbose_name='Imagem de capa')
    ano = models.ForeignKey(Ano, on_delete=models.PROTECT, related_name='atividades')
    disciplina = models.ForeignKey(Disciplina, on_delete=models.PROTECT, related_name='atividades')
    autor = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.SET_NULL, null=True, related_name='atividades')

    categoria = models.CharField(max_length=10, choices=Categoria.choices, default=Categoria.ATIVIDADE)
    dificuldade = models.CharField(max_length=10, choices=Dificuldade.choices, blank=True)
    tempo_estimado = models.CharField(max_length=40, blank=True)
    tags = models.JSONField(default=list, blank=True)
    pre_requisitos = models.TextField(blank=True)

    data_publicacao = models.DateTimeField(null=True, blank=True, verbose_name='Programar publicação')
    prazo_recomendado = models.DateField(null=True, blank=True)
    fixada = models.BooleanField(default=False)
    status = models.CharField(max_length=10, choices=Status.choices, default=Status.RASCUNHO)

    criado_em = models.DateTimeField(auto_now_add=True)
    atualizado_em = models.DateTimeField(auto_now=True)

    class Meta:
        ordering = ['-fixada', '-atualizado_em']

    def __str__(self):
        return self.titulo
```

> `data_publicacao` existe no modelo e na API, mas não há nenhuma lógica (no backend ou no front-end) que oculte a atividade até essa data chegar — é o único campo de `Atividade` sem um controle correspondente no editor.

### 5. Bloco (21 tipos)

O conteúdo variável por tipo é armazenado em um JSON (`dados`), mantendo o modelo simples e flexível.

```python
class Bloco(models.Model):
    """Bloco de conteúdo de uma atividade."""

    class Tipo(models.TextChoices):
        TEXTO = 'text', 'Texto'
        TITULO = 'heading', 'Título'
        MARKDOWN = 'markdown', 'Markdown'
        CODIGO = 'code', 'Código'
        TERMINAL = 'terminal', 'Terminal'
        IMAGEM = 'image', 'Imagem'
        GALERIA = 'gallery', 'Galeria'
        VIDEO = 'video', 'Vídeo'
        EMBED = 'embed', 'Embed'
        LISTA = 'list', 'Lista'
        PASSOS = 'steps', 'Passo a Passo'
        CHECKLIST = 'checklist', 'Checklista'
        TABELA = 'table', 'Tabela'
        CITACAO = 'quote', 'Citação'
        AVISO = 'alert', 'Aviso'
        LINK = 'link', 'Link'
        LINKS = 'links', 'Links Externos'
        DOWNLOAD = 'file', 'Download'
        ACORDEAO = 'accordion', 'Acordeão'
        DIVISOR = 'divider', 'Divisor'
        QUESTAO = 'question', 'Questão'

    atividade = models.ForeignKey(Atividade, on_delete=models.CASCADE, related_name='blocos')
    tipo = models.CharField(max_length=20, choices=Tipo.choices)
    ordem = models.PositiveIntegerField(default=0)
    dados = models.JSONField(default=dict)

    class Meta:
        ordering = ['ordem']

    def __str__(self):
        return f'{self.atividade.titulo} · {self.get_tipo_display()}'
```

### Estrutura de Questões

O bloco de questão (`tipo = 'question'`) armazena os dados no JSON `dados` conforme o `modo`:

```json
// Múltipla Escolha
{
  "enunciado": "Qual é a capital do Brasil?",
  "modo": "multipla_escolha",
  "alternativas": [
    { "texto": "São Paulo" },
    { "texto": "Brasília" },
    { "texto": "Rio de Janeiro" }
  ],
  "correta": 1
}

// Verdadeiro/Falso
{
  "enunciado": "O Vue 3 usa Composition API.",
  "modo": "verdadeiro_falso",
  "respostaVf": true
}

// Discursiva
{
  "enunciado": "Explique o que é normalização de banco de dados.",
  "modo": "discursiva"
}

// Programação
{
  "enunciado": "Escreva uma função que soma dois números.",
  "modo": "programacao",
  "linguagem": "javascript",
  "codigoEsperado": "function soma(a, b) { return a + b; }"
}
```

### 6. Arquivo

```python
class Arquivo(models.Model):
    """Arquivo anexado a uma atividade (download público)."""

    atividade = models.ForeignKey(Atividade, on_delete=models.CASCADE, related_name='arquivos', null=True, blank=True)
    arquivo = models.FileField(upload_to='arquivos/%Y/%m/', storage=raw_storage)
    nome = models.CharField(max_length=200, verbose_name='Nome de exibição')
    tipo_arquivo = models.CharField(max_length=10, blank=True, verbose_name='Extensão')
    tamanho = models.PositiveBigIntegerField(default=0, verbose_name='Tamanho em bytes')
    criado_em = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.nome
```

> **Nota:** modelo completo e funcional, visível no Django Admin, mas **não usado pelo front-end** — o editor de atividades usa o app `uploader` para anexar arquivos aos blocos (ver abaixo). `nome` e `tipo_arquivo` não possuem validadores de tamanho máximo ou de extensão (ver [Regras de Negócio, RN-13](../regras-de-negocio/README.md)).

### App `uploader` — mídia usada pelos blocos

Três modelos quase idênticos, um por tipo de mídia aceita pelo editor (`Image`, `Document`, `Video`), mais um modelo de armazenamento (`StoredFile`):

```python
class Image(models.Model):
    attachment_key = models.UUIDField(default=uuid.uuid4, unique=True)
    public_id = models.UUIDField(default=uuid.uuid4, unique=True)
    file = models.ImageField(upload_to=image_file_path)  # storage padrão (ver abaixo)
    description = models.CharField(max_length=255, blank=True)
    uploaded_on = models.DateTimeField(auto_now_add=True)

# Document e Video seguem a mesma forma, mas usam storage explícito
# (raw_storage() / video_storage(), de uploader/helpers/storage.py)

class StoredFile(models.Model):
    """Conteúdo binário de um arquivo enviado, guardado no próprio banco."""
    name = models.CharField(max_length=500, unique=True)
    content = models.BinaryField()
    content_type = models.CharField(max_length=127, default='application/octet-stream')
    size = models.PositiveBigIntegerField(default=0)
    created_at = models.DateTimeField(auto_now_add=True)
```

Validação do tipo de arquivo é feita pelo **conteúdo real** (`python-magic`), não pela extensão: `Image` só aceita JPEG/PNG/WEBP/GIF, `Document` aceita PDF/DOC/DOCX/XLS/XLSX/PPT/PPTX/ZIP/TXT, `Video` aceita MP4/WEBM/MOV/AVI/MKV.

Nenhum desses três modelos tem uma chave estrangeira para `Atividade` — a relação é só lógica: o front-end envia o arquivo, recebe uma URL de volta e cola essa URL no campo `url` do bloco correspondente.

---

## Modelagem

| Modelo | Campos principais | Relação |
|--------|-------------------|---------|
| Ano | numero, descricao | — |
| Disciplina | ano, slug, nome, descricao, icone | FK → Ano |
| User | email, name, is_active, is_staff | — |
| Atividade | titulo, capa, ano, disciplina, autor, categoria, dificuldade, tags, status, fixada | FK → Ano, Disciplina, User |
| Bloco | atividade, tipo, ordem, dados (JSON) | FK → Atividade |
| Arquivo | atividade, arquivo, nome, tipo_arquivo, tamanho | FK → Atividade (não usado pelo front-end) |
| Image / Document / Video (`uploader`) | attachment_key, public_id, file, description | sem FK (vínculo só via URL copiada) |
| StoredFile (`uploader`) | name, content (binário), content_type, size | storage usado pelos modelos acima |

---

## Migrações

```bash
python manage.py makemigrations
python manage.py migrate
```

No PDM (scripts do `pyproject.toml`):

```bash
pdm run migrate        # makemigrations + migrate + graph_models (gera core.png)
```

---

## Consultas Úteis (Django ORM)

```python
from core.models import Ano, Atividade, Bloco

# Anos com suas disciplinas
Ano.objects.prefetch_related('disciplinas').all()

# Atividades publicadas de uma disciplina, fixadas primeiro
Atividade.objects.filter(disciplina_id=4, status='publicada').order_by('-fixada', '-atualizado_em')

# Atividades com todos os blocos
Atividade.objects.prefetch_related('blocos').all()

# Blocos de uma atividade ordenados
Bloco.objects.filter(atividade_id=10).order_by('ordem')

# Contagem de atividades por disciplina
from django.db.models import Count
Atividade.objects.values('disciplina__nome').annotate(total=Count('id'))

# Busca por tag
Atividade.objects.filter(tags__contains=['SQL'])
```
