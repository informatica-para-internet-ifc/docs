# Modelagem do Banco de Dados

## Visão Geral

Banco **PostgreSQL** gerenciado via **Django ORM**. Models criados via migrations.

---

## Models Django

### 1. Ano

```python
class Ano(models.Model):

```

---

### 2. Disciplina

```python
class Disciplina(models.Model):

```

---

### 3. Usuario

```python
class User(AbstractUser):

```

---

### 4. Atividade (com todos os metadados)

```python
class Atividade(models.Model):
```

---

### 5. Bloco (18 tipos)

```python
class Bloco(models.Model):
```


### Estrutura de Questões

```json
// Múltipla Escolha
{
  "tipo": "multipla_escolha",
}

// Verdadeiro/Falso
{
  "tipo": "verdadeiro_falso",
}

// Discursiva
{
  "tipo": "discursiva",
}

// Programação
{
  "tipo": "programacao",
}
```

---

### 6. Arquivo (para downloads)

```python
class Arquivo(models.Model):
```

---

## Modelagem


---

## Migrations

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## Consultas Úteis (Django ORM)

```
