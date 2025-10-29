# Подробное руководство по проектированию и созданию каталога товаров в Битрикс

## Оглавление
1. [Этапы проектирования каталога](#этапы-проектирования)
2. [Создание структуры инфоблоков](#структура-инфоблоков)
3. [Настройка свойств товаров](#свойства-товаров)
4. [Торговые предложения](#торговые-предложения)
5. [Цены и скидки](#цены-скидки)
6. [SEO оптимизация](#seo-оптимизация)
7. [Практический пример](#практический-пример)

---

## <a name="этапы-проектирования"></a>1. Этапы проектирования каталога

### Анализ требований
```yaml
# Вопросы для анализа:
- Какие типы товаров будут в каталоге?
- Какие характеристики важны для фильтрации?
- Нужны ли варианты товаров (размеры, цвета)?
- Какая структура категорий?
- Какие типы цен необходимы?
- Требования к SEO?
```

### Проектирование структуры
```
Пример для интернет-магазина одежды:
Каталог товаров/
├── Одежда/
│   ├── Мужская/
│   │   ├── Футболки
│   │   ├── Рубашки
│   │   └── Джинсы
│   └── Женская/
│       ├── Платья
│       ├── Блузки
│       └── Юбки
├── Обувь/
└── Аксессуары/
```

---

## <a name="структура-инфоблоков"></a>2. Создание структуры инфоблоков

### Типы инфоблоков для каталога

#### Основной каталог товаров
**Настройки инфоблока:**
```php
// Админка: Контент > Инфоблоки > Типы инфоблоков > Создать
Название: "Торговый каталог"
Код: catalog
```

**Параметры инфоблока:**
```yaml
Название: "Каталог товаров"
Тип: "Торговый каталог"
Код: products
Сайты: [Выберите сайты]
Индексирование: Да
Разделы: Древовидные
Элементы: Привязка к разделам
```

### Создание разделов каталога

#### Через админку
```
Контент > Инфоблоки > Каталог товаров > Разделы
- Создать раздел "Одежда"
- Создать подраздел "Мужская одежда"
- Создать подраздел "Женская одежда"
```

#### Программное создание разделов
```php
<?php
// create_sections.php
CModule::IncludeModule('iblock');

function createCatalogSection($iblockId, $parentId, $name, $code, $sort = 100)
{
    $bs = new CIBlockSection;
    
    $arFields = array(
        'ACTIVE' => 'Y',
        'IBLOCK_ID' => $iblockId,
        'NAME' => $name,
        'CODE' => $code,
        'SORT' => $sort,
        'IBLOCK_SECTION_ID' => $parentId
    );
    
    if ($ID = $bs->Add($arFields)) {
        return $ID;
    } else {
        return false;
    }
}

// Создаем структуру
$iblockId = 2; // ID вашего инфоблока

$clothingId = createCatalogSection($iblockId, 0, 'Одежда', 'clothing', 100);
$mensId = createCatalogSection($iblockId, $clothingId, 'Мужская', 'mens', 110);
$womensId = createCatalogSection($iblockId, $clothingId, 'Женская', 'womens', 120);

createCatalogSection($iblockId, $mensId, 'Футболки', 'mens-tshirts', 100);
createCatalogSection($iblockId, $mensId, 'Рубашки', 'mens-shirts', 200);
createCatalogSection($iblockId, $womensId, 'Платья', 'womens-dresses', 100);
?>
```

---

## <a name="свойства-товаров"></a>3. Настройка свойств товаров

### Базовые свойства товара

#### Общие свойства для всех товаров
```php
// Админка: Контент > Инфоблоки > Каталог товаров > Свойства
$commonProperties = [
    [
        'NAME' => 'Артикул',
        'CODE' => 'ARTICLE',
        'TYPE' => 'S', // Строка
        'IS_REQUIRED' => 'Y',
        'SEARCHABLE' => 'Y'
    ],
    [
        'NAME' => 'Бренд',
        'CODE' => 'BRAND',
        'TYPE' => 'S',
        'SEARCHABLE' => 'Y',
        'FILTRABLE' => 'Y'
    ],
    [
        'NAME' => 'Страна производства',
        'CODE' => 'COUNTRY',
        'TYPE' => 'S',
        'FILTRABLE' => 'Y'
    ],
    [
        'NAME' => 'Материал',
        'CODE' => 'MATERIAL',
        'TYPE' => 'S',
        'FILTRABLE' => 'Y',
        'MULTIPLE' => 'Y'
    ],
    [
        'NAME' => 'Гарантия',
        'CODE' => 'WARRANTY',
        'TYPE' => 'N', // Число
        'FILTRABLE' => 'Y'
    ]
];
```

#### Свойства для одежды
```php
$clothingProperties = [
    [
        'NAME' => 'Цвет',
        'CODE' => 'COLOR',
        'TYPE' => 'L', // Список
        'FILTRABLE' => 'Y',
        'VALUES' => [
            ['VALUE' => 'Белый', 'XML_ID' => 'white'],
            ['VALUE' => 'Черный', 'XML_ID' => 'black'],
            ['VALUE' => 'Красный', 'XML_ID' => 'red']
        ]
    ],
    [
        'NAME' => 'Размер',
        'CODE' => 'SIZE',
        'TYPE' => 'L',
        'FILTRABLE' => 'Y',
        'VALUES' => [
            ['VALUE' => 'S', 'XML_ID' => 's'],
            ['VALUE' => 'M', 'XML_ID' => 'm'],
            ['VALUE' => 'L', 'XML_ID' => 'l'],
            ['VALUE' => 'XL', 'XML_ID' => 'xl']
        ]
    ],
    [
        'NAME' => 'Состав',
        'CODE' => 'COMPOSITION',
        'TYPE' => 'S'
    ],
    [
        'NAME' => 'Сезон',
        'CODE' => 'SEASON',
        'TYPE' => 'L',
        'FILTRABLE' => 'Y',
        'VALUES' => [
            ['VALUE' => 'Лето', 'XML_ID' => 'summer'],
            ['VALUE' => 'Зима', 'XML_ID' => 'winter'],
            ['VALUE' => 'Демисезон', 'XML_ID' => 'all-season']
        ]
    ]
];
```

### Программное создание свойств

```php
<?php
// create_properties.php
CModule::IncludeModule('iblock');

function createIblockProperty($iblockId, $propertyData)
{
    $ibp = new CIBlockProperty;
    
    $propertyData['IBLOCK_ID'] = $iblockId;
    $propertyData['ACTIVE'] = 'Y';
    
    if ($propertyID = $ibp->Add($propertyData)) {
        echo "Свойство {$propertyData['NAME']} создано<br>";
        return $propertyID;
    } else {
        echo "Error: " . $ibp->LAST_ERROR . "<br>";
        return false;
    }
}

$iblockId = 2;

// Создаем общие свойства
$commonProps = [
    [
        'NAME' => 'Артикул',
        'CODE' => 'ARTICLE',
        'TYPE' => 'S',
        'IS_REQUIRED' => 'Y',
        'SEARCHABLE' => 'Y'
    ],
    [
        'NAME' => 'Бренд',
        'CODE' => 'BRAND',
        'TYPE' => 'S',
        'SEARCHABLE' => 'Y',
        'FILTRABLE' => 'Y'
    ]
];

foreach ($commonProps as $prop) {
    createIblockProperty($iblockId, $prop);
}

// Создаем свойство "Цвет" со списком
$colorProperty = [
    'NAME' => 'Цвет',
    'CODE' => 'COLOR',
    'TYPE' => 'L',
    'FILTRABLE' => 'Y',
    'LIST_TYPE' => 'L',
    'MULTIPLE' => 'N',
    'VALUES' => [
        ['VALUE' => 'Белый', 'XML_ID' => 'white', 'DEF' => 'N', 'SORT' => 100],
        ['VALUE' => 'Черный', 'XML_ID' => 'black', 'DEF' => 'N', 'SORT' => 200],
        ['VALUE' => 'Красный', 'XML_ID' => 'red', 'DEF' => 'N', 'SORT' => 300]
    ]
];

createIblockProperty($iblockId, $colorProperty);
?>
```

---

## <a name="торговые-предложения"></a>4. Торговые предложения

### Когда использовать торговые предложения

```yaml
# Использовать предложения когда:
- Товар имеет варианты (размер, цвет)
- У вариантов разные цены/остатки
- Нужен отдельный артикул для варианта

# Не использовать когда:
- Товар без вариаций
- Простая структура каталога
```

### Настройка торговых предложений

#### Создание инфоблока предложений
```php
// Админка: Контент > Инфоблоки > Создать
$offerIblockSettings = [
    'NAME' => 'Торговые предложения',
    'CODE' => 'offers',
    'TYPE' => 'Торговый каталог',
    'SITE_ID' => ['s1'],
    'LANG' => [
        'ru' => ['NAME' => 'Торговые предложения']
    ]
];
```

#### Связь товаров и предложений
```php
// Настройки основного инфоблока
$catalogSettings = [
    'OFFERS_PROPERTY_ID' => 25, // ID свойства привязки
    'OFFERS_TREE_PROPS' => [26, 27], // ID свойств для построения дерева (Цвет, Размер)
    'SKU_PROPERTY_ID' => 25,
    'VAT_ID' => 0,
    'YANDEX_EXPORT' => 'Y',
    'SUBSCRIPTION' => 'N'
];
```

#### Свойства торговых предложений
```php
$offerProperties = [
    [
        'NAME' => 'Привязка к товару',
        'CODE' => 'CML2_LINK',
        'TYPE' => 'E', // Привязка к элементам
        'LINK_IBLOCK_ID' => 2, // ID основного инфоблока
        'IS_REQUIRED' => 'Y'
    ],
    [
        'NAME' => 'Цвет',
        'CODE' => 'COLOR',
        'TYPE' => 'L',
        'FILTRABLE' => 'Y',
        'VALUES' => [
            ['VALUE' => 'Белый', 'XML_ID' => 'white'],
            ['VALUE' => 'Черный', 'XML_ID' => 'black']
        ]
    ],
    [
        'NAME' => 'Размер',
        'CODE' => 'SIZE',
        'TYPE' => 'L',
        'FILTRABLE' => 'Y',
        'VALUES' => [
            ['VALUE' => 'S', 'XML_ID' => 's'],
            ['VALUE' => 'M', 'XML_ID' => 'm']
        ]
    ],
    [
        'NAME' => 'Артикул предложения',
        'CODE' => 'ARTICLE_OFFER',
        'TYPE' => 'S',
        'IS_REQUIRED' => 'Y'
    ]
];
```

### Пример структуры с предложениями

```php
// Основной товар
$product = [
    'NAME' => 'Футболка Basic',
    'CODE' => 't-shirt-basic',
    'IBLOCK_SECTION_ID' => 5, // Раздел "Футболки"
    'PROPERTY_VALUES' => [
        'BRAND' => 'Nike',
        'MATERIAL' => 'Хлопок 100%',
        'COMPOSITION' => 'Хлопок'
    ]
];

// Торговые предложения
$offers = [
    [
        'NAME' => 'Футболка Basic (Белый, S)',
        'CODE' => 't-shirt-basic-white-s',
        'PROPERTY_VALUES' => [
            'CML2_LINK' => $productId,
            'COLOR' => 'white',
            'SIZE' => 's',
            'ARTICLE_OFFER' => 'TS-BAS-WHT-S'
        ]
    ],
    [
        'NAME' => 'Футболка Basic (Белый, M)',
        'CODE' => 't-shirt-basic-white-m',
        'PROPERTY_VALUES' => [
            'CML2_LINK' => $productId,
            'COLOR' => 'white',
            'SIZE' => 'm',
            'ARTICLE_OFFER' => 'TS-BAS-WHT-M'
        ]
    ]
];
```

---

## <a name="цены-скидки"></a>5. Цены и скидки

### Настройка типов цен

#### Базовые типы цен
```php
// Админка: Магазин > Настройки > Типы цен
$priceTypes = [
    [
        'NAME' => 'Базовая цена',
        'CODE' => 'BASE',
        'BASE' => 'Y',
        'SORT' => 100
    ],
    [
        'NAME' => 'Оптовая цена',
        'CODE' => 'WHOLESALE',
        'BASE' => 'N',
        'SORT' => 200
    ],
    [
        'NAME' => 'Цена со скидкой',
        'CODE' => 'DISCOUNT',
        'BASE' => 'N',
        'SORT' => 300
    ]
];
```

#### Программное создание типов цен
```php
<?php
CModule::IncludeModule('catalog');

function createPriceType($name, $code, $isBase = false)
{
    $fields = array(
        'NAME' => $name,
        'CODE' => $code,
        'BASE' => $isBase ? 'Y' : 'N',
        'SORT' => 500
    );
    
    $res = CCatalogGroup::Add($fields);
    return $res;
}

// Создаем типы цен
createPriceType('Розничная цена', 'RETAIL', true);
createPriceType('Оптовая цена', 'WHOLESALE');
createPriceType('Специальная цена', 'SPECIAL');
?>
```

### Настройка скидок

#### Правила работы с корзиной
```php
// Админка: Магазин > Скидки > Правила корзины
$discountRules = [
    [
        'NAME' => 'Скидка 10% на заказ от 5000 руб.',
        'ACTIVE' => 'Y',
        'SITE_ID' => ['s1'],
        'CONDITIONS' => [
            'CLASS_ID' => 'CondGroup',
            'DATA' => ['All' => 'AND', 'True' => 'True'],
            'CHILDREN' => [
                [
                    'CLASS_ID' => 'CondBsktAmtGroup',
                    'DATA' => ['Logic' => 'And', 'Value' => 5000, 'Operation' => '>=']
                ]
            ]
        ],
        'ACTIONS' => [
            'CLASS_ID' => 'CondGroup',
            'DATA' => ['All' => 'AND'],
            'CHILDREN' => [
                [
                    'CLASS_ID' => 'ActSaleBsktGrp',
                    'DATA' => [
                        'Type' => 'Discount',
                        'Value' => 10,
                        'Unit' => 'Perc',
                        'Max' => 0,
                        'All' => 'AND'
                    ]
                ]
            ]
        ]
    ]
];
```

#### Скидки на группы товаров
```php
$productDiscounts = [
    [
        'NAME' => 'Скидка 15% на бренд Nike',
        'ACTIVE' => 'Y',
        'CONDITIONS' => [
            'CLASS_ID' => 'CondGroup',
            'DATA' => ['All' => 'AND'],
            'CHILDREN' => [
                [
                    'CLASS_ID' => 'CondIBProp:2', // Свойство инфоблока 2
                    'DATA' => ['logic' => 'Equal', 'value' => 'Nike']
                ]
            ]
        ],
        'ACTIONS' => [
            'CLASS_ID' => 'CondGroup',
            'DATA' => ['All' => 'AND'],
            'CHILDREN' => [
                [
                    'CLASS_ID' => 'ActSaleBsktGrp',
                    'DATA' => [
                        'Type' => 'Discount',
                        'Value' => 15,
                        'Unit' => 'Perc'
                    ]
                ]
            ]
        ]
    ]
];
```

---

## <a name="seo-оптимизация"></a>6. SEO оптимизация каталога

### SEO настройки разделов и элементов

#### Шаблоны URL
```php
// В настройках инфоблока
$seoSettings = [
    'SECTION_PAGE_URL' => '#SECTION_CODE_PATH#/',
    'DETAIL_PAGE_URL' => '#SECTION_CODE_PATH#/#ELEMENT_CODE#/',
    'CANONICAL_PAGE_URL' => '#SECTION_CODE_PATH#/#ELEMENT_CODE#/'
];

// Примеры ЧПУ:
// Раздел: /clothing/mens/tshirts/
// Товар: /clothing/mens/tshirts/t-shirt-basic/
```

#### Программная настройка SEO
```php
<?php
// Для элемента товара
$elementId = 123;
$arFields = array(
    "ELEMENT_META_TITLE" => "Купить футболку Basic в Москве - цена, отзывы",
    "ELEMENT_META_DESCRIPTION" => "Футболка Basic из 100% хлопка. Размеры S-XXL. ✓ Низкие цены ✓ Быстрая доставка ✓ Гарантия качества",
    "ELEMENT_META_KEYWORDS" => "футболка basic, купить футболку, мужские футболки",
    "ELEMENT_PAGE_TITLE" => "Футболка Basic",
    "SECTION_META_TITLE" => "Мужские футболки - каталог и цены",
    "SECTION_META_DESCRIPTION" => "Большой выбор мужских футболок в интернет-магазине. Различные цвета и размеры."
);

CIBlockElement::SetElementSection($elementId, $arFields);
?>
```

### Генерация sitemap.xml

```php
<?php
// sitemap_generator.php
CModule::IncludeModule('iblock');
CModule::IncludeModule('search');

function generateCatalogSitemap($iblockId, $baseUrl)
{
    $sitemap = '<?xml version="1.0" encoding="UTF-8"?>' . PHP_EOL;
    $sitemap .= '<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">' . PHP_EOL;
    
    // Разделы
    $res = CIBlockSection::GetList(
        ['SORT' => 'ASC'],
        ['IBLOCK_ID' => $iblockId, 'ACTIVE' => 'Y'],
        false,
        ['ID', 'CODE', 'TIMESTAMP_X']
    );
    
    while ($section = $res->GetNext()) {
        $url = $baseUrl . $section['CODE'] . '/';
        $sitemap .= generateSitemapUrl($url, $section['TIMESTAMP_X']);
    }
    
    // Элементы
    $res = CIBlockElement::GetList(
        ['SORT' => 'ASC'],
        ['IBLOCK_ID' => $iblockId, 'ACTIVE' => 'Y'],
        false,
        false,
        ['ID', 'CODE', 'TIMESTAMP_X', 'IBLOCK_SECTION_ID']
    );
    
    while ($element = $res->GetNext()) {
        // Получаем путь раздела
        $sectionPath = getSectionPath($element['IBLOCK_SECTION_ID']);
        $url = $baseUrl . $sectionPath . $element['CODE'] . '/';
        $sitemap .= generateSitemapUrl($url, $element['TIMESTAMP_X']);
    }
    
    $sitemap .= '</urlset>';
    
    file_put_contents($_SERVER['DOCUMENT_ROOT'] . '/sitemap_catalog.xml', $sitemap);
}

function generateSitemapUrl($url, $lastmod)
{
    return "\t<url>\n" .
           "\t\t<loc>" . htmlspecialchars($url) . "</loc>\n" .
           "\t\t<lastmod>" . date('c', strtotime($lastmod)) . "</lastmod>\n" .
           "\t\t<changefreq>weekly</changefreq>\n" .
           "\t\t<priority>0.8</priority>\n" .
           "\t</url>\n";
}
?>
```

---

## <a name="практический-пример"></a>7. Практический пример: Интернет-магазин одежды

### Полная настройка каталога

#### 1. Создание инфоблока
```php
<?php
// complete_catalog_setup.php
CModule::IncludeModule('iblock');
CModule::IncludeModule('catalog');

// 1. Создаем инфоблок каталога
$iblock = new CIBlock;

$arFields = array(
    "NAME" => "Каталог одежды",
    "CODE" => "clothing_catalog",
    "IBLOCK_TYPE_ID" => "catalog",
    "SITE_ID" => array("s1"),
    "SORT" => 100,
    "GROUP_ID" => array("2" => "R"), // Права доступа
    "VERSION" => 2,
    "LIST_MODE" => "S",
    "WORKFLOW" => "N",
    "BIZPROC" => "N",
    "INDEX_ELEMENT" => "Y",
    "INDEX_SECTION" => "Y",
);

$IBLOCK_ID = $iblock->Add($arFields);
```

#### 2. Создание структуры разделов
```php
// Создаем разделы
$sections = [
    ['NAME' => 'Одежда', 'CODE' => 'clothing'],
    ['NAME' => 'Мужская', 'CODE' => 'mens', 'PARENT' => 'clothing'],
    ['NAME' => 'Женская', 'CODE' => 'womens', 'PARENT' => 'clothing'],
    ['NAME' => 'Футболки', 'CODE' => 'tshirts', 'PARENT' => 'mens'],
    ['NAME' => 'Рубашки', 'CODE' => 'shirts', 'PARENT' => 'mens'],
    ['NAME' => 'Платья', 'CODE' => 'dresses', 'PARENT' => 'womens'],
];

$sectionMap = [];
foreach ($sections as $section) {
    $parentId = 0;
    if (isset($section['PARENT'])) {
        $parentId = $sectionMap[$section['PARENT']];
    }
    
    $bs = new CIBlockSection;
    $arFields = array(
        "IBLOCK_ID" => $IBLOCK_ID,
        "NAME" => $section['NAME'],
        "CODE" => $section['CODE'],
        "IBLOCK_SECTION_ID" => $parentId,
        "ACTIVE" => "Y",
    );
    
    $sectionMap[$section['CODE']] = $bs->Add($arFields);
}
```

#### 3. Добавление свойств
```php
// Общие свойства
$properties = [
    [
        "NAME" => "Бренд",
        "CODE" => "BRAND", 
        "TYPE" => "S",
        "FILTRABLE" => "Y",
        "SEARCHABLE" => "Y"
    ],
    [
        "NAME" => "Состав",
        "CODE" => "COMPOSITION",
        "TYPE" => "S"
    ],
    [
        "NAME" => "Страна",
        "CODE" => "COUNTRY",
        "TYPE" => "S",
        "FILTRABLE" => "Y"
    ]
];

foreach ($properties as $prop) {
    $ibp = new CIBlockProperty;
    $prop['IBLOCK_ID'] = $IBLOCK_ID;
    $ibp->Add($prop);
}
```

#### 4. Добавление товаров
```php
// Добавляем товары
$products = [
    [
        'NAME' => 'Футболка Basic',
        'CODE' => 't-shirt-basic',
        'SECTION' => 'tshirts',
        'PROPERTIES' => [
            'BRAND' => 'Nike',
            'COMPOSITION' => 'Хлопок 100%',
            'COUNTRY' => 'Китай'
        ],
        'PRICE' => 1990
    ],
    [
        'NAME' => 'Рубашка офисная',
        'CODE' => 'shirt-office',
        'SECTION' => 'shirts', 
        'PROPERTIES' => [
            'BRAND' => 'Zara',
            'COMPOSITION' => 'Хлопок 80%, Полиэстер 20%',
            'COUNTRY' => 'Турция'
        ],
        'PRICE' => 3490
    ]
];

foreach ($products as $product) {
    $el = new CIBlockElement;
    
    $arFields = array(
        "IBLOCK_ID" => $IBLOCK_ID,
        "NAME" => $product['NAME'],
        "CODE" => $product['CODE'],
        "IBLOCK_SECTION_ID" => $sectionMap[$product['SECTION']],
        "ACTIVE" => "Y",
        "PREVIEW_TEXT" => "Качественный товар по доступной цене",
        "DETAIL_TEXT" => "Подробное описание товара...",
        "PROPERTY_VALUES" => $product['PROPERTIES']
    );
    
    $PRODUCT_ID = $el->Add($arFields);
    
    // Добавляем цену
    if ($PRODUCT_ID) {
        CPrice::SetBasePrice($PRODUCT_ID, $product['PRICE'], 'RUB');
    }
}
```

### Проверка и тестирование

#### Валидация структуры
```php
<?php
// validate_catalog.php
function validateCatalogStructure($iblockId)
{
    $errors = [];
    
    // Проверяем наличие обязательных свойств
    $requiredProps = ['BRAND', 'ARTICLE'];
    foreach ($requiredProps as $propCode) {
        $res = CIBlockProperty::GetList([], [
            'IBLOCK_ID' => $iblockId,
            'CODE' => $propCode
        ]);
        if (!$res->Fetch()) {
            $errors[] = "Отсутствует обязательное свойство: {$propCode}";
        }
    }
    
    // Проверяем разделы без товаров
    $res = CIBlockSection::GetList(
        [],
        [
            'IBLOCK_ID' => $iblockId,
            'ELEMENT_SUBSECTIONS' => 'N'
        ],
        true
    );
    
    while ($section = $res->GetNext()) {
        if ($section['ELEMENT_CNT'] == 0) {
            $errors[] = "Раздел '{$section['NAME']}' не содержит товаров";
        }
    }
    
    return $errors;
}
?>
```

Это подробное руководство охватывает все этапы создания каталога товаров в Битрикс - от проектирования до реализации и тестирования. Структура может быть адаптирована под конкретные требования вашего проекта.