# Role Dispatcher

Skill per Claude Code, Codex CLI e OpenCode che analizza le richieste e dispatcha agenti specializzati da 209+ ruoli IT.

## Project Structure

- `skill/role-dispatcher/SKILL.md` — Logica principale del dispatcher
- `skill/role-dispatcher/assets/roles/` — 14 file con i ruoli organizzati per categoria
- `skill/role-dispatcher/references/` — Protocolli, template, esempi e guida modelli
- `bin/install.js` — Script di installazione via npx
- `install.sh` / `install.ps1` — Script di installazione manuali

## Non-Negotiable Rules

- Always use TypeScript strict mode
- Use pnpm, never npm
- Never modify the database schema directly

## Development Rules

- I file dei ruoli in `assets/roles/` hanno header solo in inglese (es. `# Software Development`)
- Le descrizioni dei ruoli dentro i file sono in inglese
- Il dispatcher risponde nella lingua dell'utente, ma il matching interno usa keyword inglesi
- Massimo 3 agenti per richiesta
- I path nei file sono relativi alla directory della skill

## Publishing

- Package npm: `@alexgabbia/role-dispatcher`
- Repo GitHub: `AlexGabbia/role-dispatcher`
- Prima di pubblicare: aggiornare la versione in `package.json`
- Comando: `npm publish --access public` (richiede autenticazione OTP)

## Key Files to Know

| File | Purpose |
|------|---------|
| `SKILL.md` | Entry point della skill, contiene il flusso di dispatch |
| `references/role-index.md` | Indice categorie con keyword per il matching |
| `references/collaboration-protocol.md` | Protocollo coordinamento multi-agente |
| `references/model-selection-guide.md` | Matrice selezione modello (Haiku/Sonnet/Opus) |
| `references/prompt-templates.md` | Template prompt strutturati per gli agenti |
| `references/examples.md` | 4 scenari completi di dispatching |
