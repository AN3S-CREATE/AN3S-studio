# AGENTS.md

## Cursor Cloud specific instructions

This is a single Next.js 15 (App Router) + Genkit AI app named "Intern Us" (an AI career
platform). Package manager is **npm** (`package-lock.json`). Dependencies are installed
automatically by the startup update script (`npm install`).

### Services

- **Next.js web app** (the only service). Dev: `npm run dev` → http://localhost:9002
  (Turbopack, port hardcoded to 9002 in `package.json`). This serves the landing page and
  runs the Genkit AI flows in-process as Next.js Server Actions.
- **Genkit dev UI** (optional, for inspecting AI flows): `npm run genkit:dev`.

### AI flows / API key (non-obvious)

- The Genkit flows in `src/ai/flows/` call Google Gemini (`googleai/gemini-2.5-flash`) via
  `@genkit-ai/google-genai`. That plugin reads `GEMINI_API_KEY` (or `GOOGLE_API_KEY`).
- The repo expects a `.env` (loaded by `src/ai/dev.ts`), but none is committed. In this
  environment the working key is available as `GEMINI_AI_API_KEY`. To run AI flows, map it,
  e.g. `GEMINI_API_KEY="$GEMINI_AI_API_KEY" npm run dev` (or `genkit:dev`). Without a key,
  the landing page still works but AI flows fail at call time.
- The AI flows are NOT yet wired into any UI page/button; the rendered app is currently just
  the marketing landing page. Exercise flows directly (e.g. a small `tsx` script importing a
  flow, or the Genkit dev UI), not through the website.

### Build / lint / typecheck (non-obvious gotchas)

- `npm run build` works and is the most reliable verification.
- `npm run typecheck` (`tsc --noEmit`) currently FAILS on a pre-existing react-day-picker API
  mismatch in `src/components/ui/calendar.tsx`. The build intentionally ignores this
  (`typescript.ignoreBuildErrors: true` in `next.config.ts`).
- `npm run lint` (`next lint`) is INTERACTIVE — no ESLint config exists, so it prompts and
  blocks. Do not run it in non-interactive/startup contexts. Builds ignore ESLint anyway
  (`eslint.ignoreDuringBuilds: true`).
- There is **no test framework / `test` script** in this repo.

### Notes

- Firebase is a declared dependency but is currently inert (no Firebase code in `src/`,
  emulators disabled in `.idx/dev.nix`). Safe to ignore for running/testing today.
- Node 20 is the documented target (`.idx/dev.nix`); Node 22 also runs the app and build fine.
