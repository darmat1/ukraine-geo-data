# Ukraine Administrative GeoJSON Database

Hierarchical GeoJSON files for Ukraine administrative divisions (Oblasts, Raions, Hromadas, Settlements). Designed for lazy loading in web mapping applications.

---

# База даних адміністративних кордонів України (GeoJSON)

Ієрархічні GeoJSON файли для адміністративно-територіального устрою України (Області, Райони, Громади, Населені пункти). Призначено для лінивого завантаження даних у веб-додатках.

---

## CDN Access / Доступ через CDN

For maximum speed and reliability, use **jsDelivr CDN**.

Для максимальної швидкості та надійності використовуйте **jsDelivr CDN**.

**Base URL / Базова адреса:**

```
https://cdn.jsdelivr.net/gh/darmat1/ukraine-geo-data@main/geodata/
```

**Example requests / Приклади запитів:**

```
https://cdn.jsdelivr.net/gh/darmat1/ukraine-geo-data@main/geodata/Ukraine.geojson
https://cdn.jsdelivr.net/gh/darmat1/ukraine-geo-data@main/geodata/odeska_oblast.geojson
https://cdn.jsdelivr.net/gh/darmat1/ukraine-geo-data@main/geodata/odeska_oblast.odeskyy_rayon.geojson
```

---

## File Structure / Структура файлів

All files are located in the repository root. The naming uses **dot-notation** to represent the hierarchy.

Усі файли знаходяться в корені репозиторію. Іменування використовує **крапкову нотацію** для відображення ієрархії.

```
Ukraine.geojson                                          # Country / Країна (all oblasts / усі області)
{oblast_slug}.geojson                                    # Oblast / Область (raions / райони)
{oblast_slug}.{raion_slug}.geojson                       # Raion / Район (hromadas / громади)
{oblast_slug}.{raion_slug}.{hromada_slug}.geojson        # Hromada / Громада (settlements / населені пункти)
```

### Hierarchy Examples / Приклади ієрархії

| Admin Level | Ukrainian Name / Українська назва | Filename / Назва файлу                                     |
| :---------- | :-------------------------------- | :--------------------------------------------------------- |
| Country     | Україна                           | `Ukraine.geojson`                                          |
| Oblast      | Одеська область                   | `odeska_oblast.geojson`                                    |
| Raion       | Одеський район                    | `odeska_oblast.odeskyy_rayon.geojson`                      |
| Hromada     | Одеська міська громада            | `odeska_oblast.odeskyy_rayon.odeska_miska_hromada.geojson` |

---

## Naming Convention / Правила іменування

Files are named based on Ukrainian names transliterated to Latin characters (slug format). The original Ukrainian names are stored in the `name` property of each GeoJSON feature. Since filenames cannot contain Cyrillic characters, transliteration is required to build the correct file path from feature properties.

Файли названі на основі українських назв, транслітерованих латиницею (формат slug). Оригінальні українські назви зберігаються у властивості `name` кожного GeoJSON об'єкту. Оскільки імена файлів не можуть містити кириличні символи, для побудови правильного шляху до файлу з властивостей об'єкту потрібна транслітерація.

**Transliteration rules / Правила транслітерації:**

| Ukr | Lat | Ukr | Lat | Ukr | Lat    |
| :-- | :-- | :-- | :-- | :-- | :----- |
| а   | a   | к   | k   | х   | kh     |
| б   | b   | л   | l   | ц   | ts     |
| в   | v   | м   | m   | ч   | ch     |
| г   | h   | н   | n   | ш   | sh     |
| ґ   | g   | о   | o   | щ   | shch   |
| д   | d   | п   | p   | ь   | (skip) |
| е   | e   | р   | r   | ю   | yu     |
| є   | ye  | с   | s   | я   | ya     |
| ж   | zh  | т   | t   | ї   | yi     |
| з   | z   | у   | u   | і   | i      |
| и   | y   | ф   | f   | '   | (skip) |

---

## Implementation Examples / Приклади реалізації

### JavaScript / TypeScript

```javascript
const CDN_BASE =
  "https://cdn.jsdelivr.net/gh/darmat1/ukraine-geo-data@main/geodata";

const toSlug = (text) => {
  if (!text) return "unknown";
  return text
    .toLowerCase()
    .replace(/є/g, "ye")
    .replace(/ї/g, "yi")
    .replace(/і/g, "i")
    .replace(/ґ/g, "g")
    .replace(/ю/g, "yu")
    .replace(/я/g, "ya")
    .replace(/щ/g, "shch")
    .replace(/ч/g, "ch")
    .replace(/ш/g, "sh")
    .replace(/х/g, "kh")
    .replace(/ц/g, "ts")
    .replace(/ж/g, "zh")
    .replace(/ь/g, "")
    .replace(/'/g, "")
    .replace(/ʼ/g, "")
    .replace(/а/g, "a")
    .replace(/б/g, "b")
    .replace(/в/g, "v")
    .replace(/г/g, "h")
    .replace(/д/g, "d")
    .replace(/е/g, "e")
    .replace(/з/g, "z")
    .replace(/и/g, "y")
    .replace(/й/g, "y")
    .replace(/к/g, "k")
    .replace(/л/g, "l")
    .replace(/м/g, "m")
    .replace(/н/g, "n")
    .replace(/о/g, "o")
    .replace(/п/g, "p")
    .replace(/р/g, "r")
    .replace(/с/g, "s")
    .replace(/т/g, "t")
    .replace(/у/g, "u")
    .replace(/ф/g, "f")
    .replace(/ы/g, "y")
    .replace(/э/g, "e")
    .replace(/ё/g, "yo")
    .replace(/[^a-z0-9]/g, "_")
    .replace(/_+/g, "_")
    .replace(/^_|_$/g, "");
};

const buildFilename = (pathSegments) => {
  if (pathSegments.length === 0) return "Ukraine";
  return pathSegments.join(".");
};

const loadGeoJSON = async (pathSegments) => {
  const filename = buildFilename(pathSegments);
  const url = `${CDN_BASE}/${filename}.geojson`;
  const response = await fetch(url);
  if (!response.ok) throw new Error(`Failed to load: ${url}`);
  return response.json();
};

// Usage with slugs / Використання зі слагами:
// loadGeoJSON([])                                              // Ukraine
// loadGeoJSON(["odeska_oblast"])                               // Raions of Odesa oblast
// loadGeoJSON(["odeska_oblast", "odeskyy_rayon"])              // Hromadas
// loadGeoJSON(["odeska_oblast", "odeskyy_rayon", "..."])       // Settlements

// Usage with Ukrainian names / Використання з українськими назвами:
// When you have a feature's `name` property from GeoJSON, use toSlug() to build the path:
// Коли ви маєте властивість `name` об'єкту GeoJSON, використовуйте toSlug() для побудови шляху:

const oblastName = "Одеська область"; // from feature.properties.name
const raionName = "Одеський район"; // from child feature.properties.name

console.log(toSlug(oblastName)); // "odeska_oblast"
console.log(toSlug(raionName)); // "odeskyy_rayon"

// Load raions for selected oblast / Завантажити райони вибраної області:
loadGeoJSON([toSlug(oblastName)]);

// Load hromadas for selected raion / Завантажити громади вибраного району:
loadGeoJSON([toSlug(oblastName), toSlug(raionName)]);
```

---

### Python

```python
import re
import requests

CDN_BASE = "https://cdn.jsdelivr.net/gh/darmat1/ukraine-geo-data@main/geodata"

TRANS_MAP = {
    'а': 'a', 'б': 'b', 'в': 'v', 'г': 'h', 'ґ': 'g', 'д': 'd', 'е': 'e', 'є': 'ye',
    'ж': 'zh', 'з': 'z', 'и': 'y', 'і': 'i', 'ї': 'yi', 'й': 'y', 'к': 'k', 'л': 'l',
    'м': 'm', 'н': 'n', 'о': 'o', 'п': 'p', 'р': 'r', 'с': 's', 'т': 't', 'у': 'u',
    'ф': 'f', 'х': 'kh', 'ц': 'ts', 'ч': 'ch', 'ш': 'sh', 'щ': 'shch', 'ь': '',
    'ю': 'yu', 'я': 'ya', 'ы': 'y', 'э': 'e', 'ё': 'yo', "'": '', 'ʼ': ''
}

def to_slug(text: str) -> str:
    if not text:
        return "unknown"
    text = text.lower()
    slug = "".join([TRANS_MAP.get(c, c) for c in text])
    slug = re.sub(r'[^a-z0-9]', '_', slug)
    slug = re.sub(r'_+', '_', slug)
    return slug.strip('_')

def build_filename(path_segments: list) -> str:
    if not path_segments:
        return "Ukraine"
    return ".".join(path_segments)

def load_geojson(path_segments: list) -> dict:
    filename = build_filename(path_segments)
    url = f"{CDN_BASE}/{filename}.geojson"
    response = requests.get(url)
    response.raise_for_status()
    return response.json()
```

---

### PHP

```php
<?php

const CDN_BASE = "https://cdn.jsdelivr.net/gh/darmat1/ukraine-geo-data@main/geodata";

function toSlug(string $text): string
{
    $map = [
        'а' => 'a', 'б' => 'b', 'в' => 'v', 'г' => 'h', 'ґ' => 'g', 'д' => 'd',
        'е' => 'e', 'є' => 'ye', 'ж' => 'zh', 'з' => 'z', 'и' => 'y', 'і' => 'i',
        'ї' => 'yi', 'й' => 'y', 'к' => 'k', 'л' => 'l', 'м' => 'm', 'н' => 'n',
        'о' => 'o', 'п' => 'p', 'р' => 'r', 'с' => 's', 'т' => 't', 'у' => 'u',
        'ф' => 'f', 'х' => 'kh', 'ц' => 'ts', 'ч' => 'ch', 'ш' => 'sh',
        'щ' => 'shch', 'ь' => '', 'ю' => 'yu', 'я' => 'ya',
        'ы' => 'y', 'э' => 'e', 'ё' => 'yo', "'" => '', 'ʼ' => ''
    ];

    $text = mb_strtolower($text, 'UTF-8');
    $text = strtr($text, $map);
    $slug = preg_replace('/[^a-z0-9]+/', '_', $text);
    return trim($slug, '_');
}

function loadGeoJSON(array $pathSegments): array
{
    $filename = empty($pathSegments) ? "Ukraine" : implode(".", $pathSegments);
    $url = CDN_BASE . "/{$filename}.geojson";
    return json_decode(file_get_contents($url), true);
}
```

---

### Swift (iOS/macOS)

```swift
import Foundation

let CDN_BASE = "https://cdn.jsdelivr.net/gh/darmat1/ukraine-geo-data@main/geodata"

let transMap: [Character: String] = [
    "а": "a", "б": "b", "в": "v", "г": "h", "ґ": "g", "д": "d", "е": "e", "є": "ye",
    "ж": "zh", "з": "z", "и": "y", "і": "i", "ї": "yi", "й": "y", "к": "k", "л": "l",
    "м": "m", "н": "n", "о": "o", "п": "p", "р": "r", "с": "s", "т": "t", "у": "u",
    "ф": "f", "х": "kh", "ц": "ts", "ч": "ch", "ш": "sh", "щ": "shch", "ь": "",
    "ю": "yu", "я": "ya", "ї": "yi", "і": "i", "'": "", "ʼ": ""
]

func toSlug(_ text: String) -> String {
    guard !text.isEmpty else { return "unknown" }
    var result = ""
    for char in text.lowercased() {
        result += transMap[char] ?? String(char)
    }
    let slug = result.replacingOccurrences(of: "[^a-z0-9]+", with: "_", options: .regularExpression)
    return slug.trimmingCharacters(in: CharacterSet(charactersIn: "_"))
}

func loadGeoJSON(_ pathSegments: [String]) async throws -> [String: Any] {
    let filename = pathSegments.isEmpty ? "Ukraine" : pathSegments.joined(separator: ".")
    guard let url = URL(string: "\(CDN_BASE)/\(filename).geojson") else { throw URLError(.badURL) }
    let (data, _) = try await URLSession.shared.data(from: url)
    return try JSONSerialization.jsonObject(with: data) as! [String: Any]
}
```

---

### Kotlin (Android)

```kotlin
import java.net.URL

const val CDN_BASE = "https://cdn.jsdelivr.net/gh/darmat1/ukraine-geo-data@main/geodata"

val transMap = mapOf(
    'а' to "a", 'б' to "b", 'в' to "v", 'г' to "h", 'ґ' to "g", 'д' to "d",
    'е' to "e", 'є' to "ye", 'ж' to "zh", 'з' to "z", 'и' to "y", 'і' to "i",
    'ї' to "yi", 'й' to "y", 'к' to "k", 'л' to "l", 'м' to "m", 'н' to "n",
    'о' to "o", 'п' to "p", 'р' to "r", 'с' to "s", 'т' to "t", 'у' to "u",
    'ф' to "f", 'х' to "kh", 'ц' to "ts", 'ч' to "ch", 'ш' to "sh", 'щ' to "shch",
    'ь' to "", 'ю' to "yu", 'я' to "ya", '\'' to "", 'ʼ' to ""
)

fun toSlug(text: String): String {
    if (text.isEmpty()) return "unknown"
    val result = text.lowercase().map { transMap[it] ?: it.toString() }.joinToString("")
    return result.replace(Regex("[^a-z0-9]+"), "_").trim('_')
}

fun loadGeoJSON(pathSegments: List<String>): String {
    val filename = if (pathSegments.isEmpty()) "Ukraine" else pathSegments.joinToString(".")
    return URL("$CDN_BASE/$filename.geojson").readText()
}
```

---

## Drill-Down Navigation / Навігація вглиб

**English:**

1. Load `Ukraine.geojson` for initial country view (all oblasts)
2. On oblast click, build path: `{oblast_slug}.geojson` to load raions
3. On raion click, build path: `{oblast_slug}.{raion_slug}.geojson` to load hromadas
4. On hromada click, build path: `{oblast_slug}.{raion_slug}.{hromada_slug}.geojson` to load settlements

**Ukrainian:**

1. Завантажте `Ukraine.geojson` для початкового перегляду країни (всі області)
2. При кліку на область, побудуйте шлях: `{oblast_slug}.geojson` для завантаження районів
3. При кліку на район, побудуйте шлях: `{oblast_slug}.{raion_slug}.geojson` для завантаження громад
4. При кліку на громаду, побудуйте шлях: `{oblast_slug}.{raion_slug}.{hromada_slug}.geojson` для завантаження населених пунктів

---

## Contributing / Як долучитися

**English:**

We welcome contributions! You can help improve this database by:

- Adding missing settlements or city districts
- Correcting existing boundary data
- Improving transliteration accuracy

To contribute:

1. Fork the repository
2. Make your changes following the naming convention
3. Submit a Pull Request with a clear description

**Ukrainian:**

Ми вітаємо внески! Ви можете допомогти покращити цю базу даних:

- Додавши відсутні населені пункти або райони міст
- Виправивши існуючі дані кордонів
- Покращивши точність транслітерації

Щоб зробити внесок:

1. Зробіть форк репозиторію
2. Внесіть зміни, дотримуючись правил іменування
3. Надішліть Pull Request з чітким описом

---

## Data Sources / Джерела даних

- OpenStreetMap administrative boundaries
- Admin levels: 4 (Oblast), 6 (Raion), 7 (Hromada), 9-10 (Settlements/City Districts)

---

## License / Ліцензія

Data is provided under ODbL (OpenStreetMap license).

Дані надаються за ліцензією ODbL (ліцензія OpenStreetMap).

Repository / Репозиторій: [github.com/darmat1/ukraine-geo-data](https://github.com/darmat1/ukraine-geo-data)
