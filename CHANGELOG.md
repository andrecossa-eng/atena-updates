# Changelog

Registro de progresso do projeto ATENA, em ordem cronológica (mais recente primeiro).
Não é um changelog técnico linha a linha do código — é um resumo do que mudou, pra
quem quiser acompanhar de fora.

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
