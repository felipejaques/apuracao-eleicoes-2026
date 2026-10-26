# Apuração · Eleições 2026

Painel com a apuração da eleição para Presidente de 2026, alimentado em tempo real pelos dados oficiais do TSE.

🔗 **Acesse:** https://felipejaques.github.io/apuracao-eleicoes-2026/

A página consulta a API pública de resultados do TSE direto do navegador, a cada 60 segundos. Não tem backend nem etapa de build: é um único `index.html` publicado no GitHub Pages.

## O que o painel mostra

- **Placar nacional:** % de votos válidos e votos de cada candidato, % de seções totalizadas, brancos, nulos e abstenção. O candidato mais votado ganha uma estrela e um selo, que vira "Eleito" quando o TSE marca o resultado. Também aparece a vantagem em votos e em pontos percentuais.
- **Evolução:** gráfico de linha com o % de cada candidato ao longo da apuração. O eixo horizontal pode ser o % apurado ou o horário.
- **1º turno × 2º turno:** % e votos de cada finalista nos dois turnos, mudança de abstenção, brancos e nulos, e um gráfico com a vantagem em cada estado nos dois turnos.
- **De onde vieram os votos:** divide o crescimento dos finalistas em três fontes: votos dos candidatos eliminados, aumento do comparecimento e queda de brancos e nulos. Inclui uma tabela por estado.
- **Mapa da apuração:** mapa do Brasil com o % de seções totalizadas em cada estado.
- **Por região:** resultado somado de Norte, Nordeste, Centro-Oeste, Sudeste, Sul e Exterior. Clicar numa região filtra a tabela.
- **Por estado:** uma barra por UF, que pode ser ordenada por região, % apurado, vantagem ou eleitorado.

## Fonte dos dados

Os dados vêm de `https://resultados.tse.jus.br`. A API é pública, mas o TSE não publica documentação dela. Os endereços abaixo foram encontrados a partir do arquivo de configuração das eleições:

| Arquivo | Endereço |
|---|---|
| Lista de eleições e códigos | `/oficial/comum/config/ele-c.json` |
| 2º turno, Presidente (eleição 6258) | `/oficial/ele2026/6258/dados/<uf>/<uf>-c0001-e006258-u.json` |
| 1º turno, Presidente (eleição 6257) | `/oficial/ele2026/6257/dados/<uf>/<uf>-c0001-e006257-u.json` |

- `<uf>` é a sigla do estado em minúsculas, `br` para o total nacional ou `zz` para o exterior.
- `c0001` é o código do cargo de Presidente.
- Em 2026 os endereços mudaram em relação a 2022: o caminho `dados-simplificados/...-r.json` deixou de existir.

Campos usados de cada arquivo:

| Campo | Significado |
|---|---|
| `carg[0].agr[].par[].cand[]` | Candidatos: `n` (número), `nmu` (nome na urna), `vap` (votos), `pvapn` (% dos válidos), `e` (eleito) |
| `s.st`, `s.ts`, `s.pstn` | Seções totalizadas, total de seções e % totalizado |
| `e.te`, `e.c`, `e.pan` | Eleitorado, comparecimento e % de abstenção |
| `v.vb`, `v.tvn` | Votos brancos e nulos |
| `dg`, `hg` | Data e hora da divulgação |

O TSE permite chamadas de qualquer origem (CORS) e guarda as respostas em cache por cerca de 60 segundos. Por isso o painel atualiza uma vez por minuto.

## Como rodar localmente

A página precisa ser aberta por um servidor HTTP, e não direto como arquivo, para que as chamadas à API funcionem. Qualquer servidor estático serve. Com Python:

```bash
python -m http.server 8765
```

Depois abra http://localhost:8765 no navegador.

## Tecnologias

- HTML, CSS e JavaScript puro, num único arquivo
- [D3.js](https://d3js.org/) 7.9.0 para o mapa e os gráficos, carregado do cdnjs
- Contorno dos estados vindo do GeoJSON do projeto [click_that_hood](https://github.com/codeforamerica/click_that_hood), da Code for America
- Fontes Archivo e IBM Plex Mono, do Google Fonts
- [GoatCounter](https://www.goatcounter.com/) para contar visitas, sem cookies
- Hospedagem no GitHub Pages

## Limitações

- **Histórico da evolução:** a API do TSE só entrega o resultado do momento. Por isso a página guarda uma leitura a cada atualização no `localStorage` do navegador. A linha começa quando a página é aberta pela primeira vez naquele navegador e não aparece em outros navegadores.
- **Origem dos votos:** os números mostram saldos. Como o voto é secreto, não há como saber quem trocou de candidato. Uma pessoa que mudou de Flávio para Lula e outra que fez o contrário se anulam na conta.
- **API sem documentação:** o TSE pode mudar os endereços ou o formato dos arquivos sem aviso.
- **Contagem de visitas:** o GoatCounter não conta acessos feitos pelo `localhost`.
