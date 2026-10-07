# PVA4 — PHP 06: Příkazy include a require

Obsahem repozitáře je cvičení k přednášce *Příkazy include a require*.
Navazuje na `PHP_02_VystupHtml` — pokračujete ve fiktivní aplikaci
**Maturita**, portálu pro maturitní témata a konzultace.

Na začátku máte dvě stránky z minula: `index.php` (Přehled) a `aboutme.php`
(Profil). Otevřete je vedle sebe — zhruba polovina kódu je v obou souborech
**stejná**: celý `<head>`, navigace, patička, a dokonce i proměnná
`$authorName` a konstanta `APP_VERSION`. Když chcete změnit jméno nebo
přidat odkaz do menu, musíte upravit oba soubory. S každou další stránkou
je to horší.

Vaším úkolem je aplikaci **rozdělit do více souborů** tak, aby se každá
opakující se část psala jen jednou a stránky si ji vkládaly pomocí
`include` a `require`.

## Cílová struktura projektu

```
/includes
  config.php        ← ÚKOL 1: nastavení aplikace (vrací pole)
/templates
  header.php        ← ÚKOL 2: <head>, začátek <body>, hlavička stránky
  menu.php          ← ÚKOL 3: odkazy navigace
  footer.php        ← ÚKOL 4: patička a konec stránky
  metric-card.php   ← ÚKOL 6: jedna metriková karta
index.php           ← Přehled
aboutme.php         ← Profil
contact.php         ← ÚKOL 7: nová stránka Kontakt
odpovedi.md         ← odpovědi na otázky z úkolů
```

## Jak postupovat

Zadání úkolů je níže v tomto README. Ve výchozích souborech najdete
**značky** `ZAČÁTEK ČÁSTI PRO …` a `KONEC ČÁSTI PRO …`, které ukazují,
kterou část kódu v kterém úkolu vyjímáte do nového souboru. Značky
přesouvejte spolu s kódem nebo je smažte, na výsledek nemají vliv.

Po **každém** úkolu si obě stránky otevřete v prohlížeči. Musí vypadat
**stejně jako na začátku** — měníte strukturu kódu, ne vzhled. Jedinou
viditelnou změnou je nová verze a e-mail v patičce, menu a stránka Kontakt.

| Úkol | Soubor | Co děláte |
| --- | --- | --- |
| 1 | `includes/config.php` | Nastavení aplikace vrácené přes `return`, načtení přes `require` |
| 2 | `templates/header.php` | Společný začátek stránky, titulek z proměnné `$pageTitle` |
| 3 | `templates/menu.php` | Navigace vložená z `header.php` přes `__DIR__`, zvýraznění aktivní stránky |
| 4 | `templates/footer.php` | Společný konec stránky, údaje z konfigurace |
| 5 | `index.php`, `aboutme.php` | Úklid — stránka obsahuje jen vlastní obsah |
| 6 | `templates/metric-card.php` | Jedna šablona karty vložená dvakrát s jinými hodnotami **[+ odpověď]** |
| 7 | `contact.php`, `menu.php` | Nová stránka a odkaz v menu úpravou jediného souboru **[+ odpověď]** |
| 8 | `templates/header.php` | Proč `__DIR__` — relativní cesta ve vnořené složce **[+ odpověď]** |
| 9 | — | **Bonus:** `include` vs. `require` při chybějícím souboru **[+ odpověď]** |

Úkoly označené **[+ odpověď]** chtějí kromě kódu i krátkou odpověď do
souboru `odpovedi.md` pod nadpis daného úkolu.

Podmínky, cykly ani vlastní funkce zatím neznáte a nepotřebujete je —
jedinou výjimkou je hotový zápis v ÚKOLU 3, který dostanete celý.

## Zadání

### ÚKOL 1 · `includes/config.php`

Vytvořte složku `includes` a v ní soubor `config.php`, který příkazem
`return` vrátí asociativní pole s nastavením aplikace:

| Klíč | Hodnota |
| --- | --- |
| `appName` | `Maturita` |
| `version` | `1.1` |
| `author` | vaše jméno |
| `email` | `maturita@oa-opava.cz` |
| `year` | `2026` (číslo, bez uvozovek) |

Soubor obsahuje **jen PHP kód** — proto ho **neukončujte** značkou `?>`.

Pak v `index.php` i `aboutme.php`:

- na začátek PHP bloku přidejte `$config = require __DIR__ . '/includes/config.php';`
- smažte deklaraci `$authorName` a konstantu `APP_VERSION`
- každé `echo $authorName` nahraďte `echo $config['author']` a `APP_VERSION`
  nahraďte `$config['version']`

Proč `require`, a ne `include`? Bez konfigurace stránka nedává smysl,
tak ať se raději zastaví.

**Očekávaný výstup:** stránky vypadají jako na začátku, jen v patičce je
`Maturita v1.1`. Jméno změníte na **jediném** místě.

### ÚKOL 2 · `templates/header.php`

Vytvořte složku `templates` a v ní `header.php`. Přesuňte do něj z
`index.php` všechno mezi značkami `ZAČÁTEK/KONEC ČÁSTI PRO
templates/header.php` — od `<!doctype html>` až po otevírací
`<div class="mx-auto max-w-3xl">`. Ze stejné části v `aboutme.php` už
kód jen smažete.

Hlavička se ale na každé stránce liší titulkem (`Přehled` / `Profil`).
Vyřešte to proměnnou:

- v `header.php` nahraďte obsah `<title>` výpisem
  `$pageTitle | $config['appName']` a text `Maturita` u ikony 📘 výpisem
  `$config['appName']`
- v každé stránce nastavte `$pageTitle` (`'Přehled'`, `'Profil'`) a
  **až pak** hlavičku vložte:

```php
$pageTitle = 'Přehled';
include __DIR__ . '/templates/header.php';
```

Šablona obsahuje HTML, takže v ní `?>` normálně používáte. Pro šablony
vzhledu se používá `include` — bez menu nebo patičky se stránka ještě
dá zobrazit.

**Očekávaný výstup (zdrojový kód stránky, `Ctrl+U`):**
`<title>Přehled | Maturita</title>` na `index.php`,
`<title>Profil | Maturita</title>` na `aboutme.php`.

### ÚKOL 3 · `templates/menu.php`

Z `header.php` vyjměte odkazy navigace (značky `ČÁSTI PRO
templates/menu.php`) do souboru `templates/menu.php` a v `header.php` je
na stejném místě vložte:

```php
<?php include __DIR__ . '/menu.php'; ?>
```

`__DIR__` je složka **aktuálního souboru** — tady `templates`, takže
`menu.php` se najde vedle `header.php`.

Teď je menu jen jedno, ale každá stránka zvýrazňovala svůj odkaz jinak
(aktivní odkaz má třídu `text-brand-600`, ostatní
`text-gray-500 hover:text-brand-600`). Každá stránka si proto před
vložením hlavičky nastaví, která je aktivní:

```php
$activePage = 'index';   // v aboutme.php 'aboutme'
```

V `menu.php` pak třídu odkazu vypíšete tímto zápisem — je to tzv. ternární
operátor `podmínka ? 'když ano' : 'když ne'`, podmínky podrobně probereme
později:

```php
<a href="index.php" class="<?php echo $activePage === 'index' ? 'text-brand-600' : 'text-gray-500 hover:text-brand-600'; ?>">Přehled</a>
```

Stejně upravte odkaz na Profil. Text odkazu je `Profil - ` a jméno
z `$config['author']`.

**Očekávaný výstup (zdrojový kód):** na `index.php` má odkaz Přehled třídu
`text-brand-600` a Profil `text-gray-500 hover:text-brand-600`, na
`aboutme.php` obráceně.

### ÚKOL 4 · `templates/footer.php`

Z `index.php` přesuňte konec stránky (značky `ČÁSTI PRO
templates/footer.php`) do `templates/footer.php`, v `aboutme.php` ho
smažte. Obě stránky ho na konci vloží přes `include` a `__DIR__`.

Šablony `header.php` a `footer.php` patří k sobě: co hlavička otevře
(`<html>`, `<body>`, `<main>`, `<div>`), to patička zavře.

Text patičky přepište tak, aby všechny údaje šly z konfigurace:

```
© rok appName vversion · email
```

**Očekávaný výstup:** `© 2026 Maturita v1.1 · maturita@oa-opava.cz`
(znak © zapište jako `&copy;`).

### ÚKOL 5 · `index.php` a `aboutme.php` — úklid

Zkontrolujte, že obě stránky teď začínají stejně a obsahují už **jen svůj
vlastní obsah**:

```php
<?php
$config = require __DIR__ . '/includes/config.php';

$pageTitle = 'Přehled';
$activePage = 'index';

// proměnné jen pro tuto stránku ($availableTopics, $topics, ...)

include __DIR__ . '/templates/header.php';
?>

<!-- obsah stránky -->

<?php include __DIR__ . '/templates/footer.php'; ?>
```

Pozor na pořadí: proměnné, které šablona používá, musí být nastavené
**před** `include`, jinak PHP hlásí `Warning: Undefined variable`.

**Očekávaný výstup:** obě stránky vypadají jako na začátku a v souborech
nezůstal žádný `<!doctype>`, `<head>`, `<nav>` ani `<footer>`.

### ÚKOL 6 · `templates/metric-card.php` — šablona vložená vícekrát

Na `index.php` jsou dvě metrikové karty, které se liší jen popiskem
a číslem. Vytvořte `templates/metric-card.php` s HTML **jedné** karty, ve
které místo popisku vypíšete `$cardLabel` a místo čísla `$cardValue`.

V `index.php` obě karty nahraďte takto (obal `<div class="grid …">`
zůstává v `index.php`):

```php
<?php
$cardLabel = 'Dostupná témata';
$cardValue = $availableTopics;
include __DIR__ . '/templates/metric-card.php';

$cardLabel = 'Moje konzultace';
$cardValue = $myConsultations;
include __DIR__ . '/templates/metric-card.php';
?>
```

**Očekávaný výstup:** dvě karty s popisky a čísly jako na začátku.

**Do `odpovedi.md`:** Co se stane, když druhé vložení karty přepíšete na
`include_once`? Proč? (Vyzkoušejte, pak vraťte zpět.)

### ÚKOL 7 · `contact.php` — nová stránka

Teď sklidíte, co jste zaseli. Vytvořte stránku `contact.php` s titulkem
`Kontakt` (`$activePage = 'contact'`). Jejím obsahem bude karta se
stejnými třídami, jako má uvítací karta na Přehledu, a v ní:

- nadpis `<h1>` s textem `Kontakt`
- odstavec `Dotazy k maturitním pracím pište na <e-mail>`, kde e-mail je
  odkaz `mailto:` s adresou z `$config['email']`

Do `templates/menu.php` přidejte třetí odkaz `Kontakt` se zvýrazněním
aktivní stránky jako v ÚKOLU 3.

**Očekávaný výstup:** odkaz Kontakt je v menu na **všech třech** stránkách,
na `contact.php` je zvýrazněný, ve zdrojovém kódu je
`<a href="mailto:maturita@oa-opava.cz"`.

**Do `odpovedi.md`:** Kolik souborů jste upravili, aby se odkaz objevil na
všech stránkách? Kolik by to bylo na začátku cvičení?

### ÚKOL 8 · Proč `__DIR__` [+ odpověď]

V `header.php` dočasně přepište vložení menu na
`include './menu.php';` a otevřete `index.php`.

**Do `odpovedi.md`:** Co se stalo a proč? Od které složky PHP relativní
cestu `./menu.php` počítá — od `index.php`, nebo od `header.php`? Proč
s `__DIR__` problém nenastane?

Pak vraťte zápis s `__DIR__`.

### ÚKOL 9 · Bonus: `include` vs. `require` [+ odpověď]

1. Přejmenujte `templates/menu.php` na `menu-old.php` a otevřete
   `index.php`. Pak ho přejmenujte zpět.
2. Přejmenujte `includes/config.php` na `config-old.php` a otevřete
   `index.php`. Pak ho přejmenujte zpět.

**Do `odpovedi.md`:** Jaká chyba se v každém případě objevila (Warning,
nebo Fatal error)? Vykreslila se zbytek stránky? Vysvětlete rozdíl
a proč jsme pro konfiguraci zvolili `require` a pro menu `include`.

## Vzhled stránky

Styly táhne Tailwind CSS přes Play CDN stejně jako v `PHP_02_VystupHtml`,
nic nebuildujete ani neinstalujete. Třídy (`text-brand-600`, `rounded-2xl`,
…) přesouvejte beze změny.

## Jak spustit stránku

**Přes FlyEnv:** nastavte tuto složku jako document root webu a otevřete
`http://localhost/index.php` (port si ověřte v nastavení FlyEnv).

**Bez nastavování** — vestavěný server PHP, ve složce s cvičením spusťte:

```sh
php -S localhost:8000
```

…a otevřete `http://localhost:8000/index.php`.

> **Tip:** titulek, třídy odkazů v menu a meta tagy kontrolujte ve
> **zdrojovém kódu stránky** (`Ctrl+U`). Pokud se něco nevložilo, PHP
> vypíše `Warning` nebo `Fatal error` přímo na stránku — přečtěte si, který
> soubor a který řádek hlásí.

## Konvence, které se od vás čekají

Stejné jako v předchozích cvičeních:

- **Názvy proměnných a klíčů** anglicky, `camelCase`, bez diakritiky —
  `$pageTitle`, `$config['appName']`.
- **Soubor, který obsahuje jen PHP kód** (`includes/config.php`),
  **neukončujte** značkou `?>`. Šablony s HTML ji používají normálně.
- **Cesty** pište vždy přes `__DIR__` a s lomítkem `/`.
- **`require`** pro soubory, bez kterých stránka nedává smysl
  (konfigurace), **`include`** pro šablony vzhledu.

## Jak poznáte, že to máte správně

- Obě původní stránky vypadají jako na začátku, `contact.php` je nová.
- Na žádné stránce se neobjeví `Warning` ani `Fatal error`.
- Jméno, verzi nebo e-mail změníte na **jednom** místě (`config.php`)
  a změna se projeví všude.
- Odkaz do menu přidáte úpravou **jednoho** souboru (`menu.php`).

## Odevzdání

Práci odevzdáváte do svého repozitáře přes **Classroom 50**:

```sh
git add .
git commit -m "Cviceni 06 - vypracovano"
git push
```
