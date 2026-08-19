# OPS Control — Gestão Operacional

App de gestão operacional (grelha de presenças, colaboradores, lojas, clientes e backups), em ficheiro único `index.html` (React 18 + Tailwind + Firebase), sem build step, hospedado via GitHub Pages.

## Stack
- React 18 (UMD) + Babel Standalone (compilado em runtime)
- Tailwind CSS (CDN)
- Chart.js (CDN)
- Firebase Firestore (compat SDK) — projeto `presencasde`

## Estrutura de dados (Firestore)
- `stores/{id}` — lojas (nome, clienteId, categoria, ccCode, meta, limBackup, cor, ordem, tipo: "normal"|"backup_pool")
- `clients/{id}` — clientes (nome, cor)
- `employees/{id}` — colaboradores (nome, lojaId, turno, veiculo, funcao, estado, desligadoData, folgaFixa: [0-6])
- `schedule/{YYYY-MM}` — **um único documento por mês**, com um mapa `cells` de `empId_dia -> status`. Isto minimiza leituras (1 documento por mês em vez de 1 por dia/colaborador).
- `backups/{id}` — registos de backup (data, colaboradorId, tipo, origem, destino, duracao, custo, motivo)
- `auditLog/{id}` — histórico de edição (timestamp, user, acao, entidade, alvo, detalhes) — últimos 300 eventos exibidos na aba Histórico

## Configurar o Firestore
1. Firebase Console → projeto `presencasde` → Firestore Database → separador **Regras**.
2. Cola o conteúdo de [`firestore.rules`](./firestore.rules) e publica.
3. Não são necessários índices compostos adicionais (as queries usam apenas range no campo `data`).

## Publicar no GitHub Pages
```bash
git init
git add .
git commit -m "OPS Control — versão inicial"
git branch -M main
git remote add origin https://github.com/malvim23/ops-control.git
git push -u origin main
```
Depois, em GitHub → repositório → **Settings → Pages** → Source: `main` / `/(root)`. A app fica disponível em:
`https://malvim23.github.io/ops-control/`

## Notas
- Login simples por nome (guardado em `localStorage`, chave `opsUser`) — sem Firebase Auth.
- Cache local do Firestore ativada (`enablePersistence`) para reduzir leituras repetidas.
- Para trocar de projeto Firebase, edita o objeto `firebaseConfig` no topo do `index.html`.
