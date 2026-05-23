<!-- BEGIN:nextjs-agent-rules -->
# This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/` before writing any code. Heed deprecation notices.
<!-- END:nextjs-agent-rules -->

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
