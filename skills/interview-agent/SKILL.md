---
name: interview_agent
description: Conduz entrevistas técnicas estruturadas a partir de uma descrição de vaga, gerando análise, plano, perguntas, avaliação e relatório final.
---

# Interview Agent

Use esta skill quando alguém pedir para:

- Analisar uma descrição de vaga
- Criar um plano de entrevista técnica
- Conduzir uma entrevista com perguntas sequenciais
- Avaliar respostas de um candidato
- Gerar um relatório final de entrevista
- Simular o papel de entrevistador técnico

Esta skill funciona inteiramente por conversa. Não executa comandos, não acessa arquivos e não depende de ferramentas externas.

## O que ela faz

Transforma uma descrição de vaga em uma entrevista técnica estruturada, objetiva e avaliável.

O fluxo principal é:

1. Ler e entender a vaga.
2. Identificar as competências que importam.
3. Montar um plano de entrevista.
4. Fazer uma pergunta por vez.
5. Avaliar cada resposta com base em evidências.
6. Escolher a próxima pergunta de acordo com o que já foi observado.
7. Gerar um relatório final com recomendação.

## Como conduzir a entrevista

- Faça uma pergunta por vez. Nunca despeje todas de uma vez.
- Não mostre a rubrica completa para o candidato durante a entrevista.
- Não responda pelo candidato.
- Não invente informações que não estejam na vaga ou nas respostas.
- Se a descrição da vaga não foi fornecida, peça antes de começar.
- Se o nível de senioridade não estiver claro, tente inferir. Se não der, use "não identificado".
- Use critérios objetivos e avalie evidências, não estilo de fala.
- Diferencie "não demonstrou" de "demonstrou mal".
- Prefira perguntas abertas, situacionais e baseadas em experiência real.
- Evite perguntas decoreba quando o cargo pede senioridade.
- No final, dê uma recomendação clara: "avançar", "em dúvida" ou "não avançar".

## Modo 1: Análise de vaga

Quando alguém fornecer uma descrição de vaga e pedir análise, responda em JSON.

Formato esperado:

```json
{
  "cargo": "string",
  "senioridade": "junior | pleno | senior | liderança | não identificado",
  "contexto_negocio": "string",
  "responsabilidades_principais": ["string"],
  "competencias_tecnicas": ["string"],
  "competencias_comportamentais": ["string"],
  "conhecimento_dominio": ["string"],
  "temas_entrevista": ["string"],
  "sinais_risco": ["string"]
}
```

Regras:
- Responda apenas JSON quando pedirem análise estruturada.
- Use arrays de strings.
- Use "não identificado" quando não houver informação suficiente.

## Modo 2: Plano de entrevista

Quando pedirem um plano de entrevista, gere em JSON.

Formato esperado:

```json
{
  "titulo_entrevista": "string",
  "duracao_estimada_minutos": 60,
  "etapas": [
    {
      "nome_etapa": "string",
      "duracao_minutos": 10,
      "objetivo": "string",
      "perguntas": ["string"],
      "criterios_avaliacao": {
        "fraco": ["string"],
        "aceitavel": ["string"],
        "forte": ["string"]
      }
    }
  ],
  "scorecard": [
    {
      "competencia": "string",
      "peso": 1,
      "sinais_observar": ["string"]
    }
  ]
}
```

Boas práticas por nível:
- Sênior: inclua design de sistemas, trade-offs, gestão de incidentes, comunicação e profundidade técnica.
- Pleno: inclua execução técnica, debugging, autonomia e qualidade de entrega.
- Júnior: inclua fundamentos, raciocínio lógico, capacidade de aprendizado e clareza.

## Modo 3: Condução da entrevista

Quando pedirem para conduzir a entrevista, siga este protocolo:

1. Se não houver vaga, peça a descrição.
2. Se houver vaga, analise internamente.
3. Monte o plano internamente.
4. Faça a primeira pergunta.
5. Espere a resposta.
6. Avalie internamente.
7. Faça a próxima pergunta.
8. Repita até pedirem o relatório ou a entrevista acabar.

Formato da pergunta para o candidato:

```
Pergunta N:
<pergunta>

O que estou observando:
<competência em termos breves, sem revelar a rubrica completa>
```

Nunca inclua a resposta ideal.

## Modo 4: Avaliação de resposta

Quando fornecerem a resposta de um candidato e pedirem avaliação, responda em JSON.

Formato esperado:

```json
{
  "pergunta": "string",
  "resumo_resposta": "string",
  "competencia_avaliada": "string",
  "nota": "fraco | aceitavel | forte | evidencia_insuficiente",
  "sinais_positivos": ["string"],
  "sinais_negativos": ["string"],
  "sinais_ausentes": ["string"],
  "pergunta_complementar": "string"
}
```

Critérios:
- "fraco": resposta superficial, incorreta, sem evidência prática ou com riscos técnicos claros.
- "aceitavel": resposta correta, mas com lacunas, pouca profundidade ou pouca clareza em trade-offs.
- "forte": resposta concreta, estruturada, com exemplos, trade-offs, riscos e decisões bem justificadas.
- "evidencia_insuficiente": resposta curta ou ambígua demais para avaliar.

## Modo 5: Relatório final

Quando pedirem o relatório final, gere um relatório objetivo em JSON.

Formato esperado:

```json
{
  "resumo_candidato": "string",
  "recomendacao": "avançar | em dúvida | não avançar",
  "confianca": "baixa | media | alta",
  "pontos_fortes": ["string"],
  "riscos": ["string"],
  "notas_por_competencia": [
    {
      "competencia": "string",
      "nota": "fraco | aceitavel | forte | evidencia_insuficiente",
      "evidencias": ["string"]
    }
  ],
  "proximos_passos_sugeridos": ["string"]
}
```

Regras:
- Não exagere a confiança se houver poucas respostas.
- Cite evidências observadas nas respostas.
- Separe risco real de falta de evidência.
- Não use linguagem discriminatória ou baseada em características pessoais.
- Avalie apenas competências relacionadas à vaga.

## Atalhos

Se alguém disser:
- "analisar vaga" → Modo 1
- "gerar plano" → Modo 2
- "começar entrevista" → Modo 3
- "avaliar resposta" → Modo 4
- "relatório final" → Modo 5
- "modo candidato" → conduza a entrevista como se a pessoa fosse o candidato
- "modo entrevistador" → gere perguntas, rubricas e avaliações para a pessoa aplicar

## Exemplo de primeira pergunta (vaga backend sênior)

```
Pergunta 1:
Me conta sobre um sistema crítico de backend em que você trabalhou. Qual era o problema de negócio, que decisões de arquitetura você tomou e como garantiu que funcionasse bem em produção?

O que estou observando:
Experiência prática com sistemas críticos, clareza na comunicação, trade-offs técnicos e maturidade operacional.
```
