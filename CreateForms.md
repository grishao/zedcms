# Создание форм #
Формы создаются с помощью класса Z\_Form\_ProcessForm. Перед вызовом необходимо задать шаблон формы в виде массива.

# Задание шаблона формы #
Шаблон формы определяет какие поля будут в форме и как они будут работать. Шаблон задается в виде массива, элементами которого являются описания каждого поля:

```
$form_template = array(

/**************************************************************
* PICTURE
*/
    "picture" => array(
        "label" => "Логотип",
        "type" => "upload_file",
        "filter_type" => "exact",
        "table_width" => "auto",
        "attributes" => "style=\"width: 100px;\"",
        "params" => array(
            "error_after" => true,
            "noauto_complete" => true
        ),
        "validators" => array(
        ),
        "normalizers" => array(
        ),
        "required" => true,
        "multiple" => false
    ),
/**************************************************************
* INPUT
*/
    "input_simple" => array(
        "label" => "Название",
        "type" => "input",
        "multiple" => false,
        "attributes" => "class=\"b-form-add-object__input_name\"",
        "params" => array(
                "inner_label" => "Введите название",
                "add_more_label" => "Добавить еще input",
                "error_after" => true,
                "examples" => "<br>
                <i>
                Примеры: ИП Иванов
                </i>"
            ),
        "normalizers" => array("trim", "double_spaces"),
        "validators" => array(
            "max_length" => 70
        ),
        "required" => true,
        "readonly" => false 
    ),
/**************************************************************
* INPUT
*/
    "input_simple" => array(
        "label" => "Адрес",
        "type" => "input",
        "multiple" => true,
        "attributes" => "class=\"b-form-add-object__input_address\"",
        "params" => array(
                "inner_label" => "Заполните адрес",
                "add_more_label" => "Добавить еще адрес",
                "error_after" => true,
                "examples" => "<br>
                <i>
                Примеры: г. Москва ул. Минская 1.
                </i>"
            ),
        "normalizers" => array("trim", "double_spaces"),
        "validators" => array(
            "max_length" => 70
        ),
        "required" => true,
        "readonly" => false 
    )
);
```