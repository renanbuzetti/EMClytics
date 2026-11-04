# Novidades do EMClytics

Cada versão lista o que é novo, o que mudou e o que ainda não existe.
Números de versão: MAJOR.MINOR.PATCH. A série 0.x é de prévia.

## [0.3.2] — 2026-11-04

### Mudou
- **Exportação com defeito:** um registro ilegível (arquivo cortado ou corrompido) não derruba mais a análise. Ele fica de fora, uma faixa no alto da placa diz em quais arquivos isso aconteceu, e as regras que usam o dado afetado mostram que ele está parcial.
- **Mais proteção ao abrir:** arquivo `.Z` corrompido, ou que passaria do limite de segurança na memória (1 GB por arquivo), é recusado com uma mensagem clara e o caminho do arquivo dentro da exportação.
- **Mensagens sem a sua pasta:** os erros ao abrir mostram só o nome do arquivo ou da pasta escolhida, não o caminho com o seu nome de usuário. O registro de erro inesperado e o "Copiar detalhes" tiram a sua pasta pessoal em qualquer forma em que ela apareça (antes, ela escapava quando vinha dentro da mensagem do erro).

## [0.3.1] — 2026-11-04

### Novo
- **Tela inicial:** antes de abrir uma placa, o programa mostra "Abrir pasta ODB++", "Abrir arquivo ODB++" e **"Abrir a placa de exemplo"**, mais as placas abertas por último.
- **Placa de exemplo:** a LimeSDR Mini 2.5, da Lime Microsystems (Solderpad Hardware License v2.1, que vai junto), com uma intenção de projeto ilustrativa. Dá para ver achados, ilustrações e relatório sem exportar nada. Também em Arquivo › Abrir a placa de exemplo.
- **Recentes:** na tela inicial e em Arquivo › Recentes.
- **Arrastar e soltar:** solte a pasta ou o zip do ODB++ na janela para abrir. Solte junto o `project_emc.yaml` para usar a intenção.
- **Erro inesperado:** em vez de o programa fechar sem aviso, uma janela explica o que houve e oferece **Copiar detalhes** para abrir uma issue. Um registro local (`%LOCALAPPDATA%\EMClytics\registro`) guarda o erro, sem dados da placa e sem o seu nome de usuário.

### Mudou
- **Falha ao abrir:** a mensagem diz o que fazer, por exemplo escolher a pasta da exportação ou exportar de novo com a netlist e os componentes.
- **Segurança dos dados:**
  - a cópia temporária da placa (ao abrir um .tgz ou .zip) é apagada mesmo quando a abertura falha;
  - sobras de uma importação interrompida são limpas quando o programa abre;
  - antes de descompactar, o programa confere o espaço livre e um limite de segurança (4 GB e 200 mil itens), contra arquivo malicioso que encheria o disco;
  - arquivo corrompido vira uma mensagem clara.

## [0.3.0] — 2026-11-04 — primeira prévia pública (gratuita)

### Novo
- **Importação ODB++** (pasta, .tgz ou .zip): camadas, stack-up, trilhas, planos (inclusive negativos), pads, vias, componentes e propriedades de BOM.
  - Conferida com exportações do Altium Designer e do Siemens Xpedition. O programa segue a especificação ODB++ 8.1: outras ferramentas que exportam ODB++ (Cadence, KiCad, Zuken e outras) devem abrir, mas ainda não foram testadas.
  - Arquivos em polegada ou em milímetro.
  - Rígido-flex: as zonas rígida e flexível são lidas, e o plano que só existe nas partes rígidas não vira "fenda" na região flexível. O coverlay entra no stack-up.
  - HDI: vias cegas e enterradas; vias empilhadas ou escalonadas contam como uma ligação só.
  - Polígono hachurado, que o ODB++ exporta como centenas de linhas, é detectado e avisado. Ele não vira plano, e as aberturas da malha não contam como fenda.
- **Base de regras:** 288 regras de boas práticas de layout para EMC: zoneamento, planos e caminho de retorno, desacoplamento, interfaces e filtragem, linhas de transmissão, stack-up, cristais, ESD de sistema e outras. Cada regra traz o mecanismo físico e a correção sugerida.
- **25 checks automáticos:** RP-007, 012, 029, 030, 031, 036, 042, 047, 072, 073, 074, 109, 110, 111, 112, 116, 117, 120, 122, 158, 167, 169, 183, 190 e 192.
  - As regras manuais aparecem como checklist.
  - As que dependem de um dado ausente aparecem como "não verificadas", com o motivo. Nunca como aprovadas.
  - A regra que rodou sem encontrar nada aparece como "Sem achado" (não quer dizer que a placa passa nos ensaios).
  - Cada achado tem confiança (alta, média ou baixa), separada da severidade, com o motivo.
- **Achados:**
  - resumo no topo, como o Summary de um DRC: violações e avisos, uma linha por regra com a contagem e o Total; o clique leva às ocorrências;
  - detalhe curto: o problema, como corrigir e, nas regras com conta, a conta feita com os valores da placa. O resto fica recolhido;
  - ao clicar num achado, a placa mostra o ponto e, se você quiser, só as camadas dele;
  - ilustração animada do problema e da correção, lado a lado, com a corrente andando.
- **Intenção de projeto** (`project_emc.yaml`): fMAX, nets críticas, conectores externos, terras separadas de propósito, exceções aceitas e outros dados que a exportação não traz.
  - Edição por formulário, sem perder os comentários do arquivo.
  - Aviso na aba da placa quando a intenção ainda não foi preenchida.
- **Interface visual:**
  - placa com camadas, zoom, realce de net e busca;
  - stack-up em corte, com o papel de cada camada e o plano de referência de cada camada de sinal;
  - interfaces e filtragem, por conector, com o caminho de cada pino até o CI;
  - "Aceitar como exceção", com justificativa, autor e data;
  - tema claro e escuro (Exibir › Tema), que pode seguir o Windows.
- **Idiomas:** português e inglês, no menu Idioma / Language (a troca vale quando o programa reabre; na primeira vez, o programa abre no idioma do Windows). Interface, achados, regras e relatório nos dois idiomas.
- **Relatório HTML** com o mesmo resumo da tela e a mesma identidade visual. Ele segue o tema claro ou escuro do sistema de quem abre.
- **Executável para Windows:** pasta com `EMClytics.exe`, sem instalar Python.
- **Privacidade:** tudo roda no seu computador; o programa não usa a internet.

### Ainda não
- Outros formatos de entrada (IPC-2581 e os demais).
- Exportações de Cadence, KiCad e Zuken conferidas com placas reais.
- Stack-up próprio para a zona flexível do rígido-flex (hoje, a análise usa o da zona rígida).
- Integração com o Altium (script que exporta a placa e abre o programa).
- Aviso de atualização dentro do programa.
- Versão Pro: relatório em PDF, comparação entre revisões, histórico de projetos e mais checks.
