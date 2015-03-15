# Принцип работы ZedCMS #

В данном разделе рассмотрен путь программы от запроса клиента до вывода данных.
Для примера возьмем структуру папок из раздела [Организация файлов и папок](FilesLocations.md) и рассмотрим на примере запроса к сайту **www.site1.com**, который располагается в папке **/myuser/www.site1.com**, а ZedCMS при этом находится в папке **/myuser/z.common**

# Единая точка входа #
При настройке веб сервера для www.site1.com домашним каталогом указывается папка **/myuser/www.site1.com/public** в которой размешены все публично доступные файлы. И в этой папке находится **index.php**, который внутри себя запускает фреймворк ZedCMS из папки **/myuser/z.common**.

Так же в этой папке размещен .htaccess со следующим содержимым:

```
SetEnvIf User-Agent "MSIE 6" no-gzip dont-vary

RewriteEngine On
RewriteBase /
RewriteRule ^root/(.*)$ $1 [NC,L]

RewriteCond %{REQUEST_FILENAME} -s [OR]
RewriteCond %{REQUEST_FILENAME} -l [OR]
RewriteCond %{REQUEST_FILENAME} -d
RewriteRule ^.*$ - [NC,L]

RewriteRule ^.*$ index.php [NC,L]

php_flag magic_quotes_gpc off
php_flag register_globals off
```

В итоге получается, что все запросы от клиента приходят в **index.php**, который лежит в папке **/myuser/www.site1.com/public**, за исключением того, если запрошенный путь не ведет к существующему файлу в папке или подпапке **/myuser/www.site1.com/public**, тогда он выводится напрямую без запуска фреймворка.

Таким образом, в папке **public** размещаются все CSS, JS и другие необходимые файлы доступные напрямую, без задействования фреймворка ZedCMS (или иными словами вся статика).

# Доступ к страницам #
В папке **public** нашего сайта существуюет специальная папка **root**, которая хранит страницы нашего сайта в виде отдельных файлов. При этом прямой доступ к этим файлам запрещен файлом .htaccess, который указан выше. Вместо этого эти файлы исполняет фреймворк ZedCMS.

# Путь запроса #
Итак представим, что пользователь запросил главную страницу **www.site1.com/**.

![![](http://zedcms.googlecode.com/files/request_path.jpg)](http://zedcms.googlecode.com/files/request_path.jpg)

Кратко о пути запроса:
  1. **index.php** в папке **/public** нашего сайта (единая точка входа)
  1. **index.php** в папке **z.common** (запуск фреймворка, дальнейшие вызовы производит сам фреймворк ZedCMS)
  1. **index.php** в папке **/public/root** (на этом шаге весь контент сгенерированный index.php запоминается, но не выводится сразу)
  1. Вызов основного шаблона из папки **/myuser/www.site1.com/templates/**
  1. Вставка всего содержимого полученного на шаге 3 в шаблон из предыдущего шага
  1. Отдача заполненного шаблона клиенту.

В итоге мы получим данные из страницы **/myuser/www.site1.com/public/index.php** обернутые в выбранный [шаблон](MainTemplates.md) (настраивается в [конфигурации сайта](SiteConfiguration.md)).

Обратите внимание, что фреймворк автоматически выбрал **index.php** на шаге 3, хотя в запросе клиента запрашиваемый файл не был явно указан.

Теперь рассмотрим еще два запроса:
  * **www.site1.com/contacts.php**
  * **www.site1.com/contacts/**

Все шаги вызовов будут такими же, за исключением 3-го шага, где для первого запроса будет выбран файл **/myuser/www.site1.com/public/root/contacts.php**, а для второго запроса будет выбран файл **/myuser/www.site1.com/public/root/contacts/index.php**.

Как видно из последнего примера, мы можем с легкостью создавать "папки" на нашем сайте и размещать в них страницы.

# URL Rewrite #
Часто на сайте необходимо организовать внутреннюю переадресацию, например, при запросе **/article/article\_name/455**, вызвать файл из **/public/root/articles.php**. Для этого в ZedCMS существует свой [urlrewrite](UrlRewrite.md), который располагается в папке сайта внутри папки **configs**.

Рассмотрим туже цепочку шагов, которая указана выше. Urlrewrite срабатывает между 2-м и 3-м шагом. Таким образом, на третьем шаге мы получаем преобразованный файл для запуска фреймворком. Подробнее в [разделе Urlrewrite](UrlRewrite.md).

# Движение данных во фреймворке с шаблоном #
В общем случае, передаваемые данные могут быть не только HTML, но и JS JSON или любая другая строка.

![http://zedcms.googlecode.com/files/data_moving_with_template.jpg](http://zedcms.googlecode.com/files/data_moving_with_template.jpg)

# Движение данных во фреймворке с отключенным шаблоном #
![http://zedcms.googlecode.com/files/data_moving_wo_template.jpg](http://zedcms.googlecode.com/files/data_moving_wo_template.jpg)