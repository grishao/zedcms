# Общий шаблон #

Шаблон используется для вывода общих данных для всех страниц сайта. Тут могут быть стандарные теги `<html>` `<head>` `<body>`, а так же компоненты, и другие динамические части сайта. То есть шаблон - это обычный PHP скрипт со специальными метками, в которые вставляется "начинка" подготовленная на этапе обработки страницы.

Фреймворк ZedCMS сначала вызывает обработку [страницы](Pages.md) сайта, запоминает ее вывод и другие данные, а затем отрисовывает шаблон с данными страницы. Коненый результат отправляется клиенту.

# Управление шаблонами, конфигурация #
Общий шаблон сайта задается в [конфигурации сайта](SiteConfiguration.md) с помощью поля **layout.main\_template** которое должно быть равно названию шаблона без **.phtml**. Во время выполнения страницы или в любом другом месте, название шаблона можно изменить или вовсе отключить шаблон.

Отключение шаблона (в любом месте выполнения, но лучше в начале страницы, для чистоты кода):
```
define(Z_SUPPRESS_TEMPLATE, true);
```

При отключении шаблона страница выводится "как есть". Это, например, используется для вывода данных при Ajax запросах. В странице достаточно сделать подобный код в конце обработки:
```
echo json_encode($mydata_to_send);
```

Для изменения шаблона:
```
Z_Application::$config["layout.main_template"] = "my_another_template";
```

# Размещение шаблонов сайта #
Шаблоны размещаются в папке /templates/ сайта. То есть полный путь будет примерно таким: **/myuser/www.site1.com/templates/**

Файлы шаблонов должны иметь расширение **.phtml**, оно не задается явно в конфигурации, по этому нужно всегда придерживаться этого правила.

# Адаптация шаблона #
Бывает на некоторых страницах сайта необходимо сделать незначительные изменения шаблона сайта, и чтобы не создавать для этого отдельный шаблон с последующим переключением на этих страницах, можно сделать адаптацию шаблона в зависимости от глобальных параметров.

Например, необходимо убрать компонент вывода погоды, на главной странице.
Фрагмент кода главной страницы index.php
```
Z_Application::$globals["index.page.disable.weather"] = true;
```

Фрагмент шаблона сайта main.phtml
```
if(!Z_Application::$globals["index.page.disable.weather"])
   Z_Component::placeComponent("mysite:weather", "side_vertical", array());
```

Теперь на любой странице можно отключить блок погоды в шаблоне одной строчкой.

# Метки шаблона #
В шаблоне указываются специальные метки, в которые затем фреймворк будет вставлять те или иные данные:
  * **Z\_HEAD\_STRING\_MARK** - метка вставки кода динамического заголовка)
  * **Z\_MAIN\_DATA\_MARK** - метка вставки страницы)
  * **Z\_GENERATE\_TIME\_MARK** - метка вставки времени генерации страницы (в сек. с точностью до сотых прим.: 0.13)

Метки - это PHP константы. И ставятся они следующим образом:
```
// ... какой то код
<?=Z_HEAD_STRING_MARK?>
// ... какой то код
<?=Z_MAIN_DATA_MARK?>
// ... какой то код
<?=Z_GENERATE_TIME_MARK?>
```

Последовательность меток может быть любая.

# Пример шаблона #
```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html>
<head id="head">
    <script type="text/javascript" src="/js/jquery-1.3.2.min.js"></script>
    <script type="text/javascript" src="/js/jquery.scrollTo-min.js"></script>
    <link href="/css/main.css" type="text/css" rel="stylesheet" />
    <?=Z_HEAD_STRING_MARK?>
    <!--[if lt IE 9]>
    <link href="/css/main_ie.css" type="text/css" rel="stylesheet" />
    <![endif]-->
    <!--[if lte IE 6]>
    <link href="/css/main_ie6.css" type="text/css" rel="stylesheet" />
    <![endif]-->
    <title><?=htmlspecialchars(Z_Application::$seoComponents["title"])?></title>
    <meta name="robots" content="<?=Z_Application::$seoComponents["robots"] ? Z_Application::$seoComponents["robots"] : "index, follow"?>" />
    <meta name="keywords" content="<?=htmlspecialchars(Z_Application::$seoComponents["keywords"])?>" />
    <meta name="description" content="<?=htmlspecialchars(Z_Application::$seoComponents["description"])?>" />
</head>
<body>
   <div class="b-page__wrapper">
      <div class="b-page">
         <?Z_Component::placeComponent("z:breadcrumbs","",array());?>
         <?=Z_MAIN_DATA_MARK?>
         <?Z_Component::placeComponent("mysite:ads.banners","partners-banners",array("type" => "banner_partners"));?>
      </div>
   <div>
   <div class=\"b-footer\">
      <span>Страница сгенерирована за <?=Z_GENERATE_TIME_MARK?> с.</span>
      <?if(Z_Application::$config["counters.enabled"]):
                    require("counters.phtml");
   </div>
</body>
</html>
```

В данном примере мы выводим стандартную "обертку", в **head** у нас имеются как статические участки, так и динамические. Обратите внимание, как выводятся **SEO мета теги** и **Title** страницы, предварительно обработанные функциями **htmlspecialchars**.

Еще необходимо обратить внимание на то, как выводятся счетчики страниц. В зависимости от [конфигурации сайта](SiteConfiguration.md) счетчики выводятся либо нет.