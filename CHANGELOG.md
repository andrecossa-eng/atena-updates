# Changelog

Registro de progresso do projeto ATENA, em ordem cronológica (mais recente primeiro).
Não é um changelog técnico linha a linha do código — é um resumo do que mudou, pra
quem quiser acompanhar de fora.

## 2026-10-07

- **Validação com CLP real.** A ATENA foi testada de ponta a ponta com um CLP Altus
  Nexto Xpress XP340, fazendo MQTT direto pela biblioteca do próprio fabricante: primeiro
  com um broker local sem criptografia, depois com um broker na nuvem usando TLS 1.2.
  Sensor, cilindro, comando e status do CLP funcionaram nos dois cenários, e o comando
  pela ATENA acionou o cilindro real.
- Autoridade certificadora própria (RSA 2048, TLS 1.2) no lugar de certificado público,
  porque o firmware do CLP não lida bem com as cadeias de certificado mais novas — com
  script de geração e de verificação da cadeia.
- O principal obstáculo do teste não era o programa: o firewall de um antivírus no PC
  descartava a tentativa de conexão do CLP antes de ela chegar ao broker. Foi descoberto
  capturando os pacotes da rede e ficou documentado no roteiro de teste, junto com os
  outros pontos que costumam travar a primeira conexão.
- Bancada atualizada: cilindro Festo ESNU-20-50-P-A (simples ação, curso de 50 mm) e
  válvula Festo MFH-3-1/8, com o novo modelo 3D e o memorial de cálculo refeito.

## 2026-10-05

- Novo template de programa para o CLP (conexão MQTT, tópicos de sensores, comando do
  cilindro e status online/offline), com um roteiro de teste em etapas que separa
  "problema de rede/certificado" de "problema no programa".
- Diagramas do TCC redesenhados e corrigidos para bater com o código atual:
  arquitetura, fluxo por voz, latência, funcionamento completo e mapeamento pelo
  modelo de referência RAMI 4.0.

## 2026-09-28

- **Gêmeo físico.** A animação do cilindro 3D deixou de usar uma "velocidade" inventada
  e passou a seguir uma equação de movimento com o tempo de curso real do atuador. A
  ATENA compara o tempo que o movimento *deveria* levar com o tempo medido entre as
  leituras reais e avisa — por chat, voz e telemetria, com proposta de chamado — quando
  o atuador foge do previsto, o que pode indicar desgaste, vazamento ou atrito. A IA
  tem uma instrução própria para esse tipo de aviso, para não confundi-lo com um padrão
  estatístico do sinal.
- Peças 3D agora mantêm as cores reais do desenho original por face (corpo, anel,
  faixa...), em vez de uma cor única por peça. O alerta vermelho/verde continua
  funcionando por cima.
- Janela de Conexão: fechar pelo X agora pergunta se deve salvar, o painel de
  mapeamento abre já numa peça de verdade, e renomear uma peça passou a persistir ao
  salvar e reabrir o projeto.
- Corrigido um erro perigoso no chamado de manutenção: se dois avisos se sobrepusessem,
  a pergunta "Deseja que eu abra um chamado?" repetia para o evento novo mas se referia
  ao chamado antigo — um "sim" do operador confirmaria o chamado errado.

## 2026-08-31

- **Provas práticas de qualificação**, com os dados guardados no projeto:
  - *Loop fechado:* a ATENA lê o sensor, publica o comando e confirma que o atuador
    chegou ao valor esperado, repetido várias vezes nos dois sentidos.
  - *Injeção de falha:* mede em quanto tempo a ATENA percebe uma falha provocada,
    comparada ao tempo de um operador humano observando.
  - *Latência:* medição do tempo entre a chegada de uma leitura e a avaliação do alerta
    (mediana de cerca de 17 ms em 33 medições).
  - *Base simbólica e prognóstico:* padrão de sensor associado a causas prováveis, e
    estimativa de quanto tempo falta até um sinal cruzar o limite configurado.
- Cada peça fechada passa a publicar seu estado automaticamente, fechando a malha MQTT
  com o equipamento.
- Suíte de testes automatizados em 271 testes, todos passando, com relatório gerado.
- Pastas reorganizadas: modelos 3D e projetos de exemplo agora têm diretórios próprios.

## 2026-08-27

- A palavra de ativação ("ATENA") e os comandos já cadastrados no projeto agora são
  reconhecidos **localmente, offline** — exatamente o que tinha ficado nos planos da
  atualização anterior. Um modelo de reconhecimento de voz pequeno roda na própria
  máquina, restrito a um vocabulário fechado (a wake word, ou os nomes dos comandos do
  projeto atual): quando a fala bate com algo conhecido, nem o reconhecimento de voz
  na nuvem nem a IA chegam a ser chamados — vai direto pro mesmo fluxo de confirmação
  sim/não de sempre, antes de acionar qualquer coisa no equipamento real. Só uma
  pergunta livre de verdade ainda passa pelo caminho de nuvem (voz + IA).
- Corrigido um atraso real no aviso sonoro de alarme: o bipe só tocava depois que a IA
  terminasse de gerar a primeira frase da análise falada — alguns segundos de "alarme
  mudo" antes do próprio bipe, o que ia contra o propósito de um aviso instantâneo.
  Agora ele toca na hora em que o alerta é percebido, em paralelo com a análise sendo
  gerada. A fala em si não ficou mais rápida — só o bipe parou de esperar por ela.
- Novo fluxo de **chamado de manutenção**: a IA (ou um alerta) pode propor abrir um
  chamado, e ele só é aberto depois da mesma confirmação sim/não usada nos comandos.
  Também corrigido o filtro de manuais, que puxava o datasheet errado porque palavras
  curtas ("temp") batiam dentro de outras ("temperature"); agora exige palavra inteira.
- Ainda não validado com uso real: só o roteamento da lógica foi testado, não a
  precisão do reconhecimento de voz local em ambiente com ruído de fábrica, sotaque ou
  microfone mais distante — planejado pra essa semana.

**Nos planos, ainda não implementado:** reduzir a demora até a IA de fato começar a
falar (tempo até o modelo gerar a primeira frase + tempo de conexão da síntese de
voz), que segue igual — a correção desta rodada só adiantou o bipe, não a fala.

## 2026-08-21

- Reduziu bastante o tempo de resposta de alarmes e da fala em geral: a IA agora fala
  frase por frase conforme vai gerando a resposta, em vez de esperar a resposta
  inteira terminar pra só então começar a falar — com a próxima frase já sendo
  preparada em paralelo enquanto a atual toca, sem pausa perceptível entre elas.
- Identificado (e cortado) o maior gargalo escondido: o modelo de IA vinha gastando
  tempo "pensando" antes de responder mesmo em avisos curtos — desativar isso sozinho
  já cortou a maior parte do atraso. Só foi seguro fazer isso depois de resolver outro
  problema primeiro: a documentação técnica de *todos* os equipamentos carregados no
  projeto entrava junto em cada pergunta/aviso, mesmo quando não tinha nada a ver —
  o que já tinha causado a IA misturar informação do datasheet errado numa resposta
  sobre outro sensor. Agora só entra o manual relevante pro equipamento/sensor em
  questão.
- Alarme novo (não a recuperação "voltou ao normal") sempre entra com "Atenção
  Operador:" no começo da fala — tanto na resposta da IA quanto no aviso de segurança
  determinístico, caso a IA esteja indisponível — e ganhou um bipe sonoro antes de
  falar, além de prioridade na fila de voz: um alarme corta uma resposta de chat comum
  que estiver tocando, em vez de esperar ela terminar.
- Ainda em validação: os ganhos de tempo foram medidos direto contra a API de
  produção, mas a experiência real de ouvir no dia a dia (ausência de pausa entre
  frases, naturalidade da fala em sequência) ainda precisa de mais uso real —
  planejado pro fim de semana.

**Nos planos, ainda não implementado:** detecção da palavra de ativação ("ATENA")
rodando localmente, sem depender de reconhecimento de voz na nuvem só pra isso, e
reconhecimento direto de comandos já configurados no projeto sem precisar passar pela
IA quando a frase do operador bate com um comando conhecido. Os dois inspirados em
como assistentes tipo Alexa conseguem responder tão rápido — processamento local pro
que é simples/fechado, IA só pro que exige entendimento livre de fato.

## 2026-08-20

- Suporte a **Modbus TCP** além de MQTT — um projeto agora fala com o equipamento por
  qualquer um dos dois protocolos, escolhido na tela de conexão. Cobre praticamente
  todo CLP industrial, inclusive os sem suporte nativo a MQTT, sem precisar de
  gateway/bridge no meio.
- O detector de comportamento anômalo passou a comparar o **tempo real entre
  mudanças** de um sinal, não só se ele mudou — agora também pega corretamente sinais
  digitais/binários (sensores indutivos, fins de curso...) mudando de estado
  erraticamente, caso que antes passava despercebido.
- Projeto `.atp` ficou portátil: os modelos 3D agora vão embutidos dentro do próprio
  arquivo do projeto, então abrir num PC diferente (ou sem os arquivos originais por
  perto) continua carregando tudo normalmente.
- "Isolar" no viewer 3D agora isola um equipamento inteiro de uma vez, além de uma
  peça só.
- Vários ajustes de estabilidade na conexão Modbus e na interface: um registrador com
  endereço/tipo mal configurado não derruba mais a conexão inteira (só avisa e segue
  lendo os outros normalmente), reabrir a janela de conexão repetidamente não quebra
  mais, e a lista de tópicos sugeridos nos formulários atualiza na hora, sem precisar
  salvar primeiro.

## 2026-08-19

- Novidade grande: a ATENA agora percebe padrões de comportamento fora do normal em
  qualquer sensor ou equipamento (motor, sensor de proximidade, esteira...), mesmo sem
  nenhuma faixa de alerta configurada — compara o histórico recente de um sinal com o
  que é normal pra ele mesmo, e já sugere uma causa provável e uma ação, do mesmo jeito
  que já fazia pros alertas de faixa min/max.
- Reconectar ao broker MQTT (ou só reabrir um projeto) agora recupera o último estado
  conhecido da planta, em vez de deixar tudo em branco até a próxima leitura chegar.
- Dois avisos falados em sequência não se atropelam mais — cada um espera o anterior
  terminar antes de começar.
- Renomear/remover peças e equipamentos passou a ser feito direto pelo botão direito na
  árvore de equipamentos; a aba antiga dedicada a isso foi incorporada à aba principal.
- Mapeamento cinemático (relação tópico -> peça/equipamento) virou geral pra planta
  inteira, em vez de precisar reconfigurar equipamento por equipamento — e agora vive
  junto da tabela de vínculos MQTT, num lugar só.
- Comandos e alertas MQTT também passaram a valer pra planta inteira, com o dono de
  cada tópico decidido pelo mapeamento já existente, não por qual equipamento está
  selecionado no momento.
- Botões dedicados de mutar microfone e voz da ATENA, no lugar do texto de status; novo
  botão pra recolher o painel de chat/voz e ganhar mais espaço pro ambiente 3D.
- Ajuste fino na recuperação de estado ao reconectar: antes, se algo mudasse no
  equipamento *enquanto o app estava desconectado* do broker (por exemplo, um alarme
  passar do limite nesse intervalo), isso só aparecia quando chegasse a próxima leitura
  depois de reconectar — podia demorar bastante, ou nunca acontecer. Agora a ATENA
  recupera certinho tudo que aconteceu nesse meio tempo, não só o último estado salvo
  antes de cair a conexão.

## 2026-08-18

- Projetos agora salvam com extensão própria (`.atp`) em vez de aproveitar o `.json`
  genérico — projetos antigos continuam abrindo normalmente.
- Ferramentas novas no viewer 3D: **rotacionar** peça individual ou o equipamento
  inteiro com o mouse, e **isolar** uma peça específica escondendo o resto da montagem
  pra examinar ela sem distração.
- Peças que só reportam estado por sensor (ex.: um sensor de temperatura) não precisam
  mais fingir ter um movimento configurado só pra funcionar — agora dá pra marcar "sem
  movimento" direto.
- Alertas proativos ficaram mais úteis: em vez de citar o nome técnico do sensor/tópico
  cru, a ATENA agora descreve o que está acontecendo em linguagem natural e já sugere
  causas prováveis e um próximo passo, usando a documentação técnica do equipamento
  como referência. Continua avisando normalmente mesmo se a IA estiver indisponível.
- Novo grupo "Manutenção": histórico consolidado de comandos executados e alertas,
  geração de Ordem de Serviço e relatório da planta, tudo a um clique.
- Vários ajustes de interface e correções: ícones mais nítidos e com cor nas ações
  principais, indicadores de status como badges coloridos, árvore de equipamentos não
  fecha mais um item sozinha ao abrir outro, cor de alerta de uma peça não some mais ao
  trocar/adicionar peças, e o aviso de "alterações não salvas" não dispara mais em falso
  logo depois de abrir um projeto que já estava salvo.

## 2026-08-17

- Histórico persistente dos comandos executados no equipamento (antes só aparecia na
  tela, sem ficar registrado).
- Depois que a ATENA pergunta uma confirmação de comando por voz, ela já entra ouvindo
  a resposta — sem precisar chamar "ATENA" de novo só pra dizer sim ou não.
- Conexão MQTT criptografada (TLS/SSL) corrigida e testada de ponta a ponta, incluindo
  suporte a certificado próprio pra brokers com certificado autoassinado.
- Ao importar um arquivo 3D com várias peças de uma montagem, o equipamento já nasce
  com o nome do arquivo em vez do nome genérico.
- Confirmação de alterações não salvas ao fechar o app, criar um projeto novo ou abrir
  outro — evita perder configuração sem querer.
- Janela de mapeamento de movimento (cinemática) não perde mais as configurações ao
  trocar de peça, e agora distingue sinal digital (liga/desliga) de analógico
  automaticamente, sem exigir isso do operador.

## 2026-08-12

- Importação de múltiplos objetos `.obj` de uma vez no viewer 3D.
- Ferramenta de alinhamento de peças reescrita.
- Undo/redo no viewer 3D.
- Gizmo de eixos pra orientação espacial.

## 2026-08-11

- Rework visual completo da interface, no padrão visual de softwares CAD profissionais
  (tipo Autodesk Inventor).
- Suporte a plantas com múltiplos equipamentos ao mesmo tempo, novas ferramentas 3D e
  tema claro.

## 2026-08-10

- Comandos MQTT agora são acionados por *function calling* real do modelo de IA (em
  vez de parsing de texto) — mais confiável, principalmente em respostas por voz.
- Nenhuma ação é executada no equipamento sem confirmação explícita do operador; se a
  confirmação vier ambígua, a ATENA avisa que cancelou.
- Suporte a múltiplos projetos/equipamentos configuráveis.
- Alertas proativos: a ATENA percebe sozinha quando um sensor sai da faixa configurada
  e avisa — sem precisar ser perguntada.
- Gráfico de histórico de telemetria.
- Integração contínua (testes automatizados a cada mudança).
- Correção de bug no reconhecimento de voz após timeout de rede.

## 2026-08-07

- Baseline inicial do projeto.
- Assistente de voz com wake word **"ATENA"** e voz neural, substituindo o chat de voz
  anterior.
- Primeiro redesenho visual: paleta de cores própria, indicador visual de estado
  ("orb") e tela de splash.
- Primeira versão do acionamento de comandos MQTT pela IA.
- Ajustes de robustez e reorganização da interface para demonstrações.

---

*Este changelog é mantido manualmente e resume o histórico de desenvolvimento — não
reflete cada commit individual do repositório privado.*
