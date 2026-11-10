# svitlo-config

Віддалений конфіг застосунку «єСвітло». Застосунок качає `remote-config.json` з гілки `main`
на кожному оновленні й зберігає останній вдалий. Лише дані, не код.

```json
{
  "off": ["kyiv"],
  "notices": [{ "uk": "Сайт ДТЕК змінився, лагодимо", "en": "DTEK changed its site, fixing it", "regions": ["kyiv"] }]
}
```

- `off`: джерела, яких не питати. Назва регіону — сайт його компанії; `yasno` — YASNO.
- `notices`: рядок на головному екрані й на екрані адреси. Без `regions` (або `[]`) — для всіх; `en` необов'язковий.
- `urls`: нова адреса джерела, якщо воно переїхало. Не вказане лишається вбудованим. Лише `https://`.
  - `dtek.kyiv`, `dtek.kyivOblast`, `dtek.dnipro`, `dtek.odesa`, `dtek.donetsk`: корінь сайту ДТЕК, наприклад
    `https://www.dtek-kem.com.ua/ua/` (сторінка — `shutdowns`, форма — `ajax` під ним).
  - `yasno`: `https://app.yasno.ua/api/blackout-service/public/shutdowns/`.
  - `mykolaiv`, `poltava`, `zhytomyr`, `chernivtsi`, `lviv.power`, `lviv.site`, `lviv.referer`,
    `ternopil.api`, `ternopil.referer`.
- `yasnoRegions`: `{"odesa": {"region": 7, "dso": 701}}` — регіон, який YASNO почав покривати, або нові id.
- `dtekAjaxMethod`: поле `method` форми будинків ДТЕК (зараз `getHomeNum`).
- `fixes`: знайти-замінити в сирій відповіді сайту до того, як застосунок її розбирає. Для дрібних змін на сайті:
  перейменована змінна JS, ключ JSON, клас у HTML, код клітинки.
  ```json
  "fixes": [
    { "source": "dtek", "find": "Schedule2\\.", "replace": "DisconSchedule." },
    { "source": "yasno", "find": "\"type\":\\s*\"Off\"", "replace": "\"type\":\"Definite\"" },
    { "source": "mykolaiv", "in": "request", "find": "addr/filii$", "replace": "address/branches" }
  ]
  ```
  - `source`: назва регіону, `yasno` або `dtek` (усі сайти ДТЕК).
  - `in`: `response` (типово) — відповідь сайту; `request` — адреса й тіло запиту (не для ДТЕК).
  - `find` — regex (`NSRegularExpression`), `replace` — заміна, `$1` — перша група. Правки йдуть по черзі.
  - Шукати треба в тексті, як його віддає сайт: у JSON кирилиця може бути як `А`.
- `dtekPageMarker`: текст, за яким застосунок бачить, що сторінка ДТЕК завантажилась (типово `DisconSchedule.fact`).
  Якщо ДТЕК перейменував змінну — тут нова назва, а в `fixes` правка, що повертає стару.
- Файлу немає — все увімкнено. Зіпсований файл (адреса не `https://`, поганий regex) ігнорується, лишається попередній.
- Зміна доходить за кілька хвилин (кеш GitHub ~5 хв) з наступним оновленням у застосунку.

Регіони: `kyiv`, `kyivOblast`, `dnipro`, `odesa`, `donetsk` (ДТЕК), `mykolaiv`, `poltava`, `zhytomyr`,
`kirovohrad`, `ternopil`, `chernivtsi`, `ivanoFrankivsk`, `lviv`. Невідома назва мовчки пропускається,
тож перевіряйте написання.
