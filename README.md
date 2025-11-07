# Guia de Uso do Marp CLI

Este guia fornece instruções para instalar e usar o Marp CLI para criar apresentações a partir de arquivos Markdown. O Marp CLI permite converter seus slides Markdown em vários formatos como HTML, PDF e imagens.

## Instalação do Marp CLI

O Marp CLI requer Node.js v18 ou superior.

### Instalação Global (Recomendado)

Instale o Marp CLI globalmente para usar o comando `marp` diretamente do seu terminal:

```bash
npm install -g @marp-team/marp-cli
```

### Instalação Local (Para Projetos Node.js)

Se você preferir instalar o Marp CLI localmente dentro de um projeto específico:

```bash
npm install @marp-team/marp-cli
```

Após a instalação local, você pode usar `npx marp` ou definir scripts no seu `package.json` para executar comandos Marp.

## Principais Comandos do Marp CLI

A estrutura básica do comando é `marp [opções] <entrada...>`

*   `<entrada...>`: Um ou mais arquivos Markdown para converter.

### Preview (Visualização ao Vivo)

Para abrir uma janela de visualização que atualiza automaticamente conforme você edita o arquivo, use a opção `--preview` ou `-p`:

```bash
marp --preview slides.md
```

Isso é útil para ver suas alterações em tempo real sem precisar gerar um novo arquivo a cada vez.

### Converter para HTML

Para converter um arquivo Markdown (`slides.md`) para uma apresentação HTML (`slides.html`):

```bash
marp slides.md -o slides.html
```

### Converter para PDF

Para converter um arquivo Markdown para um documento PDF:

```bash
marp slides.md -o slides.pdf
```

**Observação:** Para exportar para PDF, PPTX e imagens, você deve ter o Google Chrome, Microsoft Edge ou Mozilla Firefox instalado em seu sistema.

### Converter para Imagem PNG

Para converter um arquivo Markdown para uma imagem PNG (cada slide como uma imagem separada):

```bash
marp slides.md -o slides.png
```

### Converter para PPTX (PowerPoint)

Para converter um arquivo Markdown para uma apresentação do PowerPoint:

```bash
marp slides.md -o slides.pptx
```

### Observar Alterações

Você pode usar a opção `--watch` (ou `-w`) para renderizar automaticamente seu arquivo de saída sempre que o arquivo Markdown de entrada for alterado:

```bash
marp slides.md -o slides.html --watch
```

### Ajuda

Para ver todas as opções e comandos disponíveis:

```bash
marp --help
```

## Exemplo de Criação de Slides

A criação de slides com Marp é feita usando a sintaxe Markdown padrão, com algumas extensões para controle de slides e temas. Cada slide é separado por `---`.

Aqui está um exemplo básico de um arquivo `meus-slides.md`:

```markdown
---
marp: true
theme: gaia
paginate: true
---

# Meu Primeiro Slide

Olá, Marp!

Este é o conteúdo do meu primeiro slide.

---

# Segundo Slide

## Tópicos:

- Item 1
- Item 2
- Item 3

---

# Imagens e Código

![Background Image](https://marp.app/assets/hero-background.jpg)

```python
def hello_marp():
    print("Hello from Python!")
```
```