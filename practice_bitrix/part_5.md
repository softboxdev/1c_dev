
---

## **Битрикс: Модули и компоненты - Полное руководство**

Это руководство охватывает архитектурные основы разработки в Битрикс, включая создание собственных модулей и компонентов, их взаимодействие и кастомизацию.

---

### **1. Подробное рассмотрение структуры файлов**

Понимание файловой структуры — основа профессиональной разработки в Битрикс.

#### **Ключевые папки и их назначение:**

```
/bitrix/          # Ядро системы, модули, админка (НЕ РЕДАКТИРУЕТСЯ!)
  /modules/       # Все модули системы
    /main/        # Главный модуль
    /iblock/      # Модуль информационных блоков
    /your.module/ # Ваш кастомный модуль
  /components/    # Стандартные компоненты
  /templates/     # Стандартные шаблоны
  /activities/    # Бизнес-процессы
  /cache/         # Системный кеш

/upload/          # Файлы, загруженные через систему
  /iblock/        # Изображения и файлы инфоблоков
  /resize_cache/  # Кеш ресайза изображений

/local/           # Рекомендуемая папка для кастомной разработки
  /components/    # Ваши компоненты и переопределения
    /bitrix/      # Переопределения стандартных компонентов
    /custom/      # Ваши собственные компоненты
  /templates/     # Шаблоны сайта
  /php_interface/ # Файлы инициализации
    /init.php     # Главный файл инициализации
    /dbconn.php   # Настройки подключения к БД
  /modules/       # Ваши кастомные модули
  /activities/    # Кастомные бизнес-процессы

/highloadblock/   # Данные highload-блоков (если используются)
```

#### **Важные принципы:**

- **`/bitrix/`** — системная папка, обновляется автоматически. Изменения будут потеряны при обновлении.
- **`/local/`** — ваша рабочая зона. Битрикс автоматически ищет файлы сначала здесь, затем в `/bitrix/`.
- **При обновлении системы** папка `/local/` не затрагивается.

---

### **2. Взаимодействие модулей; компонент и принципов их организации**

#### **Модули в Битрикс**

**Модуль** — это функционально законченный блок системы, который можно включать/выключать.

**Структура кастомного модуля:**

```
/local/modules/
  /your.module/
    /install/              # Файлы установки/удаления
      /version.php         # Версия модуля
      /index.php           # Установщик модуля
      /admin/              # Админ-страницы
      /components/         # Компоненты модуля
      /js/                 # JavaScript файлы
      /lang/               # Языковые файлы
        /ru/
          /install.php
    /include.php           # Главный файл модуля
    /default_option.php    # Настройки по умолчанию
    /prolog.php            # Файл инициализации
```

**Пример установки модуля:**

```php
<?php
// /local/modules/your.module/install/index.php
if (!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED !== true) die();

class your_module extends CModule
{
    public $MODULE_ID = "your.module";
    public $MODULE_VERSION = "1.0.0";
    public $MODULE_VERSION_DATE = "2024-01-01 00:00:00";
    public $MODULE_NAME = "Кастомный модуль";
    public $MODULE_DESCRIPTION = "Описание модуля";

    public function DoInstall()
    {
        RegisterModule($this->MODULE_ID);
        // Дополнительные действия при установке
        $this->InstallEvents();
        $this->InstallFiles();
    }

    public function DoUninstall()
    {
        // Дополнительные действия при удалении
        $this->UnInstallEvents();
        $this->UnInstallFiles();
        UnRegisterModule($this->MODULE_ID);
    }

    public function InstallEvents() { /* ... */ }
    public function UnInstallEvents() { /* ... */ }
    public function InstallFiles() { /* ... */ }
    public function UnInstallFiles() { /* ... */ }
}
?>
```

#### **Взаимодействие модулей**

**1. Подключение модулей:**
```php
<?
// Проверка наличия и подключение модуля
if (CModule::IncludeModule("iblock")) {
    // Модуль доступен
    $iblock = new CIBlockElement;
}

// Или более современный способ
if (Bitrix\Main\Loader::includeModule("iblock")) {
    // Работа с модулем
}
?>
```

**2. События между модулями:**
```php
<?
// Генерация события из вашего модуля
$event = new \Bitrix\Main\Event("your.module", "OnAfterSomeAction", [
    "PARAM1" => $value1,
    "PARAM2" => $value2
]);
$event->send();

// Обработка события в другом модуле
AddEventHandler("your.module", "OnAfterSomeAction", "handleYourModuleEvent");
function handleYourModuleAction($params) {
    // Обработка события
}
?>
```

#### **Компоненты и принципы их организации**

**Компонент** — это визуальный элемент, который отвечает за отображение данных и взаимодействие с пользователем.

**Структура компонента:**

```
/local/components/
  /namespace/              # Пространство имен (bitrix, custom)
    /component.name/       # Код компонента
      /class.php           # Класс компонента (для complex компонентов)
      /.description.php    # Описание компонента
      /.parameters.php     # Параметры по умолчанию
      /component.php       # Логика компонента
      /templates/          # Шаблоны отображения
        /.default/         # Шаблон по умолчанию
          /template.php    # Файл шаблона
          /style.css       # Стили шаблона
          /script.js       # Скрипты шаблона
```

---

### **3. Кастомизация и модификация компонентов из типовой поставки и разработка**

#### **Кастомизация стандартных компонентов**

**Способ 1: Переопределение через /local/components/**

Создайте аналогичную структуру в `/local/components/`:

```
/local/components/bitrix/
  /news.list/              # Переопределение news.list
    /templates/
      /custom.template/    # Ваш кастомный шаблон
        template.php
```

**В шаблоне страницы:**
```php
<?php
// Использование кастомного шаблона
$APPLICATION->IncludeComponent(
    "bitrix:news.list",
    "custom.template",
    array(
        "IBLOCK_ID" => 2,
        "SORT_BY1" => "ACTIVE_FROM",
        "SORT_ORDER1" => "DESC",
    ),
    $component
);
?>
```

**Способ 2: Наследование и расширение функциональности**

Создайте свой компонент на основе стандартного:

```php
<?php
// /local/components/custom/news.list/class.php
if (!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED !== true) die();

class CustomNewsListComponent extends \CBitrixNewsListComponent
{
    // Переопределение метода
    public function myCustomMethod($params)
    {
        // Дополнительная логика
        return parent::myCustomMethod($params);
    }
    
    // Расширение выполнения компонента
    public function executeComponent()
    {
        // Дополнительные действия перед выполнением
        $this->prepareCustomData();
        
        // Вызов родительского метода
        parent::executeComponent();
        
        // Дополнительные действия после выполнения
        $this->processCustomResult();
    }
    
    private function prepareCustomData()
    {
        // Ваша кастомная логика подготовки данных
    }
}
?>
```

#### **Разработка собственных компонентов**

**1. Простой компонент (без класса):**

```php
<?php
// /local/components/custom/simple.component/component.php
if (!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED !== true) die();

// Подключение модулей
if (!CModule::IncludeModule("iblock")) {
    ShowError("Модуль инфоблоков не установлен");
    return;
}

// Обработка параметров
$arParams["IBLOCK_ID"] = intval($arParams["IBLOCK_ID"]);
$arParams["ELEMENT_COUNT"] = $arParams["ELEMENT_COUNT"] ?: 10;

// Получение данных
$arResult["ITEMS"] = [];
$res = CIBlockElement::GetList(
    ["DATE_ACTIVE_FROM" => "DESC"],
    ["IBLOCK_ID" => $arParams["IBLOCK_ID"], "ACTIVE" => "Y"],
    false,
    ["nTopCount" => $arParams["ELEMENT_COUNT"]],
    ["ID", "NAME", "DETAIL_PAGE_URL", "PREVIEW_TEXT"]
);

while ($item = $res->GetNext()) {
    $arResult["ITEMS"][] = $item;
}

// Подключение шаблона
$this->IncludeComponentTemplate();
?>
```

**2. Complex-компонент (с классом):**

```php
<?php
// /local/components/custom/complex.component/class.php
if (!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED !== true) die();

class CustomComplexComponent extends \CBitrixComponent
{
    /**
     * Подготовка параметров компонента
     */
    public function onPrepareComponentParams($arParams)
    {
        $arParams["IBLOCK_ID"] = intval($arParams["IBLOCK_ID"]);
        $arParams["CACHE_TIME"] = $arParams["CACHE_TIME"] ?? 3600;
        $arParams["DETAIL_URL"] = trim($arParams["DETAIL_URL"]);
        
        return $arParams;
    }
    
    /**
     * Получение данных
     */
    private function getData()
    {
        if ($this->startResultCache(false, $this->getCacheAdditional())) {
            $this->arResult["ITEMS"] = $this->fetchElements();
            $this->setResultCacheKeys(["ITEMS", "NAV_STRING"]);
            $this->endResultCache();
        }
    }
    
    /**
     * Получение элементов
     */
    private function fetchElements()
    {
        $elements = [];
        $res = CIBlockElement::GetList(
            $this->getSort(),
            $this->getFilter(),
            false,
            $this->getNavParams(),
            $this->getSelect()
        );
        
        while ($element = $res->GetNext()) {
            $elements[] = $this->processElement($element);
        }
        
        $this->arResult["NAV_STRING"] = $res->GetPageNavStringEx();
        
        return $elements;
    }
    
    /**
     * Основной метод выполнения компонента
     */
    public function executeComponent()
    {
        try {
            $this->checkModules();
            $this->getData();
            $this->includeComponentTemplate();
        } catch (Exception $e) {
            ShowError($e->getMessage());
        }
    }
    
    private function checkModules()
    {
        if (!CModule::IncludeModule("iblock")) {
            throw new Exception("Модуль инфоблоков не установлен");
        }
    }
    
    // Вспомогательные методы...
    private function getSort() { return ["ID" => "DESC"]; }
    private function getFilter() { return ["IBLOCK_ID" => $this->arParams["IBLOCK_ID"]]; }
    private function getSelect() { return ["ID", "NAME", "DETAIL_PAGE_URL"]; }
    private function getNavParams() { return ["nPageSize" => $this->arParams["ELEMENT_COUNT"]]; }
    private function processElement($element) { return $element; }
    private function getCacheAdditional() { return []; }
}
?>
```

**Файл описания компонента:**
```php
<?php
// /local/components/custom/complex.component/.description.php
if (!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED !== true) die();

$arComponentDescription = [
    "NAME" => "Кастомный комплексный компонент",
    "DESCRIPTION" => "Выводит элементы с продвинутой логикой",
    "PATH" => [
        "ID" => "custom",
        "NAME" => "Кастомные компоненты"
    ],
    "ICON" => "/images/icon.gif",
];
?>
```

**3. Шаблон компонента:**

```php
<?php
// /local/components/custom/complex.component/templates/.default/template.php
if (!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED !== true) die();

/** @var array $arParams */
/** @var array $arResult */
/** @global CMain $APPLICATION */
/** @global CUser $USER */
/** @global CDatabase $DB */
/** @var CBitrixComponentTemplate $this */
/** @var string $templateName */
/** @var string $templateFile */
/** @var string $templateFolder */
/** @var string $componentPath */
/** @var CBitrixComponent $component */

$this->setFrameMode(true);
?>

<div class="custom-component">
    <?php if (!empty($arResult["ITEMS"])): ?>
        <div class="component-title">
            <h2>Наши элементы (<?= count($arResult["ITEMS"]) ?>)</h2>
        </div>
        
        <div class="items-list">
            <?php foreach ($arResult["ITEMS"] as $item): ?>
                <div class="item">
                    <h3>
                        <a href="<?= $item["DETAIL_PAGE_URL"] ?>">
                            <?= $item["NAME"] ?>
                        </a>
                    </h3>
                    <?php if ($item["PREVIEW_TEXT"]): ?>
                        <p><?= $item["PREVIEW_TEXT"] ?></p>
                    <?php endif; ?>
                </div>
            <?php endforeach; ?>
        </div>
        
        <?php if ($arResult["NAV_STRING"]): ?>
            <div class="navigation">
                <?= $arResult["NAV_STRING"] ?>
            </div>
        <?php endif; ?>
        
    <?php else: ?>
        <div class="alert alert-info">
            Элементы не найдены
        </div>
    <?php endif; ?>
</div>
```

#### **Лучшие практики разработки:**

1. **Используйте неймспейсы** для избежания конфликтов
2. **Реализуйте кеширование** для повышения производительности
3. **Обрабатывайте ошибки** и исключительные ситуации
4. **Разделяйте логику и представление**
5. **Используйте D7 (API нового поколения)** для новых проектов
6. **Создавайте документацию** к вашим компонентам

---

### **Итог**

Освоение модулей и компонентов позволяет:
- **Создавать переиспользуемый код**
- **Расширять функциональность системы** без нарушения ядра
- **Строить масштабируемую архитектуру**
- **Эффективно работать в команде**


---

## **Практическое задание: Создание модуля "Список сотрудников"**

### **Цель задания:**
Создать кастомный модуль с компонентом, который выводит список сотрудников с фотографиями и контактной информацией.

---

## **Шаг 1: Подготовка структуры модуля**

### **Что нужно сделать:**
Создайте структуру папок для нового модуля.

### **Подробное объяснение:**
Модуль в Битрикс — это отдельная функциональность, которую можно включать/выключать. Мы создадим модуль `company.staff`.

### **Действия:**
1. Перейдите в папку `/local/modules/`
2. Создайте папку `company.staff`
3. Внутри создайте структуру:
```
/local/modules/company.staff/
    /install/
        /components/
        /admin/
        /lang/
            /ru/
        version.php
        index.php
    /include.php
```

### **Код для файлов:**

**`/local/modules/company.staff/install/version.php`**
```php
<?php
$arModuleVersion = [
    "VERSION" => "1.0.0",
    "VERSION_DATE" => "2024-01-15 00:00:00"
];
?>
```

**`/local/modules/company.staff/include.php`**
```php
<?php
// Простой файл-заглушка, может содержать вспомогательные функции
?>
```

---

## **Шаг 2: Создание установщика модуля**

### **Что нужно сделать:**
Создать файл, который будет устанавливать модуль в систему.

### **Подробное объяснение:**
При установке модуль регистрируется в системе, создаются необходимые таблицы в БД, добавляются компоненты.

### **Код:**

**`/local/modules/company.staff/install/index.php`**
```php
<?php
// Защита от прямого доступа
if (!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED !== true) {
    die();
}

// Класс модуля должен называться как модуль + CModule
class company_staff extends CModule
{
    // Обязательные переменные
    public $MODULE_ID = "company.staff";
    public $MODULE_VERSION;
    public $MODULE_VERSION_DATE;
    public $MODULE_NAME;
    public $MODULE_DESCRIPTION;
    public $MODULE_GROUP_RIGHTS = "N";

    public function __construct()
    {
        // Читаем версию из version.php
        $arModuleVersion = [];
        include(__DIR__ . "/version.php");
        
        $this->MODULE_VERSION = $arModuleVersion["VERSION"];
        $this->MODULE_VERSION_DATE = $arModuleVersion["VERSION_DATE"];
        $this->MODULE_NAME = "Модуль сотрудников компании";
        $this->MODULE_DESCRIPTION = "Управление списком сотрудников компании";
    }

    // Функция установки модуля
    public function DoInstall()
    {
        // Регистрируем модуль в системе
        RegisterModule($this->MODULE_ID);
        
        // Копируем компоненты
        $this->InstallComponents();
        
        // Показываем сообщение об успехе
        echo "Модуль успешно установлен!";
    }

    // Функция удаления модуля
    public function DoUninstall()
    {
        // Удаляем компоненты
        $this->UnInstallComponents();
        
        // Удаляем модуль из системы
        UnRegisterModule($this->MODULE_ID);
        
        // Показываем сообщение об успехе
        echo "Модуль успешно удален!";
    }

    // Установка компонентов
    public function InstallComponents()
    {
        // Копируем наш компонент в систему
        CopyDirFiles(
            $_SERVER["DOCUMENT_ROOT"] . "/local/modules/company.staff/install/components",
            $_SERVER["DOCUMENT_ROOT"] . "/local/components",
            true, // перезаписывать существующие
            true  // рекурсивно
        );
    }

    // Удаление компонентов
    public function UnInstallComponents()
    {
        // Удаляем папку с компонентом
        DeleteDirFilesEx("/local/components/company/staff.list");
    }
}
?>
```

---

## **Шаг 3: Создание компонента "Список сотрудников"**

### **Что нужно сделать:**
Создать компонент, который будет выводить список сотрудников.

### **Подробное объяснение:**
Компонент состоит из нескольких файлов:
- `.description.php` - описание компонента
- `.parameters.php` - параметры компонента
- `component.php` - основная логика
- Шаблон для отображения

### **Структура компонента:**
```
/local/modules/company.staff/install/components/
    /company/
        /staff.list/
            /.description.php
            /.parameters.php
            /component.php
            /templates/
                /.default/
                    /template.php
                    /style.css
```

### **Код для компонента:**

**`/.description.php`**
```php
<?php
if (!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED !== true) {
    die();
}

$arComponentDescription = [
    "NAME" => "Список сотрудников",
    "DESCRIPTION" => "Выводит красивый список сотрудников компании",
    "PATH" => [
        "ID" => "company",
        "NAME" => "Компания"
    ],
    "ICON" => "/images/icon.gif",
];
?>
```

**`/.parameters.php`**
```php
<?php
if (!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED !== true) {
    die();
}

$arComponentParameters = [
    "PARAMETERS" => [
        "COUNT" => [
            "NAME" => "Количество сотрудников на странице",
            "TYPE" => "STRING",
            "DEFAULT" => "10",
            "PARENT" => "BASE",
        ],
        "SHOW_PHOTO" => [
            "NAME" => "Показывать фотографии",
            "TYPE" => "CHECKBOX",
            "DEFAULT" => "Y",
            "PARENT" => "BASE",
        ],
        "CACHE_TIME" => [
            "DEFAULT" => 3600,
        ],
    ],
];
?>
```

**`/component.php`**
```php
<?php
if (!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED !== true) {
    die();
}

// Данные сотрудников (в реальном проекте будут из БД)
$arResult["STAFF"] = [
    [
        "NAME" => "Иван Петров",
        "POSITION" => "Директор",
        "EMAIL" => "ivan@company.com",
        "PHONE" => "+7 (495) 123-45-67",
        "PHOTO" => "/upload/staff/ivan.jpg",
        "DEPARTMENT" => "Руководство"
    ],
    [
        "NAME" => "Мария Сидорова",
        "POSITION" => "Менеджер проектов",
        "EMAIL" => "maria@company.com",
        "PHONE" => "+7 (495) 123-45-68",
        "PHOTO" => "/upload/staff/maria.jpg",
        "DEPARTMENT" => "Проектный отдел"
    ],
    [
        "NAME" => "Алексей Козлов",
        "POSITION" => "Разработчик",
        "EMAIL" => "alexey@company.com",
        "PHONE" => "+7 (495) 123-45-69",
        "PHOTO" => "/upload/staff/alexey.jpg",
        "DEPARTMENT" => "IT отдел"
    ],
];

// Обрабатываем параметры
$arParams["COUNT"] = intval($arParams["COUNT"]) ?: 10;
$arParams["SHOW_PHOTO"] = $arParams["SHOW_PHOTO"] !== "N";

// Ограничиваем количество выводимых сотрудников
$arResult["STAFF"] = array_slice($arResult["STAFF"], 0, $arParams["COUNT"]);

// Подключаем шаблон
$this->IncludeComponentTemplate();
?>
```

---

## **Шаг 4: Создание шаблона компонента**

### **Что нужно сделать:**
Создать красивый шаблон для отображения списка сотрудников.

### **Код:**

**`/templates/.default/template.php`**
```php
<?php
if (!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED !== true) {
    die();
}

/** @var array $arParams */
/** @var array $arResult */
/** @global CMain $APPLICATION */

$this->setFrameMode(true);
?>

<div class="staff-list">
    <h2>Наша команда</h2>
    
    <?php if (!empty($arResult["STAFF"])): ?>
        <div class="staff-grid">
            <?php foreach ($arResult["STAFF"] as $staff): ?>
                <div class="staff-card">
                    <?php if ($arParams["SHOW_PHOTO"] && !empty($staff["PHOTO"])): ?>
                        <div class="staff-photo">
                            <img src="<?= $staff["PHOTO"] ?>" alt="<?= htmlspecialchars($staff["NAME"]) ?>">
                        </div>
                    <?php endif; ?>
                    
                    <div class="staff-info">
                        <h3 class="staff-name"><?= htmlspecialchars($staff["NAME"]) ?></h3>
                        <p class="staff-position"><?= htmlspecialchars($staff["POSITION"]) ?></p>
                        <p class="staff-department"><?= htmlspecialchars($staff["DEPARTMENT"]) ?></p>
                        
                        <div class="staff-contacts">
                            <?php if (!empty($staff["EMAIL"])): ?>
                                <p class="staff-email">
                                    <strong>Email:</strong> 
                                    <a href="mailto:<?= htmlspecialchars($staff["EMAIL"]) ?>">
                                        <?= htmlspecialchars($staff["EMAIL"]) ?>
                                    </a>
                                </p>
                            <?php endif; ?>
                            
                            <?php if (!empty($staff["PHONE"])): ?>
                                <p class="staff-phone">
                                    <strong>Телефон:</strong> 
                                    <a href="tel:<?= htmlspecialchars($staff["PHONE"]) ?>">
                                        <?= htmlspecialchars($staff["PHONE"]) ?>
                                    </a>
                                </p>
                            <?php endif; ?>
                        </div>
                    </div>
                </div>
            <?php endforeach; ?>
        </div>
    <?php else: ?>
        <p class="staff-empty">Сотрудники не найдены</p>
    <?php endif; ?>
</div>
```

**`/templates/.default/style.css`**
```css
.staff-list {
    max-width: 1200px;
    margin: 0 auto;
    padding: 20px;
}

.staff-list h2 {
    text-align: center;
    color: #333;
    margin-bottom: 30px;
}

.staff-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 20px;
}

.staff-card {
    background: #fff;
    border: 1px solid #ddd;
    border-radius: 8px;
    padding: 20px;
    box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    transition: transform 0.3s ease;
}

.staff-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 5px 15px rgba(0,0,0,0.2);
}

.staff-photo {
    text-align: center;
    margin-bottom: 15px;
}

.staff-photo img {
    width: 150px;
    height: 150px;
    border-radius: 50%;
    object-fit: cover;
    border: 3px solid #f0f0f0;
}

.staff-name {
    color: #2c3e50;
    margin: 0 0 5px 0;
    font-size: 1.2em;
}

.staff-position {
    color: #e74c3c;
    font-weight: bold;
    margin: 0 0 5px 0;
}

.staff-department {
    color: #7f8c8d;
    font-style: italic;
    margin: 0 0 15px 0;
}

.staff-contacts p {
    margin: 5px 0;
    font-size: 0.9em;
}

.staff-email a,
.staff-phone a {
    color: #3498db;
    text-decoration: none;
}

.staff-email a:hover,
.staff-phone a:hover {
    text-decoration: underline;
}

.staff-empty {
    text-align: center;
    color: #7f8c8d;
    font-style: italic;
    padding: 40px;
}
```

---

## **Шаг 5: Установка и использование**

### **Установка модуля:**

1. Перейдите в админку Битрикс: `/bitrix/admin/`
2. Перейдите в "Marketplace" → "Установленные решения"
3. Найдите модуль "company.staff" в списке локальных модулей
4. Нажмите "Установить"

### **Использование компонента:**

**Способ 1: Через визуальный редактор**
1. Откройте нужную страницу в визуальном редакторе
2. Добавьте компонент через панель вставки
3. Найдите "Список сотрудников" в разделе "Компания"

**Способ 2: Прямое подключение в template.php**
```php
<?php
// В файле шаблона страницы
$APPLICATION->IncludeComponent(
    "company:staff.list",
    "",
    [
        "COUNT" => 6,
        "SHOW_PHOTO" => "Y"
    ],
    false
);
?>
```

---

## **Что вы узнали и практиковали:**

✅ **Структура модуля** - как организованы файлы модуля  
✅ **Установщик модуля** - как модуль регистрируется в системе  
✅ **Компоненты** - создание переиспользуемых визуальных элементов  
✅ **Параметры компонентов** - настройка поведения через параметры  
✅ **Шаблоны** - разделение логики и представления  
✅ **Стилизация** - создание красивого интерфейса  

---

## **Дополнительные задания для углубления:**

1. **Добавьте базу данных**: Создайте таблицу для сотрудников и получайте данные из БД
2. **Добавьте админку**: Создайте страницу в админке для управления сотрудниками
3. **Добавьте сортировку**: Реализуйте параметры сортировки по отделам/должностям
4. **Сделайте детальную страницу**: Создайте компонент для просмотра подробной информации о сотруднике