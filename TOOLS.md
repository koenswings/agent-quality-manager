# TOOLS.md — Veri, Quality Manager

## API Credentials
- `BASE_URL=http://172.18.0.1:8000`
- `AUTH_TOKEN` — load from `.env` in this directory (gitignored, never committed)
- `AGENT_NAME=Veri`
- `AGENT_ID=ac172302-3c45-4a51-bdb3-dc233a0f65e8`
- `BOARD_ID=d0cfa49e-edcb-4a23-832b-c2ae2c99bf67`
- `WORKSPACE_ROOT=/home/node/workspace`
- `WORKSPACE_PATH=/home/node/workspace/agents/agent-quality-manager`
- Required tools: `curl`, `jq`

## Environment

- **Quality manager repo:** `/home/node/workspace/agents/agent-quality-manager`
- **All agent repos (read access):** `/home/node/workspace/agents/agent-*/`
- **Org root:** `/home/node/workspace/` (CONTEXT.md, BACKLOG.md, proposals/, etc.)

## OpenAPI refresh (run before API-heavy work)

```bash
mkdir -p api
curl -fsS "http://172.18.0.1:8000/openapi.json" -o api/openapi.json
jq -r '
  .paths | to_entries[] as $p
  | $p.value | to_entries[]
  | select((.value.tags // []) | index("agent-lead"))
  | "\(.key|ascii_upcase)\t\($p.key)\t\(.value.operationId // "-")\t\(.value["x-llm-intent"] // "-")\t\(.value["x-when-to-use"] // [] | join(" | "))\t\(.value["x-routing-policy"] // [] | join(" | "))"
' api/openapi.json | sort > api/agent-lead-operations.tsv
```

## API discovery policy
- Use operations tagged `agent-lead`.
- Prefer operations whose `x-llm-intent` and `x-when-to-use` match the current objective.
