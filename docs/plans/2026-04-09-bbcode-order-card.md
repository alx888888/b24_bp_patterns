# BBCode Order Card Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Подготовить шаблон BBCode для карточки заказа в Bitrix24 Messenger с двухколоночной компоновкой и списком работ.

**Architecture:** Шаблон строится на одном заголовке, одной таблице `[table]` с двумя ячейками и отдельном текстовом блоке `Работы:`. Переменные Bitrix24 сохраняют исходные коды без переименования.

**Tech Stack:** Bitrix24 Business Process templates, BBCode, переменные документа и активити

---

### Task 1: Собрать шаблон сообщения

**Files:**
- Create: `docs/plans/2026-04-09-bbcode-order-card-design.md`
- Create: `docs/plans/2026-04-09-bbcode-order-card.md`

**Step 1: Подготовить структуру сообщения**

Определить три блока:

- шапка;
- таблица 2 колонки;
- список работ.

**Step 2: Сохранить исходные переменные**

Оставить без изменений:

- `{{Сделка}}`
- `{{_Номер выезда}}`
- `{{Плановая дата выезда}}`
- `{{Диапазон времени (текст)}}`
- `{=A43494_16201_78654_83104:SOURCE_ID > printable}`
- `{{Адрес выезда}}`
- `{{Домофон / Код}}`
- `{{Подъезд и этаж}}`
- `{{Контакты клиента}}`
- `{{Комментарий для мастера}}`
- `{=A84142_96083_1306_13335:rest_result_1}`
- `{=A84142_96083_1306_13335:rest_result_2}`
- `{=A84142_96083_1306_13335:rest_result_3}`
- `{=A84142_96083_1306_13335:rest_result_4}`
- `{{_ID задания по выезду}}`

**Step 3: Собрать BBCode**

Подготовить финальный шаблон с `[SIZE]`, `[B]`, `[table]`, `[tr]`, `[td]`, `[URL]`.

**Step 4: Проверить визуальную читаемость**

Проверить:

- шапка читается с первого экрана;
- подписи не слипаются со значениями;
- ссылка завершения заметна;
- список работ отделен пустой строкой.

**Step 5: Commit**

```bash
git add docs/plans/2026-04-09-bbcode-order-card-design.md docs/plans/2026-04-09-bbcode-order-card.md
git commit -m "Add BBCode order card design docs"
```
