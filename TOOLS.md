# TOOLS.md — Quality Manager

## Environment

- **QM workspace:** `/home/node/workspace/agents/agent-quality-manager`
- **Org root:** `/home/node/workspace/` (CONTEXT.md, BACKLOG.md, proposals/, standups/)
- **Read access to all agent repos:** `/home/node/workspace/agents/`

## GitHub Access

Use `gh` CLI to list and review open PRs:
```
gh pr list --repo koenswings/agent-engine-dev
gh pr list --repo koenswings/agent-console-dev
gh pr list --repo koenswings/agent-site-dev
gh pr view <number> --repo koenswings/<repo>
gh pr diff <number> --repo koenswings/<repo>
```

Leave review comments:
```
gh pr comment <number> --repo koenswings/<repo> --body "..."
```

## Notes

_(Repos currently under `koenswings`; will transfer to the org once the name is confirmed. Update repo paths at that point.)_

## GitHub Push & PR

`gh` is not available in the sandbox. Use `git` + `curl` with `GITHUB_TOKEN` from `.env`.

### Push a branch
```bash
source .env
git remote set-url origin https://koenswings:${GITHUB_TOKEN}@github.com/koenswings/agent-quality-manager.git
git push origin BRANCH_NAME
git remote set-url origin https://github.com/koenswings/agent-quality-manager.git
```

### Open a PR
```bash
source .env
curl -s -X POST "https://api.github.com/repos/koenswings/agent-quality-manager/pulls" \
  -H "Authorization: token ${GITHUB_TOKEN}" \
  -H "Content-Type: application/json" \
  -d "{
    \"title\": \"PR TITLE\",
    \"head\": \"BRANCH_NAME\",
    \"base\": \"main\",
    \"body\": \"PR description\"
  }" | python3 -c "import sys,json; print(json.load(sys.stdin).get('html_url','error'))"
```

Replace `agent-quality-manager` and `BRANCH_NAME` with the actual values for your repo.
`GITHUB_TOKEN` must be present in `.env` (gitignored, never committed).
