# Novidades do EMClytics

Cada versão lista o que é novo, o que mudou e o que ainda não existe.
Números de versão: MAJOR.MINOR.PATCH. A série 0.x é de prévia.

## [0.3.3] — 2026-11-10

### Novo
- **Linha de comando no executável:** o `EMClytics-cli.exe`, ao lado do `EMClytics.exe`, gera o relatório HTML sem abrir a janela (por exemplo, num script): `EMClytics-cli --entrada <ODB++> --intencao project_emc.yaml --saida relatorio.html`. O idioma é o da janela (ou `--idioma`), e o código de saída diz se deu certo.
- **Andamento e Cancelar:** a barra embaixo mostra a camada que está sendo lida e a regra que está sendo avaliada; Cancelar (ou Esc) interrompe a análise, e a placa que estava na tela continua.
- **Verificar atualizações** (menu Ajuda): abre no navegador a página das versões. O programa continua sem usar a internet.
- **Prefixos de refdes na intenção:** em `componentes_especiais.prefixos_refdes`, diga o tipo dos componentes de um prefixo que o programa não reconhece (ex.: `LED: diodo`). Vale para os componentes sem o tipo vindo do CAD.
- **Zonas eletromagnéticas (aba Zonas):** o programa reconhece a zona de cada CI pelo que está ligado a ele (conector, cristal, antena, indutor de fonte chaveada, nets de alta velocidade, nome das nets) e diz se a disposição atual mistura as zonas. Também desenha uma proposta sobre o contorno da placa: a interface com todos os conectores numa borda, as zonas em faixas, os CIs dentro delas e onde vai blindagem (BLS), com as medidas pelo fMAX. "Salvar desenho…" grava o desenho em SVG. Em `zonas[].funcao` do `project_emc.yaml`, você define a zona dos componentes que o programa classificou errado.
- **Relatório com as zonas:** o relatório HTML ganhou a seção "Zonas eletromagnéticas", com os desenhos de hoje e da proposta, os conflitos, as zonas que pedem blindagem e por que cada CI está na zona. Os desenhos seguem o tema claro ou escuro de quem abre o relatório.
- **Zonas com os conectores no lugar:** a aba Zonas ganhou uma terceira vista, **Conectores fixos**, para quando os conectores não podem mudar de lugar (gabinete, painel). Os conectores e as antenas ficam onde estão e as outras zonas se arrumam em volta deles, com a parte rápida o mais longe possível dos conectores e a blindagem indicada onde a distância não basta. Conector no meio da placa vira uma ilha que os componentes contornam. O relatório traz esse terceiro desenho.
- **Terras (0V):** a aba Zonas ganhou a vista **Terras (0V)**, também no relatório. Ela mostra cada rede de 0V com o papel dela (0V principal; separado por função, como AGND/DGND; terra de chassi; 0V de entrada antes do filtro; isolado), onde está o cobre de cada uma, os componentes que ligam uma à outra, as divisas com o 0V principal e os CIs com 0V em redes diferentes. Em seguida diz o que fazer, na ordem das regras: um 0V só com separação pelo zoneamento; se a divisão ficar, costura ao longo de toda a divisa; isolação com capacitores; terra de chassi com bonds de RF junto dos conectores.
- **RP-008 e RP-023 verificadas:** conectores off-board numa borda só, sem CI nem capacitor de desacoplamento entre eles e com o mesmo 0V; zona declarada maior que λ/10 no fMAX pede provisão de BLS.
- **RP-043, RP-044 e RP-045 verificadas:** conectores em partes diferentes do plano de referência; costura da divisão mantida (passo abaixo de λ/10 em fMAX e todas as posições populadas do mesmo jeito); costura da isolação galvânica com capacitores.

### Mudou
- **Importação mais rápida:** numa placa real de referência, a importação caiu de 9 s para 6 s, com os mesmos achados.
- **RP-036:** quando a trilha cruza o vão entre o plano e o cobre de uma net de sinal roteada na camada do plano, a mensagem fala em "gap no plano" (antes, chamava a net de sinal de plano). Esse caso não é atenuado por capacitores, que a regra prevê só entre dois planos.
- **RP-042:** a terra de chassi e o 0V de entrada antes do filtro não contam mais como divisão do 0V do circuito: viram um aviso por net, que explica o papel (antes, uma violação por camada).
- **Aba Interfaces e filtragem, mais clara:** cada pino aparece como um caminho do conector até o CI, mesmo quando o CI está depois de um resistor ou de uma ferrite em série. Cada componente fica onde está na placa, mais perto do conector ou do CI (pela distância em linha reta), com o símbolo dele (resistor, ferrite, capacitor, TVS), e um selo diz a situação do pino. Clique num componente para vê-lo na placa. O texto ficou mais curto; como a ferramenta lê o caminho fica a um clique.
- **Corte do stack-up:** o cobre aparece como num corte de placa (inteiro nos planos, em trilhas nas camadas de sinal), com a trama do dielétrico, L1, L2…, a espessura total e uma seta de cada camada de sinal até o plano de referência dela.
- **Menos alarmes sem sentido**, conferido em 12 placas reais de exemplo (os achados dessas regras caíram de 514 para 300):
  - **RP-183:** trilha que não sai do pad do componente (é o próprio pad) não é mais achado; o comprimento medido é só o que sai do pad.
  - **RP-012 e aba Interfaces:** componente de um pad só (ponto de teste, contato, pad de fio) não é tratado como conector nem como CI.
  - **RP-029:** cobre sem net ou de 0V (a referência, o terra de chassi) não precisa de plano por baixo: furo de fixação, concha de conector e capacitor de ligação do terra de chassi saíram.
  - **RP-074:** a busca da via de cada decap não atravessa mais o pad de outro componente (antes, uma fileira de decaps, cada um com a sua via, virava um achado por via), e as vias divididas pelos mesmos decaps saem num achado só. Decap sem via própria, que usa a via de um vizinho, continua acusado.
  - **RP-169:** pedaço de plano menor que 1 mm² não é avaliado; com fMAX informado, nem o plano cuja 1ª ressonância fica acima de fMAX.
  - **RP-190:** net de alimentação ou de controle com "CLK" no nome (VCC3P3_CLK, CLK_SEL) não é clock; não contam a saída dos pinos entre os pads do CI nem a outra linha do mesmo cristal.
  - **RP-109 a RP-112:** via no pad do pino e saída do pino sob o próprio componente (fanout de BGA) são a ligação direta ao pino, que a regra permite.

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
