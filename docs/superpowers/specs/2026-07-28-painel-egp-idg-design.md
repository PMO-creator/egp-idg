# Painel EGP · IDG — Design

**Data:** 28/07/2026
**Status:** aprovado, pendente de implementação

---

## 1. Problema

Os documentos que compõem o Escritório de Gestão de Projetos (EGP) do IDG estão
espalhados: parte em pastas do Google Drive, parte em planilhas do Sheets, parte
em HTML no repositório `maz-dashboard`, parte em formulários do Google Forms.

Não existe um lugar único que mostre **quais são os documentos do processo, em que
ordem se usam e onde cada um está**. Quem entra novo no EGP não tem mapa; quem já
está precisa caçar link a link.

## 2. Solução

Um painel HTML de página única — visualmente derivado do dashboard MAZ — que
apresenta o processo do EGP como cards navegáveis. Cada card abre o documento
correspondente em nova aba, onde quer que ele esteja hospedado.

O painel **não hospeda documentos**. Ele é um índice: um card, um link, um destino.

### Não-objetivos (v1)

- Busca e filtros — são 11 cards
- Seletor de projeto / multiprojeto
- Conversão dos documentos do Drive para HTML
- Modo escuro, contador de acessos, autenticação

## 3. Decisões e justificativas

| Decisão | Escolha | Por quê |
|---|---|---|
| Hospedagem dos documentos | Híbrida | Documento vivo (Matriz, Cronograma, Forms) fica no Drive, onde é editável. Documento finalizado em HTML fica no GitHub Pages, onde renderiza. |
| Onde publicar o painel | Repo novo `egp-idg`, público | Painel institucional, independente do MAZ. Público é requisito do GitHub Pages no plano gratuito. |
| Formato dos documentos do Drive | Manter como estão | Entrega rápida, zero retrabalho. Converter depois, um por vez, se justificar. |
| Fonte de verdade do TAP/EAP | O HTML no GitHub Pages | Já publicados no `maz-dashboard` e verificados. As cópias no Drive são descartadas. |
| Modo de abertura dos links | Varia por documento | Ver seção 6. Evita edição acidental de modelo. |
| Higienização das URLs | Remover parâmetros | Ver seção 7. |
| Itens sem documento | Card visível | Mostra o processo completo. Clique exibe aviso de que está em elaboração. |
| Arquitetura do arquivo | HTML único autocontido | Sem dependência externa. Funciona no GitHub Pages, por duplo-clique local, ou anexado em e-mail. |

### Limitação técnica registrada

O Google Drive **não serve arquivos `.html` como página web** — ao abrir, mostra o
código cru ou força download. Por isso o TAP e a EAP apontam para o GitHub Pages,
não para o Drive.

## 4. Estrutura da página

```
┌─ HEADER (fixo) ──────────────────────────────────┐
│  [logo IDG]  Escritório de Projetos              │
│              Priorização · Execução  ← atalhos   │
└──────────────────────────────────────────────────┘

╔═ 1. PRIORIZAÇÃO DE INICIATIVAS ══════════════════╗
║  grid responsivo, 5 cards                        ║
╚══════════════════════════════════════════════════╝

╔═ 2. EXECUÇÃO ════════════════════════════════════╗
║ ┌ Inicialização ─┐ ┌ Execução e ──┐ ┌ Encerra- ┐ ║
║ │ 3 cards        │ │ Acompanham.  │ │ mento    │ ║
║ │                │ │ 2 cards      │ │ 1 card   │ ║
║ └────────────────┘ └──────────────┘ └──────────┘ ║
╚══════════════════════════════════════════════════╝

┌─ RODAPÉ ─────────────────────────────────────────┐
│  Ver todos os documentos no Drive →              │
└──────────────────────────────────────────────────┘
```

Navegação por scroll, sem abas — o valor do painel é enxergar o processo inteiro
de uma vez. As três colunas de Execução empilham no celular.

O rodapé aponta para a pasta raiz do EGP no Drive; funciona como saída de
emergência se algum link individual quebrar.

## 5. Inventário de documentos

| # | Seção | Coluna | Documento | Tipo | Modo |
|---|---|---|---|---|---|
| 1 | Priorização | — | POP — EGP | `docs` | leitura |
| 2 | Priorização | — | Manual POP — EGP | `slides` | leitura |
| 3 | Priorização | — | Avaliação de Novos Projetos \| Questionário para Clientes | `form` | resposta |
| 4 | Priorização | — | Matriz de Priorização de Iniciativas | `sheets` | cópia |
| 5 | Priorização | — | Proposta Técnica | `pendente` | — |
| 6 | Execução | Inicialização | Descrição de Cargos e Responsabilidades | `sheets` | cópia |
| 7 | Execução | Inicialização | TAP — Termo de Abertura de Projetos | `github` | leitura |
| 8 | Execução | Inicialização | EAP — Estrutura Analítica do Projeto | `github` | leitura |
| 9 | Execução | Execução e Acompanhamento | Cronograma | `sheets` | edição |
| 10 | Execução | Execução e Acompanhamento | Dashboard | `github` | leitura |
| 11 | Execução | Encerramento | Termo de Encerramento de Projeto | `form` | resposta |

### URLs finais

| # | URL |
|---|---|
| 1 | `https://docs.google.com/document/d/1YYoGIT9s34Txhs8k8zpCOZsYJ18BmtYK/preview` |
| 2 | `https://docs.google.com/presentation/d/156wx0v6wYtDoLj_tQiOwI4yUPtGiSwxo/preview` |
| 3 | `https://docs.google.com/forms/d/e/1FAIpQLSclKlTxalex02NyKSXyY5sUrIR-2uoltX1T7Ov6XTULwEaieQ/viewform` |
| 4 | `https://docs.google.com/spreadsheets/d/1fpSMs4wRW-Vw7HbNPSSgpTjDCLHzCkj_KIHhjmeltG8/copy` |
| 5 | — |
| 6 | `https://docs.google.com/spreadsheets/d/1mOiYnk7FwkGxxNx1CDymOPelk-_2qoUs6Sx5QjClnxg/copy` |
| 7 | `https://pmo-creator.github.io/maz-dashboard/TAP%20Html/TAP_Museu_das_Amazonias_v1.html` |
| 8 | `https://pmo-creator.github.io/maz-dashboard/EAP/EAP_MAZ_2026_v1.html` |
| 9 | `https://docs.google.com/spreadsheets/d/17nttJ_ShqWztvDWH3l59iNqboLqkviZs3_PM5J3ihdA/edit?gid=1451384064#gid=1451384064` |
| 10 | `https://pmo-creator.github.io/maz-dashboard/` |
| 11 | `https://docs.google.com/forms/d/e/1FAIpQLSfCdvuDSs9wOpyRjmKQxUYeYLDMWa9iNnB7r6FQgCEnhzoFVg/viewform` |
| rodapé | `https://drive.google.com/drive/folders/1BI_90zsQ3qYxtsCK5bfD9yMsYXn5SIoO` |

URLs 7, 8 e 10 verificadas em 28/07/2026 — respondem HTTP 200. As demais exigem
login e serão verificadas manualmente na implementação.

**Observação sobre o card 9:** o Cronograma aponta para a planilha do Museu das
Amazônias — é o único cronograma que existe hoje. Decisão consciente: o painel
serve na prática agora, e a URL troca no bloco `DOCS` quando houver um modelo em
branco.

## 6. Modos de abertura dos links

Cada documento abre no modo adequado ao seu papel. Isso evita o cenário em que
alguém preenche o modelo original com os dados do próprio projeto e sobrescreve
o documento de referência do EGP.

| Modo | Sufixo da URL | Aplicado a | Efeito |
|---|---|---|---|
| Leitura | `/preview` | POP, Manual POP | Abre limpo, sem barra de edição. Sem risco de alteração acidental. |
| Cópia | `/copy` | Matriz de Priorização, Descrição de Cargos | Google abre a caixa "Fazer uma cópia". A pessoa trabalha no arquivo dela; o modelo fica intacto. |
| Edição | `/edit` | Cronograma | Arquivo vivo, editado colaborativamente. |
| Resposta | `/viewform` | Avaliação de Novos Projetos, Termo de Encerramento | Formulário no modo de preenchimento. O link `/edit` de um Forms concede acesso de edição ao formulário e **nunca** deve ser usado. |

**Pré-requisito do modo cópia:** o arquivo precisa estar compartilhado com pelo
menos permissão de leitura para o público-alvo. Se estiver "Restrito", o `/copy`
falha com erro de permissão.

## 7. Higienização das URLs

Os links de compartilhamento do Google carregam parâmetros que serão removidos
antes de entrar no painel:

| Parâmetro | Motivo da remoção |
|---|---|
| `ouid=116856226265398514115` | Identificador da conta Google pessoal do usuário. O repositório é público — o parâmetro ficaria exposto e indexável. |
| `usp=sharing` | Telemetria de origem. Sem efeito funcional. |
| `rtpof=true`, `sd=true` | Metadados de arquivo Office importado. Sem efeito funcional. |
| `pli=1` | Estado de sessão. Sem efeito funcional. |

O parâmetro `gid` do Cronograma é **mantido** — ele seleciona a aba correta da
planilha.

## 8. Bloco de configuração

Único ponto de manutenção do painel. Fica no topo do `<script>`, delimitado por
comentário visível:

```js
/* ═══ EDITE AQUI: links dos documentos ═══ */
const DOCS = {
  popEgp: {
    nome:  'POP — EGP',
    desc:  'Procedimento operacional padrão do Escritório de Projetos',
    icone: '📋',
    url:   'https://docs.google.com/document/d/1YYo.../preview',
    tipo:  'docs'   // docs | slides | sheets | form | github | pendente
  },
  // ...
};
```

O campo `tipo` controla o badge de formato e o comportamento do card. Para ativar
um card pendente, basta trocar `url` e `tipo` — nenhuma outra alteração no arquivo.

## 9. Anatomia do card

```
┌────────────────────────────────────┐
│ 📑                    [GitHub]     │  ícone + badge do formato
│ TAP — Termo de Abertura            │  nome
│ Formaliza o início do projeto,     │  descrição (1–2 linhas)
│ escopo e responsáveis              │
│                          Abrir →   │  ação
└────────────────────────────────────┘
```

- Card inteiro clicável, abre em nova aba (`target="_blank" rel="noopener"`)
- Card `pendente`: cinza, badge "Em elaboração", clique exibe toast discreto
  ("Documento em elaboração — em breve disponível"). Nunca `alert()`.
- Card de modo cópia: microtexto "abre uma cópia para você" abaixo da ação, para
  que o comportamento não surpreenda
- Hover: elevação leve e borda em `--accent`, seguindo o padrão do dashboard MAZ

## 10. Identidade visual

Estrutura, tipografia e comportamento herdados do dashboard MAZ. Paleta trocada
para tom institucional neutro, já que o painel serve todos os projetos do IDG.

| Token | MAZ | EGP · IDG |
|---|---|---|
| `--bg` (header) | `#1E2E0D` | `#1E293B` grafite |
| `--accent` | `#8AC43A` | `#3B82F6` azul |
| `--card` | `#FFFFFF` | `#FFFFFF` |
| `--surface` | `#EEF3E8` | `#F1F5F9` |
| `--border` | `#E2E8F0` | `#E2E8F0` |
| `--text` | `#1E293B` | `#1E293B` |
| `--muted` | `#64748B` | `#64748B` |
| `--radius` | `10px` | `10px` |

Fonte: `-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif`.

Cores dos badges por tipo: `docs` azul · `slides` laranja · `sheets` verde ·
`form` roxo · `github` grafite · `pendente` cinza.

**Logo:** `logotipo-idg.svg` — marca completa com assinatura "Instituto de
Desenvolvimento e Gestão", vetorial, `viewBox="0 0 956.3 329.2"`. O SVG é
**embutido inline** no HTML, não referenciado por `<img>`. Isso mantém o painel
autocontido e permite recolorir a marca por CSS.

A marca usa `.st0 { fill: #1d1d1b }` (preto) definido em `<style>` interno do
SVG. Como o header é escuro, o painel sobrescreve com maior especificidade:

```css
.logo svg .st0 { fill: #FFFFFF; }
```

Preferível a `filter: invert(1)`, que também inverteria qualquer outro elemento
e degrada em telas de alto contraste.

O arquivo `logo-idg.png` na pasta é um diagrama "Risco × Problema" sem relação
com o painel — não é usado. Remover mediante confirmação do usuário.

## 11. Estrutura de arquivos

```
Painel de PMO MAZ/          → construção local
  index.html                → o painel (arquivo único, SVG do logo embutido)
  logotipo-idg.svg          → fonte do logo (não referenciado em runtime)
  logo-idg.png              → arquivo alheio, remover mediante confirmação
  docs/superpowers/specs/   → este documento
```

O repositório `egp-idg` conterá apenas `index.html` e `README.md`. Nenhum
documento é hospedado nele: TAP, EAP e Dashboard permanecem no `maz-dashboard`,
onde já estão publicados, e os demais permanecem no Drive.

Publicação no repo `egp-idg` acontece apenas mediante instrução explícita do
usuário — sem `git add`, `commit` ou `push` automáticos.

## 12. Como inserir o link de um Google Forms

Para quando a Proposta Técnica ou qualquer formulário novo entrar no painel:

1. Abrir o formulário em modo de edição
2. Botão **Enviar**, canto superior direito
3. Aba do ícone 🔗
4. Marcar "Encurtar URL" e copiar

O link resultante é o de resposta e é o que vai no painel. O link terminado em
`/edit` **nunca** deve ser usado — ele concede acesso de edição ao formulário.

## 13. Critérios de aceite

- [ ] Os 11 cards renderizam nas seções e colunas corretas
- [ ] Cada card abre o destino correto em nova aba
- [ ] Cards de modo cópia abrem a caixa "Fazer uma cópia", não o arquivo original
- [ ] Cards de modo leitura abrem sem barra de edição
- [ ] Card `pendente` (Proposta Técnica) exibe toast e não navega
- [ ] Nenhuma URL do painel contém `ouid`, `usp`, `rtpof`, `sd` ou `pli`
- [ ] Logo legível sobre o header escuro
- [ ] Layout legível em 1920px, 1366px e 390px (celular)
- [ ] Arquivo abre por duplo-clique, sem servidor, sem erro no console
- [ ] Bloco `DOCS` é o único lugar a editar para trocar qualquer link
- [ ] Nenhuma dependência externa (CDN, fonte remota, script de terceiros)

## 14. Riscos conhecidos

| Risco | Mitigação |
|---|---|
| `/preview` pode não funcionar em documento Office importado | Verificar manualmente os cards 1 e 2 na implementação. Se falhar, volta para `/edit`. |
| Documento com permissão "Restrito" quebra o modo cópia | Verificar o compartilhamento de cada arquivo antes de publicar. |
| Documento movido no Drive quebra o link | Link aponta para o arquivo, não para a pasta. Rodapé com a pasta raiz serve de fallback. |
| Repo público expõe os IDs dos documentos | IDs sozinhos não dão acesso — a permissão continua controlada pelo Drive. Nenhum documento é hospedado no `egp-idg`. |
| Pasta `TAP Html` tem espaço no nome | URL exige `%20`. Funciona hoje; renomear quebraria links existentes — não mexer sem decisão explícita. |
| Cronograma aponta para o projeto MAZ | Documentado na seção 5. Trocar quando existir modelo em branco. |
| Documentos do Drive restritos ao grupo IDG | Confirmado: acesso geral "IDG — Leitor". Quem não tem conta `@idg.org.br` não abre nenhum card do Drive. O painel é ferramenta interna; comportamento esperado, não é defeito. |
| Opção "impedir download, impressão e cópia" bloqueia o modo cópia | Se o proprietário marcou essa opção avançada, `/copy` falha mesmo com permissão de Leitor. Verificar cards 4 e 6 na implementação. |

### Observação fora do escopo

A EAP publicada possui controles de edição (`+ Grupo`, `+ Tarefa`, `Salvar nova
versão`), mas **salva apenas localmente** — confirmado pelo usuário em
28/07/2026. As edições ficam no navegador de quem editou e não chegam a mais
ninguém.

Isso não afeta o painel, cujo botão apenas abre a página. Mas tem consequência
de uso: a EAP serve como visualização e como rascunho individual, não como
ferramenta colaborativa. Se o EGP for orientar o time a preencher a EAP pelo
painel, isso precisa estar dito — caso contrário duas pessoas preenchem versões
divergentes sem saber.

Fica registrado como decisão de produto pendente, fora do escopo desta
implementação.
