# Guia rápido do EMClytics

## 1. Exportar a placa em ODB++

Na sua ferramenta de CAD, exporte o PCB em ODB++. No Altium Designer, com o PCB aberto: **File › Fabrication Outputs › ODB++ Files**. O programa foi conferido com exportações do Altium Designer e do Siemens Xpedition; outras ferramentas que exportam ODB++ 8.1 devem abrir.

Para a análise completa, a exportação precisa levar:

- as camadas de cobre, a furação e o contorno da placa;
- a lista de nets e os componentes (o EMClytics avisa quando faltam);
- se possível, as camadas dielétricas, com espessura e constante dielétrica (stack-up).

O resultado é uma pasta `odb` (ou um `.tgz`/`.zip`), em geral dentro de `Project Outputs`.

## 2. Abrir no EMClytics

Na tela inicial, **Abrir pasta ODB++** e escolha a pasta `odb`, ou **Abrir arquivo ODB++** para o arquivo compactado. Também dá para arrastar a pasta ou o zip para a janela (com o `project_emc.yaml` junto, se houver). As placas abertas por último ficam em **Recentes**. Para conhecer o programa sem exportar nada, use **Abrir a placa de exemplo** (a LimeSDR Mini 2.5, com uma intenção ilustrativa). A análise leva alguns segundos (placas grandes, mais), com o andamento na barra embaixo; **Cancelar** (ou Esc) interrompe, e a placa que estava na tela continua. Depois, a placa aparece com as camadas. Se a exportação tiver registros ilegíveis (arquivo cortado ou corrompido), a placa abre com uma faixa no alto que diz em quais arquivos, e os dados afetados aparecem como parciais nas regras.

- **Camadas** (à esquerda): ligue e desligue camadas, vias, refdes e marcadores. Com **Só as camadas do achado**, a placa mostra só as camadas do achado escolhido; **Restaurar camadas** volta ao que estava.
- **Achados** (à direita):
  - no alto, o resumo: violações, avisos e aceitos, e uma linha por regra com a contagem e o Total, como no Summary de um DRC. O clique numa linha leva às ocorrências da regra;
  - embaixo, os achados agrupados por regra, com filtro (em aberto, todos, só violações, só avisos, aceitos);
  - no fim da lista ficam as regras sem achado, os checklists e as não verificadas.
- **Detalhe do achado** (embaixo): o que foi encontrado, a medida e o limite, a confiança e como corrigir. Nas regras com conta, a conta feita com os valores da placa. O porquê fica recolhido, a um clique. À direita, uma ilustração animada mostra o problema e a correção; puxe a borda do painel para aumentá-la. **Ir ao ponto** leva a placa ao local.

As outras abas:

- **Stack-up e recomendações:** o corte da placa, com o papel de cada camada (sinal, plano 0V, plano PWR) e o plano de referência de cada camada de sinal, e as recomendações de roteamento por tipo de sinal.
- **Interfaces e filtragem:** cada conector externo, com o caminho de cada pino até o CI e as dicas de filtragem.
- **Zonas:** a zona eletromagnética de cada CI (interface, rádio, analógico, digital, digital rápido, fonte chaveada), se a disposição atual mistura as zonas e uma proposta sobre o contorno da placa: as zonas separadas, os CIs e os conectores dentro delas e onde vai blindagem. **Salvar desenho…** grava o desenho em SVG para mandar a quem faz o layout. Se o programa errar a zona de um componente, corrija em `zonas[].funcao` no `project_emc.yaml`.
  - **Conectores fixos:** quando os conectores não podem mudar de lugar (gabinete, painel), esta vista mantém os conectores e as antenas onde estão e arruma as outras zonas em volta deles.
  - **Terras (0V):** as redes de 0V da placa (principal, AGND/DGND, chassi, entrada, isolada), onde fica o cobre de cada uma, o que liga uma à outra e o que fazer com elas.

O tema fica em **Exibir › Tema** (Seguir o Windows, Claro ou Escuro). O idioma (português ou inglês) fica no menu **Idioma / Language**; a troca vale quando o programa reabre. Na primeira vez, o programa abre no idioma do Windows.

## 3. Informar a intenção de projeto

A exportação não diz qual é a frequência máxima de interesse, quais nets são críticas nem quais conectores saem da placa. Essas informações ficam no `project_emc.yaml`, ao lado da exportação. Enquanto ela não for preenchida, um aviso no alto da placa mostra quantas regras esperam esses dados.

- Na aba **Intenção**, crie o arquivo e preencha pelo formulário fMAX, nets críticas, conectores externos e as terras (0V) separadas de propósito, com o motivo.
- Os demais campos (cristais, zonas, stack-up manual, parâmetros de regras, prefixos de refdes) ficam no próprio arquivo; o modelo comentado é o [project_emc.exemplo.yaml](project_emc.exemplo.yaml).
- O arquivo criado começa como **rascunho**: os achados que dependem dele saem com confiança menor até você revisá-lo e trocar `rascunho: true` por `false`.

## 4. Ler os resultados

| Situação | O que quer dizer |
|---|---|
| Violação / Aviso | A regra achou a condição na placa |
| Sem achado | A regra rodou e não achou a condição (não quer dizer que a placa passa nos ensaios) |
| Não verificada | Falta um dado (da exportação ou da intenção); o motivo aparece |
| Checklist | Regra que depende de julgamento, mecânica ou ensaio: conferir à mão |

- **Severidade** (alta, média, baixa): o peso do risco de EMC.
- **Confiança** (alta, média, baixa): o quanto o achado depende de heurística, por exemplo, uma net reconhecida só pelo nome.

## 5. Aceitar uma exceção

Se um achado é aceitável no seu projeto, selecione-o e use **Aceitar como exceção…**, com justificativa. A exceção fica no `project_emc.yaml`, com autor e data, e o achado passa a aparecer como aceito.

## 6. Relatório

**Exportar relatório HTML** gera um arquivo com o mesmo resumo da tela, os achados de cada regra, as regras não verificadas, o checklist manual, as regras sem achado e o que o importador conseguiu ler da exportação. O relatório traz a versão do programa, o programa que gerou o ODB++ e um identificador da base de regras usada, e segue o tema claro ou escuro do sistema de quem abre.

## 7. Linha de comando

Para gerar o relatório sem abrir a janela (por exemplo, num script), use o `EMClytics-cli.exe`, que fica na pasta do programa:

```
EMClytics-cli --entrada <pasta ou .tgz do ODB++> --intencao project_emc.yaml --saida relatorio.html
```

`EMClytics-cli --help` mostra as opções. O idioma é o mesmo da janela; para trocar só numa execução, use `--idioma en`. O código de saída é 0 quando o relatório foi gravado, 2 quando há erro no `project_emc.yaml` e 3 quando a placa não abriu.

Para saber se há versão nova, use **Ajuda › Verificar atualizações**: a página das versões abre no navegador. O programa em si não usa a internet.
