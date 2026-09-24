# Упражнение 1 — План за провеждане (за асистента)

**Програмиране за Интернет · 2026/27 · 4 курс бакалаври, зимен семестър · 90 мин · вторник, 1211, блок 1 и 2, подгрупи a/b през седмица**
За първия час: [FIRST-SESSION.md](FIRST-SESSION.md) (подготовка, ход по минути, Teams шаблон, присъствен лист). Източник за setup/лимити: [webdev-course.tu-sofia.workers.dev](https://webdev-course.tu-sofia.workers.dev/bg/guides/setup/).
Студентски шаблон: `antouanbg/fip-course-template` (→ студентът прави `fip-<ФН>`) · Решение: `instructor/solution/index.html`

## Какво е взето от магистърския шаблон (MrKotov/webdev-course-template) и какво е опростено

| Магистри (webdev-course-template) | Бакалаври 4 курс (fip-course-template) | Защо |
|---|---|---|
| **Упр. 1: ръчно написан MCP сървър** (JSON-RPC по stdio, 2 tools, `check.mjs`, description experiment) | **Упр. 1: HTML страница + `fetch()` към JSON API** — Част A на ръка, Част B с агент, `checks/ex1.mjs`, *prompt experiment* | MCP предполага лекции 1–2 за агенти; бакалавърският конспект (Наков) тръгва от HTTP/HTML/JS. Запазени са същите умения: протокол + JSON, „грешка в данните" vs „грешка в протокола" (тук: `response.ok` vs network error), агентът като reviewer, експеримент с формулировката. |
| Codespaces + Gemini CLI | Codespaces + **Gemini CLI** (≈1000 заявки/ден безплатно), Copilot Chat и уеб чат като алтернативи | Същият агент като при магистрите; Copilot Free е само ≈50 заявки/месец |
| devcontainer: Python + Node + Docker + SQLite | Node 22 + GitHub CLI + Live Server | Упр. 1–2 ползват само Node; Docker/DB се добавят в Упр. 3–4 |
| `AGENTS.md`, `AGENT_LOG.md`, `USAGE.md`, `SPEC.md`, `CLAUDE.md` | същите; `AGENTS.md` е предварително попълнен за Упр. 1; `USAGE.md` приема брой prompt-ове, ако инструментът не показва токени | Навикът се изгражда от първия ден |
| Self-check по упражнение, `git tag exN` за предаване, CI при push | същото (`checks/ex1.mjs`, `tag ex1`, CI само Node) | |
| Упр. 2–5: spec → feature → deploy/READINESS → adversarial review | Предложение по-долу — същата логика, по-лек обем | |

## Времева рамка

| Мин | Блок | Какво прави асистентът |
|---|---|---|
| 0–15 | Setup | Use this template → `fip-<ФН>` → Collaborator → Codespace (стартира 2–4 мин: пуснете го **първо**, обяснявайте докато зарежда). Агент: `gemini` в терминала (вход с Google, кодът се поставя обратно). **Съвет:** пратете README-то по Teams ден по-рано с молба акаунтите да са готови. |
| 15–25 | Въведение | 5 мин: frontend/backend/DB върху `ex1/index.html`; `fetch`, Promise, JSON. 5 мин: `PROMPTS.md` — роли, контролни точки, чеклист, `AGENT_LOG.md`. Правило: „не можеш да обясниш → не предаваш". |
| 25–35 | Част A | На ръка, без агент. При A4 минете по залата — всеки показва заявката в Network. |
| 35–65 | Част B | С агент. Ключов момент **B2**: контролната точка е *преди* приемане на кода — тестват трите сценария. Повечето агентски решения нямат `response.ok`. |
| 65–75 | C / буфер | Бонус за бързите; помощ на изоставащите с B3. `node checks/ex1.mjs` — всеки го пуска. |
| 75–85 | Разбор | Въпросите по-долу; 2–3 студента показват запис от `AGENT_LOG.md` и *prompt experiment*-а си. |
| 85–90 | Предаване | `git tag ex1 && git push origin ex1`; проверка на 2–3 repo-та на живо. |

**Ако групата изостава:** B4 и C стават за вкъщи (self-check-ът ги отчита като бележка, не като FAIL); присъствие при tag с A1–A4 + B1–B3.

## Контролни точки за присъствие (физическо + изпълнена задача)

- [ ] `fip-<ФН>` с tag `ex1`, преподавателят е collaborator
- [ ] `node checks/ex1.mjs` минава (18/18) — или студентът обяснява кое не и защо
- [ ] B2: може да каже кой сценарий влиза в `catch` и защо 404 не влиза без `response.ok`
- [ ] `AGENT_LOG.md`: ≥1 запис с *Went wrong* / *Changed by hand*, които не са празни
- [ ] *Prompt experiment*: 3 изречения, различни от колегата до него

## Разбор — въпроси и очаквани отговори

| Въпрос | Очакван отговор |
|---|---|
| Кое е frontend, кое backend, къде е DB? | Страницата в браузъра / `api.chucknorris.io` / зад API-то, невидима — говорим с нея само през HTTP. |
| `fetch` хвърля ли при 404? | **Не.** Отхвърля Promise-а само при мрежова грешка (DNS, offline, CORS). При 404/500 → `response.ok === false`, трябва ръчна проверка. |
| Защо два `await`? | Първият — Response (headers пристигнали). Вторият — тялото се изтегля и парсва (`json()` също връща Promise). |
| Какво става без `await`? | `data` е Promise, не обект → `data.value` е `undefined`. |
| Защо `encodeURIComponent`? | `&`, `+`, `#`, интервал, кирилица имат специално значение в URL → счупен или различен query. |
| Защо `finally` за бутона? | Връща се и при успех, и при грешка — иначе остава заключен след грешка. |
| Защо `textContent`, а не `innerHTML`? | Данните идват отвън → `innerHTML` = XSS. (Мост към лекцията по сигурност.) |
| Какво се промени в prompt experiment-а? | Без ограничения: пренаписан файл, сменени id-та, добавена библиотека. Изводът: контекст + ограничения + формат = контролируем агент. |

## Какво да следите в агентския код

- Няма `response.ok` → „error handling", който не хваща 404.
- `mode: "no-cors"` — скрива отговора (opaque), студентът мисли, че „работи".
- jQuery/axios от CDN — self-check-ът го хваща; питайте *защо* агентът го е добавил.
- Измислени полета (`data.joke`, `data.text`) — не е проверен реалният JSON.
- `innerHTML` с външни данни. Пренаписан целият файл, изгубени A1–A3.
- Агентът е написал Част A — `AGENTS.md` го забранява; питайте студента как е станало.
- Студентът не може да обясни `finally` / `prepend` / `encodeURIComponent` → връщаме го към агента с „обясни ред X".

## Предложение за Упр. 2–5 (бакалавърска пътека по логиката на магистърския шаблон)

| Упр. | Магистри | Бакалаври (предложение) | Self-check |
|---|---|---|---|
| 2 | SPEC.md + slice 1 с агента | `SPEC.md` (goals / non-goals / acceptance criteria) → агентът строи **frontend CRUD** (Angular или vanilla JS + таблица) по спецификацията; *Spec vs result* | `ex2.mjs` от шаблона — без промяна |
| 3 | Feature през всички слоеве | **Node/Express REST API + SQLite/TypeORM**: миграция, ≥1 тест за правило + ≥1 за endpoint, architecture note, малки commit-и | `ex3.mjs` — без промяна |
| 4 | Deploy + READINESS.md | **Full-stack свързване** + deploy на Render, `/health`, `READINESS.md` (lite: URL, env vars, какво се случва при грешка, gaps) | `ex4.mjs` — Dockerfile по избор |
| 5 | Adversarial review на партньор | **Review на проекта на колега** (`REVIEW.md`, 4 находки, `test(Rn)` → `fix(Rn)`) + консултации | `ex5.mjs` — без промяна |

## Материали

| Къде | Какво |
|---|---|
| `antouanbg/fip-course-template` (template repo) | Студентският шаблон: `README.md`, `AGENTS.md`, `AGENT_LOG.md`, `USAGE.md`, `SPEC.md`, `.devcontainer/`, `.github/workflows/ci.yml`, `checks/ex1.mjs`, `ex1/{README.md, index.html, PROMPTS.md}` |
| `antouanbg/TU-materials/exercises/fip/01-frontend-backend/instructor/` | Този план + референтно решение |
| Drive: `Програмиране на Интернет/Упражнение_1/` | Копие на двете |
