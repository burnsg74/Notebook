> Local web app stub. Full pattern: [[Local web app setup]]. Note: job leads are planned inside Alfred; use this only if a separate local UI still makes sense.

- URL: `http://jobhunt.localhost` (nginx, port 80)
- Frontend: Vite + Svelte 5 SPA, plain JS
- Backend: only if vault I/O — Node `http` (no Fastify), pm2, API port 4002
- Data: vault markdown/CSV via `/api` (no SQL in the frontend)
- Auto-start: brew services nginx + pm2 save
