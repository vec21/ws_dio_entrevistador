# Workshop OpenClaw — Entrevistador Técnico

Neste workshop você vai montar um assistente que conduz entrevistas técnicas estruturadas. Ele analisa vagas, monta planos de entrevista, faz perguntas uma a uma, avalia respostas e gera um relatório final.

Tudo roda local na sua máquina usando Docker. Sem instalar Node, sem scripts soltos, sem complicação.

---

## Quem preparou este workshop

| | |
|---|---|
| LinkedIn | [linkedin.com/in/diogines-augusto/](https://www.linkedin.com/in/diogines-augusto/) |
| Instagram | [@dioaugusto.dev](https://www.instagram.com/dioaugusto.dev/) |

Me chama se tiver dúvida, sugestão ou quiser trocar ideia sobre o projeto.

---

## Modelos gratuitos — onde conseguir sem pagar nada

Você não precisa gastar dinheiro para fazer este workshop. Existem várias opções gratuitas que funcionam bem.

### Recomendação para o workshop: Google Gemini (gratuito)

A melhor opção gratuita hoje é o Google AI Studio. Você consegue uma chave de API sem cartão de crédito e com limites suficientes para o workshop inteiro.

| O que | Detalhe |
|-------|---------|
| Onde criar a chave | [aistudio.google.com](https://aistudio.google.com) |
| Modelo recomendado | `google/gemini-2.5-flash` |
| Custo | Gratuito |
| Precisa de cartão? | Não |
| Limite gratuito | ~10 requisições por minuto, 250 mil tokens por minuto |

Como configurar no OpenClaw:
```bash
docker exec openclaw-workshop openclaw models set google/gemini-2.5-flash
```

### Outras opções gratuitas

| Provedor | Modelo | Limite gratuito | Cartão? | Onde pegar a chave |
|----------|--------|----------------|---------|-------------------|
| Google AI Studio | Gemini 2.5 Flash | 10 RPM, 250k TPM | Não | [aistudio.google.com](https://aistudio.google.com) |
| Google AI Studio | Gemini 2.5 Flash-Lite | 30 RPM, 1000 RPD | Não | [aistudio.google.com](https://aistudio.google.com) |
| Groq | Llama 3.3 70B | 30 RPM, 14.4k RPD | Não | [console.groq.com](https://console.groq.com) |
| Groq | Llama 4 Scout | 30 RPM, 14.4k RPD | Não | [console.groq.com](https://console.groq.com) |
| OpenRouter | Vários modelos gratuitos | Varia por modelo | Não | [openrouter.ai](https://openrouter.ai) |

**Groq** é uma boa alternativa — respostas muito rápidas e modelos open-source de qualidade. Para usar com OpenClaw, configure a variável `GROQ_API_KEY` no `.env`.

**OpenRouter** funciona como um "agregador" — uma única API que dá acesso a dezenas de modelos, incluindo vários gratuitos. Configure com `OPENROUTER_API_KEY` no `.env`.

### Opções pagas (se quiser mais qualidade)

| Provedor | Modelo | Preço aproximado | Onde pegar |
|----------|--------|-----------------|-----------|
| OpenAI | GPT-4o | ~$2.50/1M tokens entrada | [platform.openai.com](https://platform.openai.com/api-keys) |
| Anthropic | Claude Sonnet 4 | ~$3.00/1M tokens entrada | [console.anthropic.com](https://console.anthropic.com) |
| Google | Gemini 2.5 Pro | Pago (removido do free tier em abril 2026) | [aistudio.google.com](https://aistudio.google.com) |

Para o workshop, o Gemini 2.5 Flash gratuito resolve tranquilamente. Só considere pagar se quiser respostas mais sofisticadas ou usar em produção.

---

## O que é o OpenClaw?

OpenClaw é um assistente pessoal que roda no seu computador. Você conversa com ele por texto (WhatsApp, Telegram, Slack, Discord, terminal, etc.) e ele responde usando modelos de linguagem como GPT-4, Claude ou Gemini.

O diferencial é que ele é extensível por **skills** — arquivos de texto que ensinam o assistente a fazer coisas específicas. Neste workshop, vamos criar uma skill que transforma o OpenClaw em um entrevistador técnico.

---

## O que são skills?

Uma skill é uma pasta com um arquivo `SKILL.md` dentro. Esse arquivo tem duas partes:

1. Um cabeçalho com nome e descrição (em formato YAML)
2. Instruções em texto que dizem ao assistente como se comportar

Quando o OpenClaw inicia, ele lê todas as skills disponíveis e sabe quando usar cada uma. É como dar um manual de procedimentos para alguém — a pessoa lê e segue quando a situação pede.

Exemplo mínimo de uma skill:

```markdown
---
name: minha_skill
description: Faz tal coisa quando pedem.
---

# Instruções

Quando alguém pedir X, faça Y.
```

Simples assim. Sem código, sem compilação, sem dependências.

---

## Pré-requisitos

Você precisa de apenas duas coisas:

- **Docker Desktop** instalado e rodando
- **Uma chave de API** de pelo menos um provedor de modelo (OpenAI, Anthropic ou Google)

Para verificar se o Docker está instalado, abra o terminal e rode:

```bash
docker --version
```

Se aparecer algo como `Docker version 27.x.x`, está tudo certo.

---

## Estrutura do projeto

```
workshop-openclaw/
├── docker-compose.yml          # Configuração do container
├── .env.example                # Modelo de variáveis de ambiente
├── .env                        # Suas chaves (você cria este)
├── skills/
│   └── interview-agent/
│       └── SKILL.md            # A skill do entrevistador
├── possiveiserros.md           # Referência de troubleshooting
└── README.md                   # Este arquivo
```

---

## Passo 1 — Configurar as variáveis de ambiente

O OpenClaw precisa saber qual provedor de modelo usar. Isso é feito por variáveis de ambiente.

Copie o arquivo de exemplo e edite com suas chaves:

**No terminal:**
```bash
cp .env.example .env
```

**Ou pela interface:** duplique o arquivo `.env.example`, renomeie para `.env` e edite.

Dentro do `.env`, descomente a linha do provedor que você usa e cole sua chave:

```env
# Se você usa OpenAI:
OPENAI_API_KEY=sk-sua-chave-aqui

# Se você usa Anthropic (Claude):
# ANTHROPIC_API_KEY=sk-ant-sua-chave-aqui

# Se você usa Google (Gemini):
# GOOGLE_API_KEY=AI-sua-chave-aqui
```

Só precisa preencher **uma** das opções.

### De onde vem cada chave?

| Provedor | Onde pegar | Modelo recomendado | Gratuito? |
|----------|-----------|-------------------|-----------|
| Google Gemini | [aistudio.google.com](https://aistudio.google.com) | gemini-2.5-flash | Sim, sem cartão |
| Groq | [console.groq.com](https://console.groq.com) | llama-3.3-70b | Sim, sem cartão |
| OpenRouter | [openrouter.ai](https://openrouter.ai) | vários gratuitos | Sim, sem cartão |
| OpenAI | [platform.openai.com/api-keys](https://platform.openai.com/api-keys) | gpt-4o | Não |
| Anthropic | [console.anthropic.com](https://console.anthropic.com) | claude-sonnet-4 | Não |

---

## Passo 2 — Subir o OpenClaw com Docker

Com o `.env` configurado, suba o container:

```bash
docker compose up -d
```

O que esse comando faz:
- Baixa a imagem oficial do OpenClaw (na primeira vez)
- Cria um container chamado `openclaw-workshop`
- Monta a pasta `skills/` dentro do container para que o OpenClaw encontre a skill
- Expõe a porta 18789 para você acessar a interface web

Para ver se subiu corretamente:

```bash
docker compose logs -f
```

Espere até ver algo como `Gateway listening on port 18789`. Depois pressione `Ctrl+C` para sair dos logs.

### O que é o Gateway?

O Gateway é o "cérebro" do OpenClaw. É o serviço que:
- Recebe suas mensagens
- Decide qual skill usar
- Envia a mensagem para o modelo de linguagem
- Devolve a resposta

Tudo passa por ele. Quando você "sobe o OpenClaw", está subindo o Gateway.

---

## Passo 3 — Verificar se a skill foi carregada

Acesse a interface web do OpenClaw em:

```
http://localhost:18789
```

Na interface, você pode verificar as skills disponíveis na seção de configurações. A skill `interview_agent` deve aparecer na lista.

Se preferir verificar pelo terminal:

```bash
docker exec openclaw-workshop openclaw skills list
```

Deve aparecer `interview_agent` na lista.

### E se a skill não aparecer?

1. Verifique se o arquivo `skills/interview-agent/SKILL.md` existe e está correto
2. Reinicie o container:
   ```bash
   docker compose restart
   ```
3. Confira os logs para erros:
   ```bash
   docker compose logs openclaw
   ```

---

## Passo 4 — Entender a skill antes de usar

Antes de testar, vale entender o que a skill faz. Abra o arquivo `skills/interview-agent/SKILL.md` e leia.

A skill tem 5 modos de operação:

| Modo | O que faz | Quando usar |
|------|----------|-------------|
| Análise de vaga | Lê uma descrição de vaga e extrai competências, responsabilidades e riscos em JSON | Quando você tem uma vaga e quer entender o que avaliar |
| Plano de entrevista | Gera um roteiro com etapas, perguntas e critérios de avaliação | Quando quer montar a estrutura da entrevista |
| Condução | Faz perguntas uma a uma e avalia internamente | Quando quer simular a entrevista completa |
| Avaliação | Analisa uma resposta específica e dá nota com justificativa | Quando quer feedback sobre uma resposta isolada |
| Relatório final | Consolida tudo em um relatório com recomendação | Quando a entrevista terminou |

Cada modo responde em JSON estruturado, o que facilita integração com outros sistemas se você quiser expandir depois.

---

## Passo 5 — Testar: Analisar uma vaga

Vamos ao primeiro teste. Na interface web do OpenClaw (ou pelo terminal), envie esta mensagem:

### Pela interface web

Acesse `http://localhost:18789`, abra o chat e cole:

```
Use a skill interview_agent para analisar esta vaga em JSON:

Senior Backend Engineer - Fintech

Responsabilidades:
- Construir APIs críticas de pagamentos
- Trabalhar com microsserviços e arquitetura orientada a eventos
- Garantir observabilidade, escalabilidade e confiabilidade
- Atuar em incidentes de produção
- Colaborar com produto, segurança e infraestrutura

Requisitos:
- Experiência com Java, Kotlin ou Go
- Mensageria com Kafka ou RabbitMQ
- Bancos relacionais e NoSQL
- Cloud AWS ou GCP
- Forte capacidade de comunicação técnica

Diferenciais:
- Experiência com PCI-DSS
- Conhecimento de antifraude
- Experiência em ambientes regulados
```

### Pelo terminal

```bash
docker exec openclaw-workshop openclaw agent --message 'Use a skill interview_agent para analisar esta vaga em JSON:

Senior Backend Engineer - Fintech

Responsabilidades:
- Construir APIs críticas de pagamentos
- Trabalhar com microsserviços e arquitetura orientada a eventos
- Garantir observabilidade, escalabilidade e confiabilidade
- Atuar em incidentes de produção

Requisitos:
- Java, Kotlin ou Go
- Kafka ou RabbitMQ
- Bancos relacionais e NoSQL
- AWS ou GCP'
```

### O que esperar

Um JSON com a estrutura da vaga decomposta:

```json
{
  "cargo": "Senior Backend Engineer",
  "senioridade": "senior",
  "contexto_negocio": "Fintech",
  "responsabilidades_principais": ["..."],
  "competencias_tecnicas": ["..."],
  "competencias_comportamentais": ["..."],
  "conhecimento_dominio": ["..."],
  "temas_entrevista": ["..."],
  "sinais_risco": ["..."]
}
```

Isso é útil porque transforma uma vaga genérica em um mapa claro do que precisa ser avaliado.

---

## Passo 6 — Testar: Gerar plano de entrevista

Agora peça para montar o roteiro:

```
Use a skill interview_agent e gere um plano de entrevista técnica para a vaga de Senior Backend Engineer em Fintech.

A entrevista deve durar 60 minutos. Responda em JSON.
```

### O que esperar

Um plano com etapas, tempo estimado por etapa, perguntas sugeridas e critérios de avaliação (fraco/aceitável/forte) para cada uma.

Isso resolve um problema real: montar entrevistas consistentes. Em vez de cada entrevistador inventar perguntas na hora, você tem um roteiro padronizado.

---

## Passo 7 — Testar: Simular uma entrevista completa

Este é o teste mais interessante. Envie:

```
Use a skill interview_agent.

Quero começar uma entrevista técnica em modo candidato.

Vaga: Senior Backend Engineer - Fintech

Responsabilidades:
- Construir APIs críticas de pagamentos
- Trabalhar com microsserviços e arquitetura orientada a eventos
- Garantir observabilidade, escalabilidade e confiabilidade
- Atuar em incidentes de produção

Requisitos:
- Java, Kotlin ou Go
- Kafka ou RabbitMQ
- Bancos relacionais e NoSQL
- AWS ou GCP
- Comunicação técnica forte
```

### O que esperar

O assistente vai fazer a primeira pergunta no formato:

```
Pergunta 1:
Me conta sobre um sistema crítico de backend em que você trabalhou...

O que estou observando:
Experiência prática com sistemas críticos, trade-offs técnicos...
```

### Como continuar

Responda como se fosse o candidato. Por exemplo:

```
Trabalhei em uma API de pagamentos que processava cobranças recorrentes.
Tivemos problemas de duplicidade em retries, então implementamos
idempotency keys, logs estruturados e métricas por status de transação.
```

O assistente vai avaliar internamente e fazer a próxima pergunta. Continue respondendo até querer encerrar.

### Por que isso é útil?

- Para treinar candidatos antes de entrevistas reais
- Para calibrar entrevistadores (todos veem as mesmas perguntas e critérios)
- Para documentar o processo de avaliação

---

## Passo 8 — Testar: Avaliar uma resposta isolada

Se quiser avaliar uma resposta específica sem rodar a entrevista inteira:

```
Use a skill interview_agent para avaliar esta resposta.

Pergunta:
Me conta sobre um sistema crítico de backend em que você trabalhou.

Resposta do candidato:
Trabalhei em uma API de pagamentos recorrentes. O maior problema era evitar
cobranças duplicadas durante retries e falhas de rede. Usamos idempotency keys
por operação, transações no banco para consistência, filas para processamento
assíncrono e dashboards com métricas de taxa de erro, latência e status das
transações. Também tínhamos runbooks para incidentes.

Responda em JSON.
```

### O que esperar

```json
{
  "pergunta": "...",
  "resumo_resposta": "...",
  "competencia_avaliada": "...",
  "nota": "forte",
  "sinais_positivos": ["..."],
  "sinais_negativos": ["..."],
  "sinais_ausentes": ["..."],
  "pergunta_complementar": "..."
}
```

A avaliação separa sinais positivos, negativos e ausentes. Isso ajuda a entender não só "foi bem ou mal", mas exatamente onde o candidato se destacou e onde faltou evidência.

---

## Passo 9 — Testar: Gerar relatório final

Depois de algumas perguntas e respostas, peça o relatório:

```
Use a skill interview_agent para gerar relatório final.

Contexto:
Vaga: Senior Backend Engineer em fintech.

Evidências observadas:
1. Candidato explicou uso de idempotency keys, retries, transações e filas
   em sistema de pagamentos.
2. Demonstrou familiaridade com observabilidade: métricas de erro, latência
   e status de transações.
3. Mencionou runbooks e atuação em incidentes.
4. Pouca evidência sobre liderança técnica, colaboração com segurança e
   experiência com compliance PCI-DSS.

Responda em JSON.
```

### O que esperar

Um relatório com recomendação clara ("avançar", "em dúvida" ou "não avançar"), nível de confiança, pontos fortes, riscos e próximos passos sugeridos.

---

## Resumo do fluxo

```
1. Configurar chave de API          (.env)
2. Subir o container                (docker compose up -d)
3. Verificar skill carregada        (interface web ou terminal)
4. Analisar uma vaga                (Modo 1)
5. Gerar plano de entrevista        (Modo 2)
6. Simular entrevista completa      (Modo 3)
7. Avaliar respostas individuais    (Modo 4)
8. Gerar relatório final            (Modo 5)
```

---

## Troubleshooting

### O container não sobe

```bash
# Ver logs detalhados
docker compose logs openclaw

# Verificar se a porta está livre
docker ps -a
```

Causas comuns:
- Porta 18789 já em uso por outro serviço
- Docker Desktop não está rodando
- Arquivo `.env` com erro de formatação

### O modelo não responde ou dá erro de autenticação

Verifique se a chave de API está correta no `.env`. Teste a chave diretamente no site do provedor.

Se estiver usando um modelo específico, pode ser necessário configurar:

```bash
docker exec openclaw-workshop openclaw models set <provedor>/<modelo>
```

Exemplos:
```bash
docker exec openclaw-workshop openclaw models set openai/gpt-4o
docker exec openclaw-workshop openclaw models set anthropic/claude-sonnet-4
docker exec openclaw-workshop openclaw models set google/gemini-2.5-flash
```

### A skill não aparece

1. Confira se o caminho está correto: `skills/interview-agent/SKILL.md`
2. Confira se o YAML do cabeçalho está válido (sem tabs, com `---` no início e fim)
3. Reinicie: `docker compose restart`

### Resetar o modelo (se configurou errado)

```bash
docker exec openclaw-workshop openclaw models set google/gemini-2.5-flash
docker exec openclaw-workshop openclaw models fallbacks clear
docker exec openclaw-workshop openclaw models status
```

---

## Para ir além

- Crie novas skills para outros cenários (code review, onboarding, retrospectiva)
- Conecte o OpenClaw ao Telegram ou Slack para usar pelo celular
- Explore o [ClawHub](https://clawhub.ai) para skills prontas da comunidade
- Monte skills compostas que chamam outras skills

---

## Referências

- [Repositório oficial do OpenClaw](https://github.com/openclaw/openclaw)
- [Documentação de Skills](https://github.com/openclaw/openclaw/blob/main/docs/tools/skills.md)
- [ClawHub — Registry de Skills](https://clawhub.ai)
- [Docker Setup Guide](https://code-boost.com/how-to-install-openclaw-with-docker-step-by-step/)
