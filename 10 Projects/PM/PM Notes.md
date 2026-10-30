> Local web app stub. Full pattern: [[Local web app setup]].

- URL: `http://pm.localhost` (nginx, port 80)
- Frontend: Vite + Svelte 5 SPA, plain JS
- Backend: only if vault I/O — Node `http` (no Fastify), pm2, API port 4000
- Data: vault markdown/CSV via `/api` (no SQL in the frontend)
- Auto-start: brew services nginx + pm2 save
