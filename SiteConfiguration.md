# Конфигурация сайта #
Конфигурация сайта необходима для установки пароля и параметров запуска БД, для определения необходимости вывода счетчиков, для настройки определенных [компонентов](Components.md) сайта и прочих настроек.

# Местоположение конфигурации #
Конфигурационный файл находится по адресу **/configs/config.ini** относительно папки сайта. Таким образом, конфигурация задается в пределах одного сайта.

# Внутренняя организация #
Рассмотрим на примере конфигурации:

```
[default]
    robots.txt = "robots_dev.txt"
    counters.enabled=0
    
    ; обязательные поля
    layout.main_template="main"
    components.path="components/"
    upload.default="upload/"
    mail.templates_dir="mail_templates/"
    sms.templates_dir="sms_templates/"

    db.server="localhost"
    db.user="db_user1"
    db.password="mypassword"
    db.db="db_base1"

    ; необязательные поля    
    ; Для репорта ошибок
    admin.email="myemail@gmail.com"
    
    redirect.url.template="/redirect.php?url=%URL%%PARAMS%"
    
    pages.404 = "/404.php"
    
    yandex_map.key = ""
    yandex_map.default_zoom = 9
    yandex_map.default_center = "55.76, 37.64"
    yandex_map.default_behaviors = "scrollZoom,drag,multiTouch,dblClickZoom,rightMouseButtonMagnifier"
    yandex_map.default_controls = "mapTools,scaleLine,trafficControl,typeSelector,zoomControl"
    yandex_map.default_control.typeSelector = "yandex#map,yandex#satellite,yandex#hybrid"
    yandex_map.default_control.mapTools = "drag,ruler"

[www.site1.com]
    robots.txt = "robots_prod.txt"
    counters.enabled=1
    db.server="localhost"
    db.user="site1_com"
    db.password="AdErfFSIi02K"
    db.db="site1_com"
```

Как видно из примера в конфигурации есть группы, которые обозначаются в прямоугольных скобках **`[]`** и есть поля, формат которых **переменная = значение**. Переменная, потому что конфигурацию можно менять налету во время выполнения скрипта в любом месте.

# Что означает группа внутри конфигурации #
Группы необходимы для задания разных конфигураций для разных доменов или поддоменов сайта. Группа **default** выполняется всегда, затем выполняется группа одноименная домену, на котором произошел запрос, если группа с названием домена не найдена, работа продолжится с **default** конфигурацией. Если же группа найдена, то те поля, которые в ней заданы будут либо добавлены в итоговую конфигурацию, либо заменят существующие значения из группы **default**.

# Как использовать группы #
Примером использования групп может быть трехландшафтная система. Если у вас имеется продуктивный сайт **www.site1.com**, вы можете создать к нему еще два сайта: **dev.site1.com** (для разработки) и **test.site1.com**(для тестирования) и для каждого из них создать свою конфигурацию, причем общие значения конфигурации прописать в группе **default** для компактности и прозрачности.

В итоге у вас будет физически три сайта, в трех папках **www.site1.com**, **dev.site1.com** и **test.site1.com**. Файлы конфигурации при транспорте кода из одной папки в другую будут одинаковыми, но фреймворк будет понимать на каком поддомене он запущен и будет выбирать соответствущую конфигурацию.

[Подробнее об архитектуре трехландшафной системы транспорта](ThreeLandscape.md)

Количество дополнительных сайтов не ограничивается тремя, можно делать их сколько угодно, это актуально, когда ведется несколько разработок над разными частями сайта, и каждая из них ведется в своей папке/сайте.

# Доступ к конфигурации #
Конфигурация после запуска фреймворка автоматически размещается в глобальной переменной класса ([библиотеки](Libraries.md)):

**`Z_Application::$config`**

Из вышеуказанного примера конфигурации, получится следующее:
```
Z_Applaction::$config = array(
    "robots.txt" => "robots_dev.txt"
    "counters.enabled" => 0
    "layout.main_template => "main"
    "components.path" => "components/"
    "upload.default" => "upload/"
    "mail.templates_dir" => "mail_templates/"
    "sms.templates_dir" => "sms_templates/"
    "db.server" => "localhost"
    "db.user" => "db_user1"
    "db.password" => "mypassword"
    "db.db" => "db_base1"
    "admin.email" => "myemail@gmail.com"
    "redirect.url.template" => "/redirect.php?url=%URL%%PARAMS%"
    "pages.404" => "/404.php"
    "yandex_map.key" = ""
    "yandex_map.default_zoom" => 9
    "yandex_map.default_center" => "55.76, 37.64"
    "yandex_map.default_behaviors" => "scrollZoom,drag,multiTouch,dblClickZoom,rightMouseButtonMagnifier"
    "yandex_map.default_controls" => "mapTools,scaleLine,trafficControl,typeSelector,zoomControl"
    "yandex_map.default_control.typeSelector" => "yandex#map,yandex#satellite,yandex#hybrid"
    "yandex_map.default_control.mapTools" => "drag,ruler"
);
```

Таким образом, конфигурация доступна в любом месте в любое время сразу после запуска фреймворка.

# Правила наименования #
Каждое поле конфигурации в ZedCMS необходимо делать с названием разбитыми точками. Например, **db.user**, это необходимо для легкого понимания конфигурации разработчиками, а так же для предотвращения "пересечения" различных конфигураций. Никаких особых ограничений в наименовании нет.

# Обязательные поля #
  * layout.main\_template="main"
  * components.path="components/"
  * upload.default="upload/"
  * mail.templates\_dir="mail\_templates/"
  * sms.templates\_dir="sms\_templates/"
  * db.server="localhost"
  * db.user="db\_user1"
  * db.password="mypassword"
  * db.db="db\_base1"

Из названий полей понятно, что они означают.

**Размещение компонентов, шаблонов email и SMS изменять не рекомендуется, для унифицированности всех внедрений фреймворка. В дальнейшем эти переменные будут заменены на константы внутри фреймворка.**