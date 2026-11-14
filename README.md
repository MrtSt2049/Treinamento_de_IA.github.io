# Inteligência Artificial: Da Teoria Zero à Engenharia Mestre

Livro técnico em português do Brasil, da matemática de base a agentes de IA em produção. Este repositório contém o **código-fonte do livro** e o publica como site no **GitHub Pages**: texto em Markdown com LaTeX, programas Python com as saídas reais de execução, figuras vetoriais e os scripts que montam o livro.

Seis módulos, quinze capítulos, dez projetos integradores, 218 exercícios com solução comentada, glossário, índice de símbolos, bibliografia consolidada, índice remissivo e revisão editorial.

**Leitura online:** `https://SEU_USUARIO.github.io/NOME_DO_REPO/`

## Conteúdo

| Módulo | Nível | Capítulos |
|---|---|---|
| I. Fundamentos matemáticos, estatísticos e paradigmas iniciais | Base | 1. Matemática para computação e aprendizado · 2. Agentes racionais |
| II. Aprendizado estatístico e ML clássico | Intermediário | 3. Formulação do aprendizado · 4. Regressão e classificação · 5. Avaliação e generalização |
| III. Deep Learning | Avançado | 6. Redes neurais · 7. CNNs · 8. RNNs, LSTM e GRU |
| IV. Transformers e modelos de linguagem | Especialista | 9. Atenção e Transformer · 10. Hugging Face, LoRA e quantização |
| V. Aplicações com LLMs, RAG e conhecimento | Mestre | 11. Engenharia de contexto e confiabilidade · 12. RAG com LlamaIndex |
| VI. Agentes autônomos e sistemas em produção | Arquiteto | 13. Agentes e ferramentas · 14. LangGraph · 15. Avaliação, monitoramento e governança |

**Projetos integradores:**

1. Fundamentos matemáticos com NumPy
2. Pipeline de ML com scikit-learn
3. Rede neural do zero
4. Deep Learning com PyTorch
5. Classificação de imagens com CNN
6. Modelagem sequencial (RNN, LSTM, GRU)
7. Fine-tuning com LoRA
8. Sistema RAG com fontes
9. Agente com ferramentas
10. Sistema multiagente com LangGraph

## Estrutura do repositório

```
src/
  00-pretextuais.md          abertura: prefácio, notação, mapa de pré-requisitos
  01a … 15c-*.md             capítulos (cada um dividido em partes a, b, c, d)
  90 … 95-*.md               conclusão, glossário, símbolos, bibliografia, índice, revisão
  codigo/                    programas Python (.py) e saídas reais (.out)
  fig_*.svg, figs_c*.py      figuras e os scripts que as geram
  livro.json                 estrutura: módulos, capítulos e arquivos de cada um
  livro.css, livro.js, modelo.html   identidade visual e interface do livro
build/
  build.js                   converte Markdown + LaTeX em site estático (gera site/)
  empacotar.py               junta o site em um único HTML offline
  package.json
tools/
  qa.py, reexecuta.py        verificação do manuscrito e dos programas
  gera_bibliografia.py, gera_indice.py, gera_revisao.py   geram 93-, 94- e 95-*.md
.github/workflows/pages.yml  publicação automática no GitHub Pages
```

Os arquivos `93-`, `94-` e `95-` são **gerados** por script. Edite o script ou os capítulos e regenere, em vez de editar esses arquivos à mão.

## Publicar no GitHub Pages

O site é estático: `build.js` gera a pasta `site/` (um `index.html` mais `css/`, `js/` e um arquivo `capitulos/*.js` por capítulo, carregado sob demanda). Ela funciona em qualquer subcaminho, então serve para o endereço `usuario.github.io/repositorio/`. Há duas formas de publicar.

### Opção A: GitHub Actions (recomendada)

A pasta `site/` fica fora do Git (`.gitignore`) e o GitHub constrói e publica a cada push.

1. No repositório, abra **Settings → Pages** e, em **Source**, escolha **GitHub Actions**.
2. Crie `.github/workflows/pages.yml` com este conteúdo:

```yaml
name: Publicar no GitHub Pages
on:
  push:
    branches: [main]
  workflow_dispatch:
permissions:
  contents: read
  pages: write
  id-token: write
concurrency:
  group: pages
  cancel-in-progress: true
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - name: Construir o livro
        run: |
          cd build
          npm install
          node build.js
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with:
          path: site
  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

3. Faça o push para `main`. Em alguns minutos o livro estará no endereço indicado em **Settings → Pages**.

Com essa opção, o `.gitignore` deve conter `node_modules/`, `site/` e `__pycache__/`.

### Opção B: publicar a pasta construída

Sem Actions: gere o site localmente e comite a pasta de saída.

```
cd build
npm install
node build.js
cd ..
cp -r site docs
touch docs/.nojekyll
git add docs
git commit -m "Publica o site"
git push
```

Em **Settings → Pages**, escolha **Deploy from a branch**, branch `main`, pasta `/docs`. Nesse caso, **não** coloque `site/` no `.gitignore`, mas continue ignorando `node_modules/`. A cada alteração no livro, repita a construção e o commit.

### Verificar depois de publicar

- Abra o endereço e navegue por um capítulo: o conteúdo carrega a cada clique, então erros de caminho aparecem logo.
- Teste uma âncora direta, como `.../#cap-13` ou `.../#pos-indice`, para confirmar que os links do índice remissivo funcionam.
- Alternativa de um arquivo só: `python3 build/empacotar.py Livro_IA_Completo.html` gera uma versão offline, que pode ir como *release* do repositório, sem entrar no Git.

## Construir e testar localmente

Requer Node.js e Python 3.10 ou superior.

```
cd build
npm install
node build.js                                    # gera site/ (abra site/index.html)
python3 empacotar.py ../Livro_IA_Completo.html   # opcional: arquivo único offline
```

O `build.js` usa o KaTeX com erro fatal: qualquer fórmula inválida interrompe a construção, então um site que compila não tem LaTeX quebrado. Para testar em servidor local: `python3 -m http.server -d site 8000`.

## Como executar os programas

Cada programa de `src/codigo/` deve ser executado de dentro dessa pasta, porque alguns leem arquivos por caminho relativo (por exemplo, `../livro.json`):

```
cd src/codigo
python3 c6_exemplo.py
```

O texto do livro inclui o código e a saída por meio de diretivas (`<<< codigo/x.py` e `>>> codigo/x.out`), então a saída impressa no livro vem sempre do arquivo `.out`.

**Ambiente usado nas execuções:** Python 3.13, CPU, sementes explícitas. As versões das bibliotecas (NumPy, scikit-learn, PyTorch, `transformers`, `peft`, `llama-index-core`, `langchain`, `langgraph` e outras) estão listadas na abertura do livro.

## Verificação e reprodutibilidade

```
python3 tools/qa.py                     # numeração, referências cruzadas, exercícios x soluções
python3 tools/reexecuta.py <pasta-src>  # reexecuta os programas e compara com os .out
```

Na reexecução completa, 103 de 115 programas com saída salva reproduziram a saída idêntica. Os demais variam só em medidas de tempo, dependem do treino não determinístico de um tokenizador ou não foram reexecutados; os detalhes estão em `src/95-revisao.md`.

## Para editar ou corrigir

1. Edite o `.md` do capítulo em `src/`.
2. Se mudou um programa, reexecute-o e regenere o `.out` correspondente.
3. Rode `python3 tools/qa.py`.
4. Regenere os pós-textuais, se for o caso: `gera_bibliografia.py`, `gera_indice.py`, `gera_revisao.py`.
5. Faça o push. Com a Opção A, o site é reconstruído sozinho.

## Limites conhecidos

- **Sem internet no ambiente de produção:** modelos pré-treinados e o Hugging Face Hub, ResNet-18 com pesos, LlamaParse, avaliação por LLM-juiz e LangSmith **não foram executados**. Esses trechos estão marcados como "não executado" no texto.
- **LLMs simulados nos Capítulos 13 a 15:** `langchain` e `langgraph` são os pacotes reais, mas o modelo é um modelo de chat scriptado. Os resultados mostram a mecânica (composição de erros, custo com e sem checkpoint, portão humano), não quanto um modelo real erraria.
- **Experimentos pequenos:** tudo roda em CPU, e fenômenos de escala não são reproduzidos.
- **Referências de memória:** confira anos, volumes e páginas antes de citar formalmente. Nenhum DOI ou URL foi inventado. Leis e normas (LGPD, regulamento europeu de IA, NIST) devem ser lidas nas fontes oficiais, e o livro não é aconselhamento jurídico.
- **Versões:** interfaces de `langchain`, `langgraph` e `transformers` mudam entre versões. As do livro foram conferidas nas versões executadas.
- **Redação assistida por IA:** o texto, o código e as figuras foram produzidos por Claude (Anthropic), a partir do roteiro e das exigências do autor. Verifique o conteúdo crítico, principalmente referências e detalhes de software.

## Autoria

Marcus Rafael Iwasaki Marquini Amaral. Novembro de 2026.
