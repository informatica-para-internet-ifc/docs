# Regras de Negócio

## Visão Geral

Regras de Negócio definem as **políticas e restrições obrigatórias** que o sistema deve respeitar.

---

## RN-01 — Acesso aos Conteúdos

1. **RN-01.1:** Conteúdo acadêmico é **público**. Qualquer visitante navega sem conta.
2. **RN-01.2:** Criação/edição de atividades exige autenticação.
3. **RN-01.3:** Fluxo público: `Home → Ano → Disciplina → Atividade`.

---

## RN-02 — Autenticação

1. **RN-02.1:** Login via JWT com backend Django (`////`).
2. **RN-02.2:** Token armazenado no `localStorage` do navegador.
3. **RN-02.3:** Após login, redireciona para a página que tentava acessar.
4. **RN-02.4:** Senhas criptografadas com PBKDF2 (padrão Django).

---

## RN-03 — Perfis de Acesso

| Perfil | Permissões |
|--------|-----------|
| Aluno | Consultar conteúdo (público) |
| Professor | Criar, editar, excluir atividades |
| Monitor | Mesmo que professor (apoio pedagógico) |

---

## RN-04 — Estrutura Acadêmica

1. **RN-04.1:** Hierarquia: `Ano → Disciplina → Atividade → Blocos`.
2. **RN-04.2:** Atividade pertence a **1 ano** e **1 disciplina**.

---

## RN-05 — Criação de Atividades

1. **RN-05.1:** Obrigatório: título, ano, disciplina, pelo menos 1 bloco.
2. **RN-05.2:** 18 tipos de blocos disponíveis.
3. **RN-05.3:** Blocos podem ser adicionados, removidos, duplicados e reordenados.
4. **RN-05.4:** Professor pode escolher um **template** inicial (Em branco, Lista de exercícios, Aula prática, Trabalho, Tutorial, Projeto).

---

## RN-06 — Metadados da Atividade

1. **RN-06.1:** Tags são armazenadas como array JSON (máx. 10 tags).
2. **RN-06.2:** Dificuldade: apenas valores `facil`, `medio`, `dificil`.
3. **RN-06.3:** Tempo estimado: texto livre (ex: "30 min", "2 horas").
4. **RN-06.4:** Pré-requisitos: texto livre descrevendo dependências.
5. **RN-06.5:** Data de programação: atividade fica invisível até a data.
6. **RN-06.6:** Prazo recomendado: data visual, sem bloqueio de acesso.
7. **RN-06.7:** Fixar: apenas atividades publicadas podem ser fixadas.

---

## RN-07 — Status

| Status | Alunos | Admin | Mudança |
|--------|--------|-------|---------|
| Rascunho | Invisível | Visível | → Publicada ou Arquivada |
| Publicada | Visível | Visível | → Rascunho ou Arquivada |
| Arquivada | Invisível | Visível | → Publicada |

---

## RN-08 — Duplicar Atividade

1. **RN-08.1:** Cópia inclui todos os blocos e metadados.
2. **RN-08.2:** Título: "Cópia de [original]".
3. **RN-08.3:** Status inicial: Rascunho.
4. **RN-08.4:** Data de criação: atual.

---

## RN-09 — Sumário Automático

1. **RN-09.1:** Gerado a partir dos blocos tipo Título.
2. **RN-09.2:** Atualizado automaticamente.
3. **RN-09.3:** Exibido no topo da atividade.

---

## RN-10 — Blocos de Conteúdo

### Bloco de Download
1. **RN-10.1:** Tipos aceitos: PDF, DOCX, PPTX, XLSX, ZIP, RAR, TAR.GZ.
2. **RN-10.2:** Tamanho máximo: 50MB.
3. **RN-10.3:** Arquivo salvo com UUID para evitar conflitos de nome.

### Bloco de Vídeo
1. **RN-10.4:** Apenas URLs do YouTube são aceitas.
2. **RN-10.5:** Player embutido via iframe.

### Bloco de Galeria
1. **RN-10.6:** Máximo de 20 imagens por galeria.
2. **RN-10.7:** Imagens via URL (upload indireto).

### Bloco de Aviso
1. **RN-10.8:** Tipo deve ser: informacao, dica, atencao, importante ou erro_comum.

### Bloco de Terminal
1. **RN-10.9:** Separação visual de código de programação.

### Bloco de Tabela
1. **RN-10.10:** Máximo de 20 colunas e 50 linhas.

### Bloco de Checklista
1. **RN-10.11:** Estado `concluido` não é salvo (referência visual apenas).

---

## RN-11 — Questões

1. **RN-11.1:** Tipos: multipla_escolha, verdadeiro_falso, discursiva, programacao.
2. **RN-11.2:** Múltipla escolha: 2 a 6 alternativas, 1 resposta correta.
3. **RN-11.3:** Verdadeiro/Falso: exatamente 2 opções.
4. **RN-11.4:** Discursiva: resposta aberta (sem validação automática).
5. **RN-11.5:** Programação: código como resposta (validação futura).

---

## RN-12 — Busca

1. **RN-12.1:** Busca por título, tags e conteúdo.
2. **RN-12.2:** Filtros opcionais: dificuldade, disciplina, ano.
3. **RN-12.3:** Apenas atividades publicadas aparecem na busca.

---

## RN-13 — Upload de Arquivos

1. **RN-13.1:** Apenas usuários autenticados podem fazer upload.
2. **RN-13.2:** Tamanho máximo: 50MB por arquivo.
3. **RN-13.3:** Tipos aceitos: pdf, docx, pptx, xlsx, zip, rar, tar.gz, jpg, png.
4. **RN-13.4:** Arquivo renomeado com UUID no storage.
5. **RN-13.5:** Download público para qualquer visitante.

---

## RN-14 — Exportação

1. **RN-14.1:** Exportar como Markdown: converte blocos para `.md`.
2. **RN-14.2:** Exportar como PDF: gera PDF formatado.
3. **RN-14.3:** Disponível para qualquer atividade publicada.

---

## RN-15 — Importação

1. **RN-15.1:** Professor pode colar Markdown ou enviar arquivo `.md`.
2. **RN-15.2:** Sistema converte para blocos de atividade.
3. **RN-15.3:** Atividade criada como Rascunho.

---

## RN-16 — Editor

1. **RN-16.1:** Salvamento automático no `localStorage` (chave `...`).
2. **RN-16.2:** Status visível: " Salvo agora" ou "Salvando...".
3. **RN-16.3:** Atalhos: Ctrl+S (salvar), Ctrl+Z (desfazer), Ctrl+Shift+Z (refazer), / (menu de blocos).
4. **RN-16.4:** Preview responsivo: alternar Celular / Desktop.
5. **RN-16.5:** Duplicar atividade inteira a partir da lista.

---
