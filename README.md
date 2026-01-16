# Ukraine Administrative GeoJSON Database

A repository containing structured, hierarchical GeoJSON files for the administrative divisions of Ukraine (Oblasts, Raions, Hromadas, City Districts). Designed for **Lazy Loading** in web applications.

## База даних адміністративних кордонів України (GeoJSON)

Репозиторій містить структуровані, ієрархічні файли GeoJSON для адміністративно-територіального устрою України (Області, Райони, Громади, Райони міст). Призначений для "лінивого" завантаження даних (Lazy Loading) у веб-додатках.

---

## Структура даних / Data Structure

Файли організовані відповідно до адміністративних рівнів (Admin Level):

| Папка / Folder        | Admin Level | Опис / Description                                                                            |
| :-------------------- | :---------- | :-------------------------------------------------------------------------------------------- |
| **`Ukraine.geojson`** | 4           | Кордони всієї країни та областей. / Country and Oblast boundaries.                            |
| **`/raions`**         | 6           | Кордони районів всередині областей. / Raion boundaries within Oblasts.                        |
| **`/hromadas`**       | 7           | Кордони територіальних громад. / Hromada boundaries.                                          |
| **`/settlements`**    | 9/10        | Кордони населених пунктів та районів великих міст. / Settlement and City District boundaries. |

---

## Логіка іменування / Naming Convention

Файли названі на основі українських назв об'єктів, перетворених у **slug** (транслітерація на англійську + snake_case).

**Приклад (Example):**

- `Одеська область` -> `odeska.geojson`
- `Одеський район` -> `odeskyi_raion.geojson`
- `Одеська міська громада` -> `odeska_miska_hromada.geojson`

### Алгоритми транслітерації / Transliteration Algorithms

Для забезпечення коректного пошуку файлів у різних мовах програмування використовуйте наступні імплементації.

_(To ensure correct file lookup in different programming languages, use the following implementations.)_

#### JavaScript / TypeScript

```javascript
const toSlug = (text) => {
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
    .replace(/[^a-z0-9]/g, "_");
};
```

#### Python

```python
import re

TRANS_MAP = {
    'а': 'a', 'б': 'b', 'в': 'v', 'г': 'h', 'ґ': 'g', 'д': 'd', 'е': 'e', 'є': 'ye', 'ж': 'zh',
    'з': 'z', 'и': 'y', 'і': 'i', 'ї': 'yi', 'й': 'y', 'к': 'k', 'л': 'l', 'м': 'm', 'н': 'n',
    'о': 'o', 'п': 'p', 'р': 'r', 'с': 's', 'т': 't', 'у': 'u', 'ф': 'f', 'х': 'kh', 'ц': 'ts',
    'ч': 'ch', 'ш': 'sh', 'щ': 'shch', 'ь': '', 'ю': 'yu', 'я': 'ya', 'ы': 'y', 'э': 'e', 'ё': 'yo'
}

def to_slug(text):
    text = text.lower()
    slug = "".join([TRANS_MAP.get(c, c) for c in text])
    slug = re.sub(r'[^a-zA-Z0-9]', '_', slug)
    return slug.lower()

# Example: to_slug("Одеський район") -> "odeskyi_raion"
```

#### PHP

```php
<?php

function to_slug(string $text): string
{
    $map = [
        'а' => 'a', 'б' => 'b', 'в' => 'v', 'г' => 'h', 'ґ' => 'g', 'д' => 'd', 'е' => 'e', 'є' => 'ye',
        'ж' => 'zh', 'з' => 'z', 'и' => 'y', 'і' => 'i', 'ї' => 'yi', 'й' => 'y', 'к' => 'k', 'л' => 'l',
        'м' => 'm', 'н' => 'n', 'о' => 'o', 'п' => 'p', 'р' => 'r', 'с' => 's', 'т' => 't', 'у' => 'u',
        'ф' => 'f', 'х' => 'kh', 'ц' => 'ts', 'ч' => 'ch', 'ш' => 'sh', 'щ' => 'shch', 'ь' => '',
        'ю' => 'yu', 'я' => 'ya', 'ы' => 'y', 'э' => 'e', 'ё' => 'yo',
    ];

    $text = mb_strtolower($text, 'UTF-8');
    $text = strtr($text, $map);

    // Remove non-alphanumeric and replace with underscore
    $slug = preg_replace('/[^a-zA-Z0-9]+/', '_', $text);

    // Ensure all characters are lower-case (should be already, but safe check)
    return strtolower($slug);
}
```

#### Go (Golang)

```go
package main

import (
	"strings"
	"regexp"
)

var transMap = map[rune]string{
    'а': "a", 'б': "b", 'в': "v", 'г': "h", 'ґ': "g", 'д': "d", 'е': "e", 'є': "ye",
    'ж': "zh", 'з": "z", 'и': "y", 'і': "i", 'ї': "yi", 'й': "y", 'к': "k", 'л': "l",
    'м': "m", 'н': "n", 'о': "o", 'п': "p", 'р': "r", 'с': "s", 'т': "t", 'у': "u",
    'ф': "f", 'х': "kh", 'ц': "ts", 'ч': "ch", 'ш': "sh", 'щ': "shch", 'ь': "",
    'ю': "yu", 'я': "ya", 'ы': "y", 'э': "e", 'ё': "yo",
}

var nonAlphanumeric = regexp.MustCompile("[^a-zA-Z0-9]+")

func toSlug(text string) string {
    var slugBuilder strings.Builder

    for _, r := range text {
        rLower := strings.ToLower(string(r))
        if val, ok := transMap[r]; ok {
            slugBuilder.WriteString(val)
        } else if val, ok := transMap[[]rune(rLower)[0]]; ok {
            // Check for uppercase letters
            slugBuilder.WriteString(val)
        } else {
            slugBuilder.WriteRune(r)
        }
    }

    slug := nonAlphanumeric.ReplaceAllString(slugBuilder.String(), "_")
    return strings.ToLower(slug)
}
// Note: Golang implementation requires careful handling of Unicode.
// A simpler, but less strictly correct, approach is often preferred for simple slugs.
```

## Як використовувати (CDN) / How to Use

Для максимальної швидкості та надійності використовуйте **jsDelivr CDN**.

**Базовий URL (Base URL):**

```
https://cdn.jsdelivr.net/gh/darmat1/ukraine-geo-data@main/
```

**Приклад запиту (Example Fetch):**

```
https://cdn.jsdelivr.net/gh/darmat1/ukraine-geo-data@main/Ukraine.geojson
https://cdn.jsdelivr.net/gh/darmat1/ukraine-geo-data@main/raions/odeska.geojson
```
