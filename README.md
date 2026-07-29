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
