# Выборка данных mySQL #

Выбирать данные можно вручную построив запрос, например:
```
global $DB;

$id = 5;

$id = intVal($id);
$result1 = $DB->Query("SELECT * FROM mytable_name WHERE id=$id")->fetchNext();

$name = "Имя"
$name = $DB->escapeString($name);
$result2 = $DB->Query("SELECT * FROM mytable_name WHERE name=\"$name\"")->fetchAll();
```

В данном примере результат **$result1** содержит одну строку которая соответствует **id=5**.
Результат **result2** содержит несколько строк, у которых поле **name** равно **Имя**;

При этом экранирование переменных должно производится в ручную с помощью:
  * **intVal()** - для целых чисел
  * **$DB->escapeString()** для строк

# Создание WHERE условий #
Если необходимо строить сложные запросы с множественными условиями, можно использовать специальный механизм ZedCMS для облегчения генерации **WHERE** условий.

Рассмотрим на примере:
```

$filter = array(
   "id" => 4,
   "ad_type" => "paid",
   "parent_address_id" => 23,
   "object_type" => "pavilion",
   "object_id" => 323,
   "active" => "Y",
   "lat" => "56.232",
   "lng" => "34.232"
);

// Формирование строки WHERE
        $where_filter = $DB->generateWhereFromArray($filter, array(
            "id" => "e_adr.ID",
            "ad_type" => "p_adr.field_138",
            "parent_address_id" => "p_adr.field_149",
            "object_type" => "e_obj.type_id",
            "object_id" => "e_obj.ID",
            "active" => "e_adr.ACTIVE",
            "lat" => "p_adr.field_89",
            "lng" => "p_adr.field_90"
        ), array(
            "id" => array("!=" => "( 1 )", "=" => "(1)"),
            "object_id" => array("!=" => "( 1 )", "=" => "(1)"),
            "ad_type" => array("nopaid" => 7, "paid" => "9", "premium" => 10),
            "object_type" => array(
                    "=pavilion" => "( e_obj.type_id = 19 AND p_adr.field_149 IS NOT NULL )",
                    "=magazin" => "( e_obj.type_id = 19 AND p_adr.field_149 IS NULL )",
                    "rynok" => 16,
                    "tc" => 17,
                    "giper" => 18
                ),
            "lat" => array("=%PATTERN%" => "p_adr.field_89 = %%VALUE%%"),
            "lng" => array("=%PATTERN%" => "p_adr.field_90 = %%VALUE%%"),
            "parent_address_id" => array( "=" => "p_adr.field_149 IS NULL" ),
            "product_categories" => array("%PATTERN%" => "p_adr.field_133 LIKE \"%|%%VALUE%%|%\"",
                              "=" => "(1)"),
            "shosse" => array("%PATTERN%" => "p_adr.field_218 LIKE \"%|%%VALUE%%|%\"",
                              "!%PATTERN%" => "( p_adr.field_218 NOT LIKE \"%|%%VALUE%%|%\" OR p_adr.field_218 IS NULL )",
                              "=" => "(1)",
                              "!" => "(1)"),
            "metro" => array("%PATTERN%" => "p_adr.field_136 LIKE \"%|%%VALUE%%|%\"",
                             "!%PATTERN%" => "( p_adr.field_136 NOT LIKE \"%|%%VALUE%%|%\" OR p_adr.field_136 IS NULL )",
                              "=" => "(1)"),
            "worktime" => array("%PATTERN%" => "p_adr.field_130 LIKE \"%%VALUE%%\"",
                              "=" => "(1)"),
            "sort" => array( "=nopaid" => "(e_adr.SORT > -2000000000 AND e_adr.SORT < -1500000000)",
                             "=nopaidphoto" => "(e_adr.SORT > -1000000000 AND e_adr.SORT < -500000000)",
                             "=nopaidcatalog" => "(e_adr.SORT > -1500000000 AND e_adr.SORT < -1000000000)", 
                             "=nopaidphotocatalog" => "(e_adr.SORT > -500000000 AND e_adr.SORT < 0)",
                             "=paid" => "(e_adr.SORT > 1000000000 AND e_adr.SORT < 1500000000)",
                             "=premium" => "(e_adr.SORT > 1500000000 AND e_adr.SORT < 2000000000)" )
        ));

$result = $DB->Query("SELECT *
   FROM address_table as e_adr
   JOIN address_props_table as p_adr ON p_adr.element_id = e_adr.ID
   JOIN object_table as e_obj ON e_obj.ID = p_adr.field_83
   WHERE $where_filter")->fetchAll();
```