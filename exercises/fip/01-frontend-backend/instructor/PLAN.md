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

## Времева рамка (режим „ти задаваш, те решават" — подробно в FIRST-SESSION.md)

| Мин | Блок | Какво прави асистентът |
|---|---|---|
| 0–15 | Рамка + Задача 0 | 3 изречения за курса и правилото; на екрана — само дъската със задачите. Setup-а правят **сами** по README (без демонстрация); ти обхождаш и записваш проблемите с акаунти/мрежа. |
| 15–20 | Един въпрос към залата | „Кой е frontend, кой backend, къде е базата?" — отговарят студенти, ти не обясняваш. Останалото (fetch, Promise, PROMPTS.md) го четат сами от README. |
| 20–35 | Част A | Сами, без агент. Обхождаш за A4: всеки показва реда в Network. Помагаш с въпрос („F12 → Network → натисни пак"), не с ръка на клавиатурата. |
| 35–75 | Част B | Сами, с агент. Единственото спиране на всички (~50 мин): „Счупете URL-а. Хвана ли 404? Ако не — питайте агента какво липсва." Повечето агентски решения нямат `response.ok`. |
| 75–85 | Разбор | 3–4 студента на екрана със **своя** код; въпросите по-долу. Ако един не знае — питаш следващия, не обясняваш ти. |
| 85–90 | Предаване | `node checks/ex1.mjs` → `git tag ex1 && git push origin ex1` → Stop codespace → Sign out (общи лаптопи). Проверка на 2–3 tag-а на живо. |

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
