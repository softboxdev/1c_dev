# Подробное руководство по кастомизации и разработке компонентов в 1С-Битрикс

## Оглавление
1. [Архитектура компонентов Битрикс](#архитектура)
2. [Структура компонента](#структура)
3. [Кастомизация стандартных компонентов](#кастомизация)
4. [Создание собственных компонентов](#создание)
5. [Практические примеры](#примеры)
6. [Best Practices](#best-practices)

---

## <a name="архитектура"></a>1. Архитектура компонентов Битрикс

### Типы компонентов
- **Простой компонент** - вывод без сложной логики
- **Комплексный компонент** - включает другие компоненты
- **Умный компонент** (.script) - современный подход с использованием script.js

### Файловая структура компонентов
```
/bitrix/components/
    /bitrix/          # Стандартные компоненты
        /component.name/
    /custom/          # Пользовательские компоненты
        /component.name/
```

---

## <a name="структура"></a>2. Структура компонента

### Обязательные файлы компонента
```
component.name/
    ├── .description.php     # Описание компонента
    ├── component.php        # Основной логический файл
    ├── .parameters.php      # Параметры компонента
    ├── template.php         # Файл шаблона по умолчанию
    └── /templates/          # Директория шаблонов
        ├── .default/
        │   ├── template.php
        │   ├── style.css
        │   └── script.js
        └── custom_template/
            ├── template.php
            └── .description.php
```

### .description.php
```php
<?php
if (!defined('B_PROLOG_INCLUDED') || B_PROLOG_INCLUDED !== true) die();

$arComponentDescription = array(
    'NAME' => GetMessage('CUSTOM_COMPONENT_NAME'),
    'DESCRIPTION' => GetMessage('CUSTOM_COMPONENT_DESCRIPTION'),
    'PATH' => array(
        'ID' => 'custom',
        'NAME' => GetMessage('CUSTOM_COMPONENT_CATEGORY'),
    ),
    'ICON' => '/images/icon.gif',
    'CACHE_PATH' => 'Y',
    'COMPLEX' => 'N',
);
?>
```

### .parameters.php
```php
<?php
if (!defined('B_PROLOG_INCLUDED') || B_PROLOG_INCLUDED !== true) die();

$arComponentParameters = array(
    'PARAMETERS' => array(
        'ELEMENT_ID' => array(
            'PARENT' => 'BASE',
            'NAME' => GetMessage('CUSTOM_ELEMENT_ID'),
            'TYPE' => 'STRING',
            'DEFAULT' => '',
        ),
        'CACHE_TIME' => array('DEFAULT' => 3600),
        'DISPLAY_FIELDS' => array(
            'PARENT' => 'BASE',
            'NAME' => GetMessage('CUSTOM_DISPLAY_FIELDS'),
            'TYPE' => 'LIST',
            'MULTIPLE' => 'Y',
            'VALUES' => array(
                'NAME' => GetMessage('CUSTOM_FIELD_NAME'),
                'PREVIEW_TEXT' => GetMessage('CUSTOM_FIELD_PREVIEW_TEXT'),
                'DETAIL_TEXT' => GetMessage('CUSTOM_FIELD_DETAIL_TEXT'),
            ),
        ),
    ),
);
?>
```

---

## <a name="кастомизация"></a>3. Кастомизация стандартных компонентов

### Метод 1: Создание дочернего компонента

#### Шаг 1: Копирование компонента
```bash
# Копируем стандартный компонент
cp -r /bitrix/components/bitrix/news.list /local/components/custom/news.list
```

#### Шаг 2: Модификация .parameters.php
```php
// Добавляем кастомные параметры
$arComponentParameters['PARAMETERS']['CUSTOM_FIELD'] = array(
    'PARENT' => 'BASE',
    'NAME' => 'Кастомное поле',
    'TYPE' => 'STRING',
    'DEFAULT' => 'Значение по умолчанию',
);
```

#### Шаг 3: Модификация component.php
```php
<?php
if (!defined('B_PROLOG_INCLUDED') || B_PROLOG_INCLUDED !== true) die();

// Подключаем родительский компонент
CBitrixComponent::includeComponentClass('bitrix:news.list');

class CustomNewsListComponent extends CBitrixNewsListComponent
{
    public function onPrepareComponentParams($arParams)
    {
        $arParams = parent::onPrepareComponentParams($arParams);
        
        // Кастомная логика
        if (empty($arParams['CUSTOM_FIELD'])) {
            $arParams['CUSTOM_FIELD'] = 'default';
        }
        
        return $arParams;
    }
    
    protected function getElements()
    {
        // Дополняем родительский метод
        $result = parent::getElements();
        
        // Кастомная обработка элементов
        foreach ($result as &$element) {
            $element['CUSTOM_PROPERTY'] = $this->processCustomProperty($element);
        }
        
        return $result;
    }
    
    private function processCustomProperty($element)
    {
        // Логика обработки
        return strtoupper($element['NAME']);
    }
}
?>
```

### Метод 2: Создание кастомного шаблона

#### Шаг 1: Создание директории шаблона
```bash
# В директории компонента
mkdir -p /bitrix/components/bitrix/news.list/templates/custom_template
```

#### Шаг 2: Создание template.php для кастомного шаблона
```php
<?php
if (!defined('B_PROLOG_INCLUDED') || B_PROLOG_INCLUDED !== true) die();

/** @var array $arParams */
/** @var array $arResult */
/** @global CMain $APPLICATION */
?>

<div class="custom-news-list">
    <?php foreach ($arResult['ITEMS'] as $item): ?>
        <div class="news-item custom-layout">
            <h3 class="news-title"><?= $item['NAME'] ?></h3>
            <div class="news-date"><?= FormatDate('d.m.Y', MakeTimeStamp($item['DATE_CREATE'])) ?></div>
            <div class="news-preview"><?= $item['PREVIEW_TEXT'] ?></div>
            
            <!-- Кастомное поле -->
            <?php if ($item['PROPERTIES']['CUSTOM_FIELD']['VALUE']): ?>
                <div class="custom-field">
                    <?= $item['PROPERTIES']['CUSTOM_FIELD']['VALUE'] ?>
                </div>
            <?php endif; ?>
        </div>
    <?php endforeach; ?>
</div>

<?php
// Кастомная пагинация
if ($arParams['DISPLAY_BOTTOM_PAGER'] == 'Y') {
    echo $arResult['NAV_STRING'];
}
?>
```

#### Шаг 3: Создание .description.php для шаблона
```php
<?php
if (!defined('B_PROLOG_INCLUDED') || B_PROLOG_INCLUDED !== true) die();

$arTemplateDescription = array(
    'NAME' => 'Кастомный шаблон новостей',
    'DESCRIPTION' => 'Шаблон с дополнительными полями и стилями',
);
?>
```

---

## <a name="создание"></a>4. Создание собственных компонентов

### Пример: Компонент "Пользовательский каталог"

#### Структура компонента
```
custom.catalog/
    ├── .description.php
    ├── .parameters.php
    ├── component.php
    └── templates/
        └── .default/
            ├── template.php
            ├── style.css
            └── script.js
```

#### component.php
```php
<?php
if (!defined('B_PROLOG_INCLUDED') || B_PROLOG_INCLUDED !== true) die();

class CustomCatalogComponent extends CBitrixComponent
{
    private $errors = array();
    
    public function onPrepareComponentParams($arParams)
    {
        // Обработка параметров
        $arParams['IBLOCK_ID'] = (int)$arParams['IBLOCK_ID'];
        $arParams['ELEMENTS_COUNT'] = (int)$arParams['ELEMENTS_COUNT'] ?: 20;
        $arParams['CACHE_TIME'] = (int)$arParams['CACHE_TIME'] ?: 3600;
        
        return $arParams;
    }
    
    public function executeComponent()
    {
        try {
            $this->checkRequirements();
            $this->getElements();
            $this->includeComponentTemplate();
        } catch (Exception $e) {
            $this->errors[] = $e->getMessage();
            $this->arResult['ERRORS'] = $this->errors;
        }
    }
    
    private function checkRequirements()
    {
        if (!CModule::IncludeModule('iblock')) {
            throw new Exception('Модуль iblock не установлен');
        }
        
        if (empty($this->arParams['IBLOCK_ID'])) {
            throw new Exception('Не указан инфоблок');
        }
    }
    
    private function getElements()
    {
        if ($this->StartResultCache(false, $this->getAdditionalCacheId())) {
            $filter = $this->getFilter();
            $select = $this->getSelect();
            $navParams = $this->getNavParams();
            
            $elements = CIBlockElement::GetList(
                $this->getSort(),
                $filter,
                false,
                $navParams,
                $select
            );
            
            $this->arResult['ITEMS'] = array();
            $this->arResult['NAV_OBJECT'] = $elements;
            
            while ($element = $elements->GetNextElement()) {
                $fields = $element->GetFields();
                $properties = $element->GetProperties();
                
                $this->arResult['ITEMS'][] = array(
                    'FIELDS' => $fields,
                    'PROPERTIES' => $properties,
                    'ACTIVE_FROM' => $this->formatDate($fields['ACTIVE_FROM']),
                );
            }
            
            if (empty($this->arResult['ITEMS'])) {
                $this->AbortResultCache();
            }
            
            $this->EndResultCache();
        }
    }
    
    private function getFilter()
    {
        $filter = array(
            'IBLOCK_ID' => $this->arParams['IBLOCK_ID'],
            'ACTIVE' => 'Y',
            'CHECK_PERMISSIONS' => 'Y',
        );
        
        // Дополнительная фильтрация
        if (!empty($this->arParams['SECTION_ID'])) {
            $filter['SECTION_ID'] = $this->arParams['SECTION_ID'];
        }
        
        return $filter;
    }
    
    private function getSelect()
    {
        return array(
            'ID',
            'IBLOCK_ID',
            'NAME',
            'DATE_ACTIVE_FROM',
            'PREVIEW_TEXT',
            'PREVIEW_PICTURE',
            'DETAIL_PAGE_URL',
        );
    }
    
    private function getSort()
    {
        return array(
            'SORT' => 'ASC',
            'DATE_ACTIVE_FROM' => 'DESC',
            'ID' => 'DESC',
        );
    }
    
    private function getNavParams()
    {
        return array(
            'nPageSize' => $this->arParams['ELEMENTS_COUNT'],
            'bShowAll' => false,
        );
    }
    
    private function getAdditionalCacheId()
    {
        return array(
            $this->arParams['IBLOCK_ID'],
            $this->arParams['SECTION_ID'],
            $this->arParams['ELEMENTS_COUNT'],
        );
    }
    
    private function formatDate($date)
    {
        if (!$date) return '';
        return FormatDate('d F Y', MakeTimeStamp($date));
    }
}
?>
```

#### template.php
```php
<?php
if (!defined('B_PROLOG_INCLUDED') || B_PROLOG_INCLUDED !== true) die();

/** @var array $arParams */
/** @var array $arResult */
/** @global CMain $APPLICATION */
?>

<?php if (!empty($arResult['ERRORS'])): ?>
    <div class="alert alert-danger">
        <?= implode('<br>', $arResult['ERRORS']) ?>
    </div>
<?php endif; ?>

<div class="custom-catalog" id="custom-catalog-<?= $this->randString() ?>">
    <?php if (!empty($arResult['ITEMS'])): ?>
        <div class="catalog-grid">
            <?php foreach ($arResult['ITEMS'] as $item): ?>
                <div class="catalog-item" data-id="<?= $item['FIELDS']['ID'] ?>">
                    <div class="item-image">
                        <?php if ($item['FIELDS']['PREVIEW_PICTURE']): ?>
                            <?php
                            $picture = CFile::GetFileArray($item['FIELDS']['PREVIEW_PICTURE']);
                            ?>
                            <img 
                                src="<?= $picture['SRC'] ?>" 
                                alt="<?= htmlspecialcharsbx($item['FIELDS']['NAME']) ?>"
                                loading="lazy"
                            >
                        <?php endif; ?>
                    </div>
                    
                    <div class="item-content">
                        <h3 class="item-title">
                            <a href="<?= $item['FIELDS']['DETAIL_PAGE_URL'] ?>">
                                <?= $item['FIELDS']['NAME'] ?>
                            </a>
                        </h3>
                        
                        <div class="item-date">
                            <?= $item['ACTIVE_FROM'] ?>
                        </div>
                        
                        <div class="item-preview">
                            <?= $item['FIELDS']['PREVIEW_TEXT'] ?>
                        </div>
                        
                        <?php if (!empty($item['PROPERTIES'])): ?>
                            <div class="item-properties">
                                <?php foreach ($item['PROPERTIES'] as $property): ?>
                                    <?php if ($property['VALUE'] && $property['CODE'] != 'SECTION_PAGE_URL'): ?>
                                        <div class="property">
                                            <strong><?= $property['NAME'] ?>:</strong>
                                            <?= $this->formatPropertyValue($property) ?>
                                        </div>
                                    <?php endif; ?>
                                <?php endforeach; ?>
                            </div>
                        <?php endif; ?>
                    </div>
                </div>
            <?php endforeach; ?>
        </div>
        
        <!-- Пагинация -->
        <?php if ($arResult['NAV_OBJECT']): ?>
            <div class="catalog-pagination">
                <?= $arResult['NAV_OBJECT']->GetPageNavString(GetMessage('NAV_TITLE')) ?>
            </div>
        <?php endif; ?>
    <?php else: ?>
        <div class="alert alert-info">
            <?= GetMessage('NO_ITEMS_FOUND') ?>
        </div>
    <?php endif; ?>
</div>

<script>
    BX.ready(function() {
        // Инициализация компонента
        new BX.Custom.Catalog({
            container: document.getElementById('custom-catalog-<?= $this->randString() ?>'),
            ajaxUrl: '<?= $this->GetFolder() ?>/ajax.php'
        });
    });
</script>
```

---

## <a name="примеры"></a>5. Практические примеры

### Пример 1: AJAX-компонент

#### component.php (дополнение)
```php
private function handleAjaxRequest()
{
    if ($_REQUEST['ajax_action'] == 'load_more' && check_bitrix_sessid()) {
        $this->arParams['PAGE'] = (int)$_REQUEST['page'];
        $this->getElements();
        
        // Рендерим только items
        $GLOBALS['APPLICATION']->RestartBuffer();
        header('Content-Type: application/json');
        
        echo json_encode(array(
            'items' => $this->arResult['ITEMS'],
            'hasMore' => $this->hasMoreItems(),
        ));
        
        die();
    }
}

public function executeComponent()
{
    if ($_REQUEST['ajax_action'] && $_REQUEST['component'] == $this->getName()) {
        $this->handleAjaxRequest();
    } else {
        parent::executeComponent();
    }
}
```

### Пример 2: Компонент с кешированием сложных данных

```php
private function getComplexData()
{
    if ($this->StartResultCache()) {
        $this->arResult['COMPLEX_DATA'] = array();
        
        // Сложные запросы к БД
        $this->getCatalogData();
        $this->getUserData();
        $this->getExternalData();
        
        // Добавляем теги кеширования
        $this->SetResultCacheKeys(array(
            'COMPLEX_DATA',
            'NAV_OBJECT',
        ));
        
        // Добавляем теги для инвалидации
        $cp = $this->getCachePath();
        $this->getTaggedCache()->RegisterTag('iblock_id_' . $this->arParams['IBLOCK_ID']);
        
        $this->EndResultCache();
    }
}
```

---

## <a name="best-practices"></a>6. Best Practices

### Безопасность
```php
// Всегда проверяйте входные параметры
$arParams['IBLOCK_ID'] = (int)$arParams['IBLOCK_ID'];

// Используйте битриксовые функции для экранирования
echo htmlspecialcharsbx($value);
echo HtmlFilter::encode($value);

// Проверяйте права доступа
if (!CIBlockSectionRights::UserHasRightTo($iblockId, $sectionId, 'section_read')) {
    throw new Exception('Доступ запрещен');
}
```

### Производительность
```php
// Правильное кеширование
$this->StartResultCache(
    $this->arParams['CACHE_TIME'],
    $this->getCacheId(),
    $this->getCachePath()
);

// Используйте правильные методы API
CIBlockElement::GetList(); // Вместо CIBlockElement::GetListEx()
CModule::IncludeModule('iblock'); // Вместо Bitrix\Main\Loader::includeModule()

// Оптимизируйте запросы
$select = array('ID', 'NAME'); // Выбирайте только нужные поля
```

### Поддержка многосайтовости
```php
private function getSiteSettings()
{
    $siteId = SITE_ID;
    $settings = \Bitrix\Main\Config\Option::get(
        'custom.module', 
        'settings_' . $siteId
    );
    
    return unserialize($settings);
}
```

### Логирование
```php
private function logError($message, $context = array())
{
    AddMessage2Log(
        $message . PHP_EOL . print_r($context, true),
        'custom.component'
    );
}
```

Это руководство покрывает все основные аспекты работы с компонентами в Битрикс - от кастомизации стандартных до создания сложных собственных компонентов с поддержкой AJAX, кеширования и соблюдением best practices.