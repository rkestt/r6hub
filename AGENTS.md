<!-- BEGIN:nextjs-agent-rules -->
# This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/` before writing any code. Heed deprecation notices.
<!-- END:nextjs-agent-rules -->

# Local Project Info
Questo e' un side-project dove l'utente non deve mettere mano nel 99% dei casi, la maggiorparte del lavoro deve farla l'agente
<!-- BEGIN:agent-rules -->
# DELEGA SEMPRE AI SUBAGENTS
- **SEMPRE** delegare il lavoro ai subagents disponibili
- Non eseguire mai direttamente il lavoro se esiste un agent specializzato
- Usare il subagent appropriato per ogni task:
  - `@explorer` - ricerche nel codebase
  - `@librarian` - documentazione librerie
  - `@oracle` - decisioni architetturali, code review
  - `@designer` - UI/UX, frontend design
  - `@fixer` - implementazioni, test

# USA SEMPRE LE SKILL DISPONIBILI
Skill disponibili:
- `agent-browser` - automazione browser, test QA
- `code-review-excellence` - code review best practices
- `codemap` - mappatura repository sconosciuti
- `customize-opencode` - configurazione opencode
- `find-skills` - scoperta nuove skill
- `frontend-design` - interfacce frontend di qualita
- `game-design-document` - documentazione game design
- `godot-gdscript-patterns` - pattern Godot 4 GDScript
- `huashu-design` - prototipi HTML, animazioni, design
- `impeccable` - design review, polish UI
- `modern-best-practice-react-components` - componenti React puliti
- `openspec-apply-change` - implementare change OpenSpec
- `openspec-archive-change` - archiviare change completati
- `openspec-explore` - mode esplorazione OpenSpec
- `openspec-propose` - proporre change OpenSpec
- `programmatic-seo` - pagine SEO su larga scala
- `remotion-best-practices` - video Remotion
- `seo-audit` - audit SEO
- `simplify` - semplificazione codice
- `stitch-design` - design system Stitch
- `vercel-react-best-practices` - ottimizzazione React/Next.js
- `vercel-react-native-skills` - best practice React Native
- `web-design-guidelines` - review UI/UX, accessibilita
- `writing-plans` - piani per task multi-step

Caricare una skill con: `skill` tool quando il task matcha.

# USA SEMPRE /CAVEMAN
- **SEMPRE** usare `/caveman` per comunicare
- Linguaggio semplice, diretto, essenziale
- Niente fronzoli, niente spiegazioni lunghe
- Solo informazioni necessarie
<!-- END:agent-rules -->

## graphify

This project has a knowledge graph at graphify-out/ with god nodes, community structure, and cross-file relationships.

When the user types `/graphify`, invoke the `skill` tool with `skill: "graphify"` before doing anything else.

Rules:
- For codebase questions, first run `graphify query "<question>"` when graphify-out/graph.json exists. Use `graphify path "<A>" "<B>"` for relationships and `graphify explain "<concept>"` for focused concepts. These return a scoped subgraph, usually much smaller than GRAPH_REPORT.md or raw grep output.
- Dirty graphify-out/ files are expected after hooks or incremental updates; dirty graph files are not a reason to skip graphify. Only skip graphify if the task is about stale or incorrect graph output, or the user explicitly says not to use it.
- If graphify-out/wiki/index.md exists, use it for broad navigation instead of raw source browsing.
- Read graphify-out/GRAPH_REPORT.md only for broad architecture review or when query/path/explain do not surface enough context.
- After modifying code, run `graphify update .` to keep the graph current (AST-only, no API cost).

### For @explorer
When searching the codebase, use the knowledge graph as primary source:
1. Read `graphify-out/GRAPH_REPORT.md` to identify relevant communities and god nodes
2. Use `grep` on `graphify-out/graph.json` to find specific nodes, edges, and source files
3. Use `graphify query "<question>"` if you have shell access, otherwise query the JSON directly
4. Only fall back to raw `grep` across source files if the graph has no relevant entries

## Browser Testing (agent-browser)

**IMPORTANTE**: Avviare sempre agent-browser con init script per bypassare dialog nativi:

```bash
agent-browser --init-script test-scripts/confirm-bypass.js open http://localhost:3000
```

Lo script `test-scripts/confirm-bypass.js` bypassa `confirm()`, `alert()`, `prompt()` che altrimenti bloccano agent-browser.

### Setup Infrastruttura Locale

- **Supabase self-hosted**: `docker compose --env-file .env.supabase up -d`
- **Migrations**: `.\scripts\apply-migrations.ps1` (tracking con tabella `schema_migrations`)
- **Dev server**: `npm run dev` su `localhost:3000`
- **Email test**: Mailpit su `localhost:8025` (API: `/api/v1/messages`)
- **Studio DB**: `localhost:54322` (user: supabase, pass: vedi `.env.supabase`)

### Flow Login Test

1. Fill email con JS injection (React non accetta `.value = ...` diretto)
2. Click "Send Magic Link"
3. Leggi email da Mailpit API
4. Estrai magic link da HTML
5. Naviga link per completare auth
