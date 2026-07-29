# Painel EGP · IDG — Plano de Implementação

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Construir um painel HTML de página única que indexa os 11 documentos do Escritório de Gestão de Projetos do IDG, cada um abrindo em nova aba no modo adequado ao seu papel.

**Architecture:** Arquivo único `index.html` autocontido — CSS e JS inline, logo SVG embutido, zero dependência externa. Os cards são gerados por JavaScript a partir de um objeto `DOCS` declarado no topo do script, que é o único ponto de manutenção do painel.

**Tech Stack:** HTML5, CSS3 (custom properties, grid, flexbox), JavaScript ES6 sem framework. Python 3 para as edições no arquivo (regra do CLAUDE.md). Node.js para validação de sintaxe.

**Spec:** `docs/superpowers/specs/2026-07-28-painel-egp-idg-design.md`

---

## Sobre testes neste plano

Este projeto não tem — e não deve ter — suíte de testes automatizados. São 11 cards estáticos gerados por uma função de template; montar Jest ou Playwright para isso custaria mais manutenção do que o painel inteiro, e o usuário do projeto não é desenvolvedor.

No lugar de TDD, cada tarefa termina com **verificação executável**: um comando com saída esperada explícita. Onde a verificação é visual, o passo diz exatamente o que olhar. Nenhuma tarefa é dada como concluída sem a verificação passar.

## Sobre commits

O `CLAUDE.md` do projeto proíbe `git add`, `git commit` e `git push` sem instrução explícita do usuário. Esta regra tem precedência sobre a prática de commits frequentes.

Portanto: **nenhuma tarefa executa git.** Os comandos ficam reunidos na Tarefa 9, a ser executada apenas mediante autorização.

## Diretório de trabalho

Todos os caminhos são relativos a:

```
C:\Users\gagui\Github\maz-dashboard\Painel de PMO MAZ\
```

## Estrutura do arquivo

`index.html` é um arquivo só, por requisito do spec (precisa funcionar por duplo-clique, sem servidor). A organização interna é por blocos comentados, na ordem:

| Bloco | Responsabilidade |
|---|---|
| `<style>` | Tokens de cor, layout, componentes |
| `<header>` | Logo SVG inline + navegação por âncora |
| `<main>` | Dois `<section>` vazios, preenchidos por JS |
| `DOCS` | Dados dos 11 documentos — único ponto de manutenção |
| `SECOES` | Estrutura das seções e colunas |
| funções de render | `cardHTML`, `renderPainel`, `mostrarToast` |

---

## Task 1: Esqueleto do arquivo e tokens visuais

**Files:**
- Create: `index.html`

- [ ] **Step 1: Criar o arquivo com estrutura e CSS base**

Criar `index.html` com este conteúdo:

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Escritório de Projetos · IDG</title>
<style>
:root{
  --bg:#1E293B;
  --accent:#3B82F6;
  --card:#FFFFFF;
  --surface:#F1F5F9;
  --border:#E2E8F0;
  --text:#1E293B;
  --muted:#64748B;
  --radius:10px;
  --gap:32px;
  --badge-docs:#2563EB;
  --badge-slides:#EA580C;
  --badge-sheets:#16A34A;
  --badge-form:#7C3AED;
  --badge-github:#334155;
  --badge-pendente:#94A3B8;
}
*{box-sizing:border-box;margin:0;padding:0}
body{
  font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,sans-serif;
  background:var(--surface);
  color:var(--text);
  line-height:1.5;
}
.wrap{max-width:1280px;margin:0 auto;padding:0 24px}

/* ── Header ── */
header{
  background:var(--bg);
  color:#fff;
  padding:20px 0;
  position:sticky;
  top:0;
  z-index:100;
  box-shadow:0 1px 3px rgba(0,0,0,.2);
}
.header-in{display:flex;align-items:center;gap:24px;flex-wrap:wrap}
.logo{flex:0 0 auto}
.logo svg{height:44px;width:auto;display:block}
.logo svg .st0{fill:#FFFFFF}
.header-txt{flex:1 1 auto;min-width:200px}
.header-txt h1{font-size:20px;font-weight:600;letter-spacing:-.01em}
.header-txt p{font-size:13px;color:#94A3B8;margin-top:2px}
.nav{display:flex;gap:8px;flex:0 0 auto}
.nav a{
  color:#CBD5E1;
  text-decoration:none;
  font-size:13px;
  padding:6px 14px;
  border-radius:999px;
  border:1px solid #334155;
  transition:.15s;
}
.nav a:hover{color:#fff;border-color:var(--accent);background:rgba(59,130,246,.12)}

/* ── Seções ── */
.secao{padding:48px 0 8px}
.secao-head{display:flex;align-items:baseline;gap:12px;margin-bottom:8px}
.secao-num{
  font-size:13px;font-weight:700;color:#fff;background:var(--bg);
  width:26px;height:26px;border-radius:50%;
  display:flex;align-items:center;justify-content:center;flex:0 0 auto;
}
.secao h2{font-size:24px;font-weight:650;letter-spacing:-.02em}
.secao-sub{color:var(--muted);font-size:14px;margin:0 0 24px 38px}
</style>
</head>
<body>

<header>
  <div class="wrap header-in">
    <div class="logo"><!--LOGO_SVG--></div>
    <div class="header-txt">
      <h1>Escritório de Projetos</h1>
      <p>Documentos e modelos do EGP · Instituto de Desenvolvimento e Gestão</p>
    </div>
    <nav class="nav">
      <a href="#priorizacao">Priorização</a>
      <a href="#execucao">Execução</a>
    </nav>
  </div>
</header>

<main class="wrap" id="painel"></main>

<script>
/* preenchido nas próximas tarefas */
</script>
</body>
</html>
```

- [ ] **Step 2: Verificar que o arquivo abre sem erro**

Rodar:

```bash
cd "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ" && python -c "
import io
h = io.open('index.html', encoding='utf-8').read()
assert '<!--LOGO_SVG-->' in h, 'marcador do logo ausente'
assert h.count('<style>') == 1 and h.count('</style>') == 1, 'style malformado'
assert h.count('<script>') == 1 and h.count('</script>') == 1, 'script malformado'
print('OK - estrutura valida,', len(h), 'bytes')
"
```

Saída esperada: `OK - estrutura valida, ~3000 bytes`

---

## Task 2: Embutir o logo SVG

**Files:**
- Modify: `index.html` (substitui o marcador `<!--LOGO_SVG-->`)
- Read: `logotipo-idg.svg`

O SVG tem ~5 KB de path data. Não deve ser digitado à mão — o script abaixo lê o arquivo e injeta o conteúdo.

- [ ] **Step 1: Injetar o SVG no lugar do marcador**

Rodar:

```bash
cd "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ" && python -c "
import io, re
svg = io.open('logotipo-idg.svg', encoding='utf-8').read()
# remove o preambulo XML e o comentario do Illustrator
svg = re.sub(r'<\?xml[^>]*\?>\s*', '', svg)
svg = re.sub(r'<!--.*?-->', '', svg, flags=re.S).strip()
h = io.open('index.html', encoding='utf-8').read()
assert '<!--LOGO_SVG-->' in h, 'marcador ja substituido'
h = h.replace('<!--LOGO_SVG-->', svg)
io.open('index.html','w',encoding='utf-8',newline='').write(h)
print('OK - svg embutido,', len(svg), 'bytes')
"
```

Saída esperada: `OK - svg embutido, ~11000 bytes`

- [ ] **Step 2: Verificar que o SVG está íntegro e recolorível**

Rodar:

```bash
cd "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ" && python -c "
import io
h = io.open('index.html', encoding='utf-8').read()
assert '<?xml' not in h, 'preambulo xml vazou para dentro do html'
assert 'viewBox=\"0 0 956.3 329.2\"' in h, 'viewBox do logo ausente'
assert 'class=\"st0\"' in h, 'classe st0 ausente - regra de recolorir nao pega'
assert h.count('</svg>') == 1, 'svg malformado'
print('OK - logo integro')
"
```

Saída esperada: `OK - logo integro`

- [ ] **Step 3: Confirmar visualmente que o logo aparece branco**

Abrir o arquivo no navegador:

```bash
start "" "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ/index.html"
```

Olhar: a marca `idg` com a assinatura "Instituto de Desenvolvimento e Gestão" deve aparecer **em branco** sobre a faixa grafite do topo, à esquerda de "Escritório de Projetos". Se aparecer preta ou invisível, a regra `.logo svg .st0{fill:#FFFFFF}` não está vencendo o `<style>` interno do SVG — nesse caso trocar por `.logo svg path{fill:#FFFFFF !important}`.

---

## Task 3: Bloco DOCS com os 11 documentos

**Files:**
- Modify: `index.html` (dentro do `<script>`)

- [ ] **Step 1: Inserir o bloco de dados**

Rodar o script abaixo, que substitui o comentário placeholder do `<script>`:

```bash
cd "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ" && python - <<'PY'
import io
BLOCO = r"""
/* ═══════════════════════════════════════════════════════════
   EDITE AQUI — links dos documentos do EGP

   tipo:  docs | slides | sheets | form | github | pendente
   modo:  texto exibido no card sobre o que acontece ao clicar
   ═══════════════════════════════════════════════════════════ */
const DOCS = {
  popEgp: {
    nome:'POP — EGP',
    desc:'Procedimento operacional padrão do Escritório de Projetos',
    icone:'📋', tipo:'docs', modo:'leitura',
    url:'https://docs.google.com/document/d/1YYoGIT9s34Txhs8k8zpCOZsYJ18BmtYK/preview'
  },
  manualPop: {
    nome:'Manual POP — EGP',
    desc:'Apresentação que explica como aplicar o POP na prática',
    icone:'📕', tipo:'slides', modo:'leitura',
    url:'https://docs.google.com/presentation/d/156wx0v6wYtDoLj_tQiOwI4yUPtGiSwxo/preview'
  },
  avaliacao: {
    nome:'Avaliação de Novos Projetos',
    desc:'Questionário para clientes na triagem de novas iniciativas',
    icone:'📝', tipo:'form', modo:'preencher',
    url:'https://docs.google.com/forms/d/e/1FAIpQLSclKlTxalex02NyKSXyY5sUrIR-2uoltX1T7Ov6XTULwEaieQ/viewform'
  },
  matriz: {
    nome:'Matriz de Priorização de Iniciativas',
    desc:'Compara e ordena iniciativas por critérios de valor e esforço',
    icone:'📊', tipo:'sheets', modo:'copia',
    url:'https://docs.google.com/spreadsheets/d/1fpSMs4wRW-Vw7HbNPSSgpTjDCLHzCkj_KIHhjmeltG8/copy'
  },
  propostaTecnica: {
    nome:'Proposta Técnica',
    desc:'Documento de proposta para o cliente',
    icone:'📄', tipo:'pendente', modo:'',
    url:''
  },
  cargos: {
    nome:'Descrição de Cargos e Responsabilidades',
    desc:'Define papéis e responsabilidades da equipe do projeto',
    icone:'👥', tipo:'sheets', modo:'copia',
    url:'https://docs.google.com/spreadsheets/d/1mOiYnk7FwkGxxNx1CDymOPelk-_2qoUs6Sx5QjClnxg/copy'
  },
  tap: {
    nome:'TAP — Termo de Abertura de Projetos',
    desc:'Formaliza o início do projeto, escopo, prazos e responsáveis',
    icone:'📑', tipo:'github', modo:'leitura',
    url:'https://pmo-creator.github.io/maz-dashboard/TAP%20Html/TAP_Museu_das_Amazonias_v1.html'
  },
  eap: {
    nome:'EAP — Estrutura Analítica do Projeto',
    desc:'Decompõe o projeto em eixos, marcos e tarefas',
    icone:'🌳', tipo:'github', modo:'leitura',
    url:'https://pmo-creator.github.io/maz-dashboard/EAP/EAP_MAZ_2026_v1.html'
  },
  cronograma: {
    nome:'Cronograma',
    desc:'Planilha de datas, responsáveis e status das entregas',
    icone:'📅', tipo:'sheets', modo:'edicao',
    url:'https://docs.google.com/spreadsheets/d/17nttJ_ShqWztvDWH3l59iNqboLqkviZs3_PM5J3ihdA/edit?gid=1451384064#gid=1451384064'
  },
  dashboard: {
    nome:'Dashboard',
    desc:'Painel de acompanhamento do cronograma e das requisições',
    icone:'📈', tipo:'github', modo:'leitura',
    url:'https://pmo-creator.github.io/maz-dashboard/'
  },
  encerramento: {
    nome:'Termo de Encerramento de Projeto',
    desc:'Formulário de fechamento com lições aprendidas',
    icone:'✅', tipo:'form', modo:'preencher',
    url:'https://docs.google.com/forms/d/e/1FAIpQLSfCdvuDSs9wOpyRjmKQxUYeYLDMWa9iNnB7r6FQgCEnhzoFVg/viewform'
  }
};

const PASTA_DRIVE = 'https://drive.google.com/drive/folders/1BI_90zsQ3qYxtsCK5bfD9yMsYXn5SIoO';

const SECOES = [
  {
    id:'priorizacao', num:'1', titulo:'Priorização de Iniciativas',
    sub:'Avaliação e seleção de novas iniciativas antes da aprovação',
    colunas:[ {titulo:null, docs:['popEgp','manualPop','avaliacao','matriz','propostaTecnica']} ]
  },
  {
    id:'execucao', num:'2', titulo:'Execução',
    sub:'Documentos do ciclo de vida do projeto aprovado',
    colunas:[
      {titulo:'Inicialização', docs:['cargos','tap','eap']},
      {titulo:'Execução e Acompanhamento', docs:['cronograma','dashboard']},
      {titulo:'Encerramento', docs:['encerramento']}
    ]
  }
];
"""
h = io.open('index.html', encoding='utf-8').read()
alvo = '/* preenchido nas próximas tarefas */'
assert alvo in h, 'placeholder do script nao encontrado'
h = h.replace(alvo, BLOCO.strip())
io.open('index.html','w',encoding='utf-8',newline='').write(h)
print('OK - bloco DOCS inserido')
PY
```

Saída esperada: `OK - bloco DOCS inserido`

- [ ] **Step 2: Validar a sintaxe do JavaScript**

`node --check` não aceita `.html` — extrair o script e validar (armadilha documentada no CLAUDE.md):

```bash
cd "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ" && python -c "
import io, re
h = io.open('index.html', encoding='utf-8').read()
js = re.search(r'<script>(.*?)</script>', h, re.S).group(1)
io.open('_tmp_check.js','w',encoding='utf-8').write(js)
print('extraido', len(js), 'bytes')
" && node --check _tmp_check.js && echo "JS OK"
```

Saída esperada: `extraido ~3800 bytes` seguido de `JS OK`

- [ ] **Step 3: Verificar que nenhuma URL carrega parâmetro indevido**

Critério de aceite do spec — nenhuma URL pode conter `ouid`, `usp`, `rtpof`, `sd=` ou `pli`:

```bash
cd "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ" && python -c "
import io, re
h = io.open('index.html', encoding='utf-8').read()
urls = re.findall(r\"url:'([^']*)'\", h)
sujos = [p for p in ['ouid=','usp=','rtpof=','sd=true','pli='] if any(p in u for u in urls)]
assert not sujos, 'parametro indevido encontrado: %s' % sujos
assert len(urls) == 11, 'esperava 11 urls, achei %d' % len(urls)
print('OK -', len(urls), 'urls limpas')
"
```

Saída esperada: `OK - 11 urls limpas`

- [ ] **Step 4: Remover o arquivo temporário**

Regra 8 do CLAUDE.md — scripts temporários são apagados imediatamente após uso.

```bash
cd "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ" && rm -f _tmp_check.js && echo "limpo"
```

---

## Task 4: CSS dos cards e das colunas

**Files:**
- Modify: `index.html` (bloco `<style>`)

- [ ] **Step 1: Acrescentar o CSS dos componentes**

Rodar:

```bash
cd "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ" && python - <<'PY'
import io
CSS = r"""
/* ── Colunas ── */
.colunas{display:grid;grid-template-columns:repeat(3,1fr);gap:var(--gap);align-items:start}
.coluna-head{
  font-size:12px;font-weight:700;text-transform:uppercase;letter-spacing:.06em;
  color:var(--muted);padding-bottom:10px;margin-bottom:16px;
  border-bottom:2px solid var(--border);
}
.grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(280px,1fr));gap:20px}
.coluna .grid{grid-template-columns:1fr}

/* ── Card ── */
.card{
  background:var(--card);
  border:1px solid var(--border);
  border-radius:var(--radius);
  padding:20px;
  display:flex;flex-direction:column;gap:8px;
  text-decoration:none;color:inherit;
  transition:.15s;
  min-height:150px;
}
.card:hover{
  border-color:var(--accent);
  box-shadow:0 4px 12px rgba(30,41,59,.10);
  transform:translateY(-2px);
}
.card-top{display:flex;align-items:flex-start;justify-content:space-between;gap:12px}
.card-icone{font-size:26px;line-height:1}
.badge{
  font-size:10px;font-weight:700;text-transform:uppercase;letter-spacing:.05em;
  color:#fff;padding:3px 9px;border-radius:999px;white-space:nowrap;
}
.badge.docs{background:var(--badge-docs)}
.badge.slides{background:var(--badge-slides)}
.badge.sheets{background:var(--badge-sheets)}
.badge.form{background:var(--badge-form)}
.badge.github{background:var(--badge-github)}
.badge.pendente{background:var(--badge-pendente)}
.card-nome{font-size:15px;font-weight:600;letter-spacing:-.01em}
.card-desc{font-size:13px;color:var(--muted);flex:1 1 auto}
.card-acao{
  font-size:13px;font-weight:600;color:var(--accent);
  display:flex;align-items:center;gap:5px;margin-top:4px;
}
.card:hover .card-acao{gap:9px}
.card-nota{font-size:11px;color:var(--muted);font-style:italic}

/* ── Card pendente ── */
.card.pendente{background:var(--surface);border-style:dashed;cursor:default}
.card.pendente:hover{border-color:var(--border);box-shadow:none;transform:none}
.card.pendente .card-nome,.card.pendente .card-desc{color:var(--muted)}
.card.pendente .card-icone{opacity:.5}
.card.pendente .card-acao{color:var(--muted)}

/* ── Rodapé ── */
footer{margin-top:56px;padding:28px 0 40px;border-top:1px solid var(--border)}
.footer-in{display:flex;justify-content:space-between;align-items:center;gap:16px;flex-wrap:wrap}
footer a{color:var(--accent);text-decoration:none;font-size:14px;font-weight:500}
footer a:hover{text-decoration:underline}
footer small{color:var(--muted);font-size:12px}

/* ── Toast ── */
#toast{
  position:fixed;left:50%;bottom:32px;transform:translate(-50%,16px);
  background:var(--bg);color:#fff;font-size:14px;
  padding:13px 22px;border-radius:var(--radius);
  box-shadow:0 8px 24px rgba(30,41,59,.28);
  opacity:0;pointer-events:none;transition:.2s;z-index:200;
}
#toast.on{opacity:1;transform:translate(-50%,0)}

/* ── Responsivo ── */
@media(max-width:900px){
  .colunas{grid-template-columns:1fr;gap:36px}
  .secao{padding:36px 0 8px}
}
@media(max-width:600px){
  .wrap{padding:0 16px}
  .header-in{gap:14px}
  .logo svg{height:34px}
  .header-txt h1{font-size:17px}
  .header-txt p{display:none}
  .nav{width:100%}
  .nav a{flex:1;text-align:center}
  .secao h2{font-size:20px}
  .secao-sub{margin-left:0}
}
"""
h = io.open('index.html', encoding='utf-8').read()
alvo = '</style>'
assert alvo in h, 'tag de fechamento do style nao encontrada'
h = h.replace(alvo, CSS.strip() + '\n</style>', 1)
io.open('index.html','w',encoding='utf-8',newline='').write(h)
print('OK - css dos componentes inserido')
PY
```

Saída esperada: `OK - css dos componentes inserido`

- [ ] **Step 2: Verificar integridade do CSS**

```bash
cd "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ" && python -c "
import io, re
h = io.open('index.html', encoding='utf-8').read()
css = re.search(r'<style>(.*?)</style>', h, re.S).group(1)
assert css.count('{') == css.count('}'), 'chaves desbalanceadas no css'
for c in ['.card','.badge.sheets','#toast','.colunas','.card.pendente']:
    assert c in css, 'seletor ausente: ' + c
print('OK - css balanceado,', len(css), 'bytes')
"
```

Saída esperada: `OK - css balanceado, ~5000 bytes`

---

## Task 5: Renderização dos cards

**Files:**
- Modify: `index.html` (bloco `<script>`)

- [ ] **Step 1: Inserir as funções de render**

Rodar:

```bash
cd "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ" && python - <<'PY'
import io
JS = r"""

/* ═══ Render — não precisa editar daqui para baixo ═══ */

const ROTULO_TIPO = {
  docs:'Docs', slides:'Slides', sheets:'Sheets',
  form:'Formulário', github:'Web', pendente:'Em elaboração'
};

const NOTA_MODO = {
  copia:'abre uma cópia para você preencher',
  edicao:'abre o arquivo em uso pela equipe',
  leitura:'', preencher:'', '':''
};

function escH(s){
  return String(s).replace(/[&<>"']/g, c => (
    {'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[c]
  ));
}

function cardHTML(chave){
  const d = DOCS[chave];
  if(!d) return '';
  const badge = `<span class="badge ${d.tipo}">${escH(ROTULO_TIPO[d.tipo] || d.tipo)}</span>`;
  const nota  = NOTA_MODO[d.modo] ? `<span class="card-nota">${escH(NOTA_MODO[d.modo])}</span>` : '';

  const miolo = `
      <div class="card-top"><span class="card-icone">${d.icone}</span>${badge}</div>
      <span class="card-nome">${escH(d.nome)}</span>
      <span class="card-desc">${escH(d.desc)}</span>
      ${nota}`;

  if(d.tipo === 'pendente' || !d.url){
    return `<div class="card pendente" data-pendente="1" tabindex="0" role="button">
      ${miolo}
      <span class="card-acao">Em elaboração</span>
    </div>`;
  }
  return `<a class="card" href="${escH(d.url)}" target="_blank" rel="noopener">
      ${miolo}
      <span class="card-acao">Abrir <span aria-hidden="true">→</span></span>
    </a>`;
}

function renderPainel(){
  const html = SECOES.map(sec => {
    const temColunas = sec.colunas.length > 1 || sec.colunas[0].titulo;
    const corpo = temColunas
      ? `<div class="colunas">${sec.colunas.map(col => `
          <div class="coluna">
            <div class="coluna-head">${escH(col.titulo)}</div>
            <div class="grid">${col.docs.map(cardHTML).join('')}</div>
          </div>`).join('')}</div>`
      : `<div class="grid">${sec.colunas[0].docs.map(cardHTML).join('')}</div>`;

    return `<section class="secao" id="${sec.id}">
      <div class="secao-head">
        <span class="secao-num">${sec.num}</span>
        <h2>${escH(sec.titulo)}</h2>
      </div>
      <p class="secao-sub">${escH(sec.sub)}</p>
      ${corpo}
    </section>`;
  }).join('');

  document.getElementById('painel').innerHTML = html + `
    <footer>
      <div class="footer-in">
        <a href="${escH(PASTA_DRIVE)}" target="_blank" rel="noopener">
          Ver todos os documentos no Drive →
        </a>
        <small>Escritório de Gestão de Projetos · IDG</small>
      </div>
    </footer>`;
}

let toastTimer = null;
function mostrarToast(msg){
  const t = document.getElementById('toast');
  t.textContent = msg;
  t.classList.add('on');
  clearTimeout(toastTimer);
  toastTimer = setTimeout(() => t.classList.remove('on'), 2600);
}

document.addEventListener('click', e => {
  if(e.target.closest('[data-pendente]')){
    mostrarToast('Documento em elaboração — em breve disponível');
  }
});

document.addEventListener('keydown', e => {
  if((e.key === 'Enter' || e.key === ' ') && e.target.matches('[data-pendente]')){
    e.preventDefault();
    mostrarToast('Documento em elaboração — em breve disponível');
  }
});

renderPainel();
"""
h = io.open('index.html', encoding='utf-8').read()
alvo = '</script>'
assert alvo in h, 'tag de fechamento do script nao encontrada'
h = h.replace(alvo, JS.rstrip() + '\n</script>', 1)
# elemento do toast
h = h.replace('</main>', '</main>\n<div id="toast" role="status" aria-live="polite"></div>', 1)
if '<div id="toast"' not in h:
    h = h.replace('<main class="wrap" id="painel"></main>',
                  '<main class="wrap" id="painel"></main>\n<div id="toast" role="status" aria-live="polite"></div>', 1)
io.open('index.html','w',encoding='utf-8',newline='').write(h)
print('OK - render inserido')
PY
```

Saída esperada: `OK - render inserido`

- [ ] **Step 2: Validar a sintaxe do JavaScript completo**

```bash
cd "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ" && python -c "
import io, re
h = io.open('index.html', encoding='utf-8').read()
js = re.search(r'<script>(.*?)</script>', h, re.S).group(1)
io.open('_tmp_check.js','w',encoding='utf-8').write(js)
print('extraido', len(js), 'bytes')
" && node --check _tmp_check.js && echo "JS OK" && rm -f _tmp_check.js
```

Saída esperada: `extraido ~7500 bytes`, `JS OK`

Se falhar com erro de template literal, verificar a armadilha do CLAUDE.md: nunca crase dentro de `${}` dentro de outra crase. A função `renderPainel` tem template aninhado dentro de `.map()` — é o ponto mais provável de erro.

- [ ] **Step 3: Verificar que o toast existe no DOM**

```bash
cd "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ" && python -c "
import io
h = io.open('index.html', encoding='utf-8').read()
assert h.count('id=\"toast\"') == 1, 'elemento do toast ausente ou duplicado'
assert 'aria-live=\"polite\"' in h, 'toast sem anuncio para leitor de tela'
print('OK - toast presente')
"
```

Saída esperada: `OK - toast presente`

---

## Task 6: Verificação visual no navegador

**Files:** nenhum — verificação apenas

- [ ] **Step 1: Servir o arquivo localmente**

```bash
cd "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ" && python -m http.server 8010
```

Deixar rodando em background. O painel fica em `http://localhost:8010`.

Porta 8010 e não 8000 — o dashboard MAZ e o mup usam a 8000.

- [ ] **Step 2: Conferir a contagem de cards**

Abrir `http://localhost:8010` e executar no console do navegador:

```js
console.log({
  cards: document.querySelectorAll('.card').length,
  links: document.querySelectorAll('a.card').length,
  pendentes: document.querySelectorAll('.card.pendente').length,
  colunas: document.querySelectorAll('.coluna').length
});
```

Saída esperada: `{cards: 11, links: 10, pendentes: 1, colunas: 3}`

- [ ] **Step 3: Conferir a distribuição por seção**

```js
[...document.querySelectorAll('.secao')].forEach(s =>
  console.log(s.id, s.querySelectorAll('.card').length)
);
```

Saída esperada:
```
priorizacao 5
execucao 6
```

- [ ] **Step 4: Conferir as colunas da seção Execução**

```js
[...document.querySelectorAll('#execucao .coluna')].forEach(c =>
  console.log(c.querySelector('.coluna-head').textContent.trim(),
              '→', c.querySelectorAll('.card').length)
);
```

Saída esperada:
```
Inicialização → 3
Execução e Acompanhamento → 2
Encerramento → 1
```

- [ ] **Step 5: Testar o card pendente**

Clicar no card **Proposta Técnica**. Esperado: aparece uma tarja escura na base da tela com "Documento em elaboração — em breve disponível", que some sozinha em ~2,6 s. A página **não** navega e nenhuma aba nova abre.

- [ ] **Step 6: Testar responsivo**

No DevTools, alternar entre as larguras 1920px, 1366px e 390px.

Esperado:
- **1920 e 1366px** — Priorização em grade de múltiplas colunas; Execução em 3 colunas lado a lado
- **390px** — tudo empilhado em uma coluna, sem barra de rolagem horizontal, logo reduzido, subtítulo do header oculto

Verificar ausência de scroll horizontal:

```js
console.log('overflow horizontal:', document.documentElement.scrollWidth > window.innerWidth);
```

Saída esperada: `overflow horizontal: false`

- [ ] **Step 7: Conferir o console limpo**

O console não deve conter nenhum erro. Avisos de favicon ausente são aceitáveis.

- [ ] **Step 8: Parar o servidor**

Encerrar o processo do `http.server`.

---

## Task 7: Verificação dos destinos

**Files:** nenhum — verificação apenas

Esta é a tarefa que valida o propósito do painel. Um card que abre o destino errado é pior que um card ausente.

- [ ] **Step 1: Verificar os destinos públicos**

```bash
cd "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ" && python -c "
import io, re
h = io.open('index.html', encoding='utf-8').read()
for u in re.findall(r\"url:'(https://pmo-creator[^']*)'\", h): print(u)
" | while read u; do printf "%s -> " "$u"; curl -s -o /dev/null -w "%{http_code}\n" -L "$u"; done
```

Saída esperada: três linhas terminando em `200`.

- [ ] **Step 2: Verificar manualmente os 8 destinos do Google**

Exigem login `@idg.org.br` e não podem ser verificados por script. Abrir o painel e clicar em cada card, conferindo o comportamento:

| Card | Esperado ao clicar |
|---|---|
| POP — EGP | Documento abre **em modo leitura**, sem barra de edição |
| Manual POP — EGP | Apresentação abre em modo leitura |
| Avaliação de Novos Projetos | Formulário abre **para responder** — nunca a tela de edição do formulário |
| Matriz de Priorização | Caixa **"Fazer uma cópia"** — não o arquivo original |
| Descrição de Cargos | Caixa **"Fazer uma cópia"** |
| Cronograma | Planilha do MAZ abre normalmente, na aba correta |
| Termo de Encerramento | Formulário abre para responder |
| Rodapé "Ver todos no Drive" | Pasta do EGP no Drive |

- [ ] **Step 3: Tratar falhas do modo cópia**

Se a Matriz ou a Descrição de Cargos der erro de permissão em vez da caixa de cópia, a causa é a opção avançada "impedir download, impressão e cópia" no compartilhamento do arquivo. Registrar o resultado e reportar ao usuário — a correção é no Drive, não no código. Enquanto não for corrigida, trocar o `/copy` por `/edit` na URL do card afetado.

- [ ] **Step 4: Tratar falhas do modo leitura**

Se o POP ou o Manual POP não abrir com `/preview` (documentos importados do Office às vezes não suportam), trocar o sufixo por `/edit` no bloco `DOCS` e registrar o desvio.

---

## Task 8: README do repositório

**Files:**
- Create: `README.md`

- [ ] **Step 1: Criar o README**

Criar `README.md`:

```markdown
# Painel EGP · IDG

Índice dos documentos do Escritório de Gestão de Projetos do Instituto de
Desenvolvimento e Gestão.

**Painel publicado:** https://pmo-creator.github.io/egp-idg/

## O que é

Uma página que reúne, num lugar só, os documentos usados nas duas etapas do
processo do EGP — Priorização de Iniciativas e Execução. Cada card abre o
documento correspondente em nova aba.

O painel não hospeda documentos. Ele aponta para onde cada um já vive: Google
Drive, ou as páginas publicadas no repositório `maz-dashboard`.

## Acesso

Os documentos do Drive estão compartilhados com o grupo `@idg.org.br`. Quem não
tiver conta do instituto verá o painel, mas não conseguirá abrir os documentos.

## Como alterar um link

Abrir `index.html` e localizar o bloco `const DOCS`, no topo do `<script>`. É o
único lugar a editar. Cada documento tem:

| Campo | O que é |
|---|---|
| `nome` | Título exibido no card |
| `desc` | Uma linha explicando para que serve |
| `icone` | Emoji do card |
| `tipo` | `docs`, `slides`, `sheets`, `form`, `github` ou `pendente` — define a cor do selo |
| `modo` | `leitura`, `copia`, `edicao` ou `preencher` — define o texto auxiliar |
| `url` | O endereço de destino |

Para ativar um documento que está como "Em elaboração", preencher a `url` e
trocar o `tipo` de `pendente` para o formato correto.

## Modos de link do Google

O sufixo da URL determina o que acontece ao abrir. Isso é intencional:

| Sufixo | Efeito | Usar quando |
|---|---|---|
| `/preview` | Abre em leitura, sem barra de edição | Documento de consulta |
| `/copy` | Oferece "Fazer uma cópia" | Modelo a ser preenchido |
| `/edit` | Abre o arquivo para edição | Arquivo vivo da equipe |
| `/viewform` | Formulário no modo resposta | Google Forms |

O `/copy` protege o modelo original: sem ele, quem preencher salva por cima do
documento de referência do EGP.

Nunca usar o link `/edit` de um Google Forms — ele dá acesso de edição ao
formulário.

## Antes de colar uma URL nova

Remover os parâmetros que o Google acrescenta ao link de compartilhamento:
`ouid`, `usp`, `rtpof`, `sd` e `pli`. O `ouid` identifica a conta que gerou o
link e este repositório é público. Os links funcionam sem eles.

Exceção: o parâmetro `gid` do Cronograma seleciona a aba da planilha e deve ser
mantido.

## Documentação

O desenho e as decisões estão em
`docs/superpowers/specs/2026-07-28-painel-egp-idg-design.md`.
```

- [ ] **Step 2: Verificar o README**

```bash
cd "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ" && python -c "
import io
r = io.open('README.md', encoding='utf-8').read()
for t in ['const DOCS','/copy','ouid','egp-idg']:
    assert t in r, 'trecho ausente no README: ' + t
print('OK - readme com', len(r), 'bytes')
"
```

Saída esperada: `OK - readme com ~2600 bytes`

---

## Task 9: Publicação — SOMENTE COM AUTORIZAÇÃO EXPLÍCITA

**Files:** operações de git

> **Não executar nenhum passo desta tarefa sem o usuário pedir.** O `CLAUDE.md`
> do projeto proíbe `git add`, `commit` e `push` sem instrução explícita, e esta
> é a regra mais importante do repositório.

- [ ] **Step 1: Confirmar o destino com o usuário**

Perguntar antes de qualquer comando:

1. Criar o repositório `egp-idg` ou publicar em outro lugar?
2. O `logo-idg.png` (diagrama Risco × Problema, alheio ao projeto) pode ser removido?
3. O `logotipo-idg.svg` deve ir para o repositório? Ele não é usado em runtime — o SVG está embutido no HTML — mas serve como fonte para futuras alterações.

- [ ] **Step 2: Criar o repositório**

Só após o "sim" do passo anterior:

```bash
cd "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ" && gh repo create egp-idg --public --description "Painel de documentos do Escritório de Gestão de Projetos do IDG"
```

- [ ] **Step 3: Publicar o conteúdo**

```bash
cd "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ" && git init && git add index.html README.md logotipo-idg.svg docs/ && git commit -m "Painel de documentos do EGP - IDG

Índice em página única dos 11 documentos do Escritório de Gestão de
Projetos, divididos em Priorização de Iniciativas e Execução.

Cada card abre seu documento no modo adequado: leitura para documentos de
consulta, cópia para modelos preenchíveis, edição para arquivo vivo.

Co-Authored-By: Claude Opus 5 <noreply@anthropic.com>" && git branch -M main && git remote add origin https://github.com/PMO-creator/egp-idg.git && git push -u origin main
```

- [ ] **Step 4: Ativar o GitHub Pages**

```bash
gh api -X POST repos/PMO-creator/egp-idg/pages -f "source[branch]=main" -f "source[path]=/"
```

- [ ] **Step 5: Confirmar a publicação**

O Pages leva 1–2 minutos para propagar.

```bash
curl -s -o /dev/null -w "%{http_code}\n" -L https://pmo-creator.github.io/egp-idg/
```

Saída esperada: `200`

---

## Critérios de aceite

Conferir contra a seção 13 do spec antes de dar o trabalho por concluído:

- [ ] Os 11 cards renderizam nas seções e colunas corretas — Task 6, passos 2–4
- [ ] Cada card abre o destino correto em nova aba — Task 7, passos 1–2
- [ ] Cards de modo cópia abrem "Fazer uma cópia", não o arquivo original — Task 7, passo 2
- [ ] Cards de modo leitura abrem sem barra de edição — Task 7, passo 2
- [ ] Card Proposta Técnica exibe toast e não navega — Task 6, passo 5
- [ ] Nenhuma URL contém `ouid`, `usp`, `rtpof`, `sd` ou `pli` — Task 3, passo 3
- [ ] Logo legível sobre o header escuro — Task 2, passo 3
- [ ] Layout legível em 1920px, 1366px e 390px — Task 6, passo 6
- [ ] Arquivo abre por duplo-clique, sem servidor, sem erro no console — Task 2, passo 3 e Task 6, passo 7
- [ ] Bloco `DOCS` é o único lugar a editar para trocar qualquer link — por construção, documentado no README
- [ ] Nenhuma dependência externa — verificar:

```bash
cd "C:/Users/gagui/Github/maz-dashboard/Painel de PMO MAZ" && python -c "
import io, re
h = io.open('index.html', encoding='utf-8').read()
externos = re.findall(r'<(?:script|link)[^>]*(?:src|href)=[\"\x27](https?:)?//[^\"\x27]*', h)
assert not externos, 'dependencia externa: %s' % externos
print('OK - painel autocontido')
"
```

Saída esperada: `OK - painel autocontido`

---

## Desvios a reportar

Registrar e comunicar ao usuário, sem corrigir por conta própria:

| Situação | O que reportar |
|---|---|
| `/preview` não funciona no POP ou Manual POP | Qual card, que erro apareceu, e que o `modo` voltou para `edicao` |
| `/copy` bloqueado por permissão | Qual arquivo — a correção é no compartilhamento do Drive, não no código |
| Logo não recolore para branco | Que a regra `!important` foi necessária |
| Qualquer link do Google retorna erro | Qual card e qual mensagem — pode ser arquivo movido ou permissão alterada |
