# Changelog

Registro de progresso do projeto ATENA, em ordem cronológica (mais recente primeiro).
Não é um changelog técnico linha a linha do código — é um resumo do que mudou, pra
quem quiser acompanhar de fora.

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
