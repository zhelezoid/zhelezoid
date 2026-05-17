# zhelezoid (GitHub org profile) — CLAUDE.md

Публичный README профиля организации zhelezoid на GitHub.

## Pipeline-подключение

Этот репо обслуживается централизованным pipeline `zhelezoid/ai-infrastructure` (orchestrator живёт там как подкаталог `orchestrator/`; локально `~/Developer/orchestrator` — symlink на sparse checkout).

### Как делается commit

1. Изменения коммитятся локально (Денис через Claude в сессии или Mac Worker pipeline)
2. `pre-commit` hook (`~/bin/pre-commit-combined.sh`, symlink установлен во всех 29 репо):
   - **gitleaks scan** — блокирует commit если найден secret (API keys, tokens, passwords)
   - **anti-drift check** — блокирует удаление файла на который ссылается живая Module-нота из `~/ObsidianVault/Modules/`
   - Bypass запрещён (`--no-verify` не использовать)

### Как делается push

1. `git push origin main`
2. GitHub принимает push → запускает `post-commit` hook локально (`~/bin/post-commit-cross-review.sh`):
   - Определяет автора commit'а по `Co-Authored-By:` в commit message
   - Запускает `cross-review.sh`: противоположная модель (Claude↔DeepSeek) ревьюит diff
   - Создаёт kanban-задачу с вердиктом APPROVE / CONCERNS / REJECT
3. code-graph reindex автоматически после push (порт `:3011` всегда свежий)

### Как делается deploy

Это публичный README профиля org `zhelezoid` — деплоя нет, GitHub рендерит автоматически.

### Как делается merge

Direct push на `main`. PR flow не используется.

### Миграции / переименования

Репо self-contained, миграций не было.

### Что НЕ работает (известный bug)

GitHub webhook `tasks.zheleznyak.net/deploy/webhook` принимает push (status 200), но execute fails на ssh+DNS внутри container (BUG-007 в `~/ObsidianVault/Orchestrator/BUGS.md`). Не блокер — нет deploy-цели.

Архитектура pipeline: `~/ObsidianVault/Modules/Pipeline-as-maintainer.md`
