# Erros comuns e como resolver

## Modelo respondendo mal ou ignorando a skill

Se o modelo estiver respondendo de forma genérica e ignorando a skill, pode ser que o modelo configurado seja fraco demais. Modelos muito pequenos (como variantes "lite" ou "mini") nem sempre seguem instruções complexas de skills.

**Solução:** troque para um modelo mais capaz.

```bash
docker exec openclaw-workshop openclaw models set google/gemini-2.5-flash
docker exec openclaw-workshop openclaw models fallbacks clear
docker exec openclaw-workshop openclaw models status
```

Modelos recomendados para este workshop:
- `openai/gpt-4o`
- `anthropic/claude-sonnet-4`
- `google/gemini-2.5-flash`

Evite modelos "lite", "mini" ou "nano" — eles costumam não seguir bem as instruções da skill.

---

## Container não inicia

```bash
# Ver o que está acontecendo
docker compose logs openclaw

# Verificar se já tem algo na porta 18789
docker ps -a
```

Se a porta estiver ocupada, mude no `docker-compose.yml`:
```yaml
ports:
  - "18790:18789"  # mude a porta da esquerda
```

---

## Chave de API inválida

Sintomas: erro 401, "unauthorized", ou o modelo simplesmente não responde.

1. Abra o `.env` e confira se a chave está correta (sem espaços extras)
2. Confira se descomentou a linha certa (removeu o `#` do início)
3. Depois de editar o `.env`, reinicie:
   ```bash
   docker compose down
   docker compose up -d
   ```

---

## Skill não aparece na lista

1. Confira se o arquivo existe em `skills/interview-agent/SKILL.md`
2. Confira se o cabeçalho YAML está correto:
   ```yaml
   ---
   name: interview_agent
   description: Conduz entrevistas técnicas...
   ---
   ```
3. Não use tabs no YAML, apenas espaços
4. Reinicie o container: `docker compose restart`

---

## Respostas em inglês quando você quer português

Adicione no início da sua mensagem:

```
Responda em português brasileiro.
```

Ou configure no prompt do agente principal para sempre responder em pt-BR.

---

## Docker Desktop não está rodando

No Windows: abra o Docker Desktop pelo menu Iniciar e espere ele ficar "verde" (running).

Teste com:
```bash
docker info
```

Se der erro, o Docker não está rodando ainda.
