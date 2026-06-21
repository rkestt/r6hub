<!-- BEGIN:nextjs-agent-rules -->
# This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/` before writing any code. Heed deprecation notices.
<!-- END:nextjs-agent-rules -->

# Local Project Info
Questo e' un side-project dove l'utente non deve mettere mano nel 99% dei casi, la maggiorparte del lavoro deve farla l'agente

Lavorare sempre in autonomo, testare, fare meno domande possibile
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

This project has a knowledge graph at `graphify-out/` with god nodes, community structure, and cross-file relationships.

### Comandi Graphify

| Comando | Uso | Costo |
|---------|-----|-------|
| `graphify update .` | Aggiorna grafo dopo modifiche codice (AST-only) | Gratis |
| `graphify extract . --backend opencode-go --model deepseek-v4-flash` | Estrazione semantica completa (docs, immagini) | Incluso nel piano Go |
| `graphify cluster-only . --backend opencode-go` | Rinomina community e rigenera report | Incluso nel piano Go |
| `graphify query "<domanda>"` | Query mirata sul grafo | Gratis |
| `graphify path "<A>" "<B>"` | Relazioni tra due nodi | Gratis |
| `graphify explain "<concetto>"` | Spiegazione concetto | Gratis |

### Workflow

1. **Dopo modifiche codice**: `graphify update .` (sempre, gratis)
2. **Dopo aggiunta docs/immagini**: `graphify extract . --backend opencode-go --model deepseek-v4-flash` (richiede `OPENCODE_GO_API_KEY`)
3. **Per domande sul codebase**: prima `graphify query`, poi grep solo se necessario

### Regole

- Per domande sul codebase, usa prima `graphify query "<question>"` - restituisce subgrafo scoped, molto più piccolo di GRAPH_REPORT.md
- Se `graphify-out/wiki/index.md` esiste, usalo per navigazione ampia
- Leggi `graphify-out/GRAPH_REPORT.md` solo per review architettura ampia
- Dopo modifiche codice, esegui `graphify update .` per mantenere il grafo attuale

### Per @explorer

Quando cerchi nel codebase, usa il knowledge graph come fonte primaria:
1. Leggi `graphify-out/GRAPH_REPORT.md` per identificare community e god nodes rilevanti
2. Usa `grep` su `graphify-out/graph.json` per nodi, edges, file sorgente specifici
3. Usa `graphify query "<question>"` se hai shell access
4. Solo se il grafo non ha entry rilevanti, usa `grep` sui file sorgente

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

---

## Hetzner Dev Environment

Il server Hetzner e' l'ambiente di sviluppo principale. Tutto gira li'.

### Connessione SSH

```
Host: 142.132.176.234
User: root
Key:  C:\Users\andre\.ssh\id_ed25519
DNS:  r6hub.duckdns.org
```

### Infrastruttura Server

| Servizio | Container | Porta | URL |
|----------|-----------|-------|-----|
| Next.js PRODUZIONE | `r6hub-nextjs` | 3000 (interno) | `https://r6hub.duckdns.org` |
| Next.js DEV (hot-reload) | `r6hub-nextjs-dev` | 3001 | `http://142.132.176.234:3001` |
| Caddy (reverse proxy + SSL) | `r6hub-caddy` | 80, 443 | Let's Encrypt |
| Supabase DB | `supabase-db` | 54324 | Postgres 15 |
| Supabase Auth | `supabase-auth` | interno | GoTrue v2 |
| Supabase Storage | `supabase-storage` | interno | |
| Supabase Realtime | `realtime-dev.supabase-realtime` | 4000 | |
| Supabase Studio | `supabase-studio` | 54322 | Admin UI |
| Kong (API Gateway) | `supabase-kong` | 54321, 54323 | |
| Mailpit (email test) | `supabase-mailpit` | 8025 | `http://142.132.176.234:8025` |

### Repo sul server

```
/opt/r6hub/          # codice sorgente (git clone da GitHub)
/opt/r6hub/.env      # env vars produzione
/opt/r6hub/.env.supabase  # env vars Supabase
/opt/r6hub/docker-compose.yml      # compose principale
/opt/r6hub/docker-compose.dev.yml  # override dev (profile: dev)
/opt/r6hub/dev.sh    # helper script server
```

### Workflow Dev

1. **Modifichi codice** su Windows (`C:\Projects\r6Hub`)
2. **Sync su Hetzner**: `.\sync.ps1 q` (quick, tar+scp, ~5s)
3. **Dev server** su Hetzner rileva cambiamenti → hot-reload automatico (~1s)
4. **Vedi modifiche** su `http://142.132.176.234:3001`

### Sync Script (Windows)

```powershell
.\sync.ps1 q    # Quick sync (tar+scp, files only)
.\sync.ps1 g    # Git sync (commit+push+pull, safe)
.\sync.ps1 s    # SSH shell
.\sync.ps1 l    # Dev server logs (tail -f)
```

### Server Helper Script

```bash
cd /opt/r6hub
./dev.sh start    # Start dev server (porta 3001)
./dev.sh stop     # Stop dev server
./dev.sh restart  # Restart dev server
./dev.sh logs     # Tail dev logs
./dev.sh npm <cmd>  # Run npm command in dev container
./dev.sh rebuild  # Reinstall node_modules
./dev.sh status   # Container status
```

### Comandi Docker utili

```bash
# Start dev server
docker compose --env-file .env.supabase -f docker-compose.yml -f docker-compose.dev.yml --profile dev up -d nextjs-dev

# Stop dev server
docker compose --env-file .env.supabase -f docker-compose.yml -f docker-compose.dev.yml --profile dev down

# Logs dev
docker logs r6hub-nextjs-dev --tail 50 -f

# Logs produzione
docker logs r6hub-nextjs --tail 50 -f

# Rebuild produzione (dopo git pull)
docker compose --env-file .env.supabase build nextjs
docker compose --env-file .env.supabase up -d nextjs
```

### Firewall Hetzner

Porte aperte: 22 (SSH), 80 (HTTP), 443 (HTTPS), 3001 (dev), 54321-54324 (Supabase), 8025 (Mailpit), 4000 (Realtime)

### API Key Hetzner

Token: `Tt3UaiNzfztaLnGToFs5kqIrnXkagxU5mw2Q90DVW3r70SSZy4zzMP23E3S9ZU8m`
Server ID: `142381430`
Firewall ID: `11151434`
