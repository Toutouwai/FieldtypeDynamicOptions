# Dynamic Options

A module for ProcessWire CMS/CMF. A Fieldtype for dynamic options that are generated at runtime via a hook.

## Configuration

### Inputfield type

You can choose from a range of inputfield types on the Details tab of a Dynamic Options field. Your field will return a string or an array depending on if the selected input field type is for  "single item selection" or "multiple item selection".

### Maximum number of items

You can define a maximum number of items the field is allowed to contain. The core inputfields supported by this module will become disabled once the limit is reached. This option is only applicable if you have selected an inputfield type that is for multiple item selection.

### Format as Pagefile/Pageimage object(s)

If the field will store paths/URLs to Pagefiles/Pageimages then you can enable this option to have the formatted value be a Pagefile/Pageimage object for "single" fields or an array of Pagefile/Pageimage objects for "multiple" fields.

There is a related [Select Images](https://github.com/Toutouwai/InputfieldSelectImages) inputfield module that allows you to visually select image thumbnails.

### Defining selectable options

Selectable options for a Dynamic Options field should be set in a `FieldtypeDynamicOptions::getSelectableOptions` hook in /site/ready.php. The hook should return an array of options as 'value' => 'label'.

An example hook is shown on the Details tab of a Dynamic Options field:

```php
$wire->addHookAfter('FieldtypeDynamicOptions::getSelectableOptions', function(HookEvent $event) {
    // The page being edited
    $page = $event->arguments(0);
    // The Dynamic Options field
    $field = $event->arguments(1);
    if($field->name === 'your_field_name') {
        $event->return = [
            'red' => 'Red',
            'green' => 'Green',
            'blue' => 'Blue',
        ];
    }
});
```

## Formatted value

If a Dynamic Options field uses a "single" input type then its formatted value is a string, and if it uses a "multiple" input type then its formatted value is an array. The unformatted value of a Dynamic Options field is always an array.

Also see the Configuration section above for description of an option to have the formatted value be Pagefile/Pageimage object(s). 

## Examples of possible uses

![dynamic-options](https://user-images.githubusercontent.com/1538852/112603696-3a49c700-8e7a-11eb-8e94-60f9e7f97317.png)

```php
$wire->addHookAfter('FieldtypeDynamicOptions::getSelectableOptions', function(HookEvent $event) {
    // The page being edited
    $page = $event->arguments(0);
    // The Dynamic Options field
    $field = $event->arguments(1);

    // Select from the "files" field on the page
    if($field->name === 'select_files') {
        $options = [];
        foreach($page->files as $file) {
            // Value is basename, label is description if one exists
            $options[$file->basename] = $file->get('description|basename');
        }
        $event->return = $options;
    }

    // Select from files in a folder
    if($field->name === 'select_folder_files') {
        $options = [];
        $path = $event->wire()->config->paths->root . 'my-folder/';
        $files = $event->wire()->files->find($path);
        foreach($files as $file) {
            // Value is full path, label is basename
            $options[$file] = str_replace($path, '', $file);
        }
        $event->return = $options;
    }

    // Select from non-system templates
    if($field->name === 'select_template') {
        $options = [];
        foreach($event->wire()->templates as $template) {
            if($template->flags & Template::flagSystem) continue;
            $options[$template->id] = $template->name;
        }
        $event->return = $options;
    }

    // Select from non-system fields
    if($field->name === 'select_field') {
        $options = [];
        foreach($event->wire()->fields as $field) {
            if($field->flags & Field::flagSystem) continue;
            $options[$field->id] = $field->name;
        }
        $event->return = $options;
    }

    // Select from FormBuilder forms
    if($field->name === 'select_formbuilder_form') {
        $form_names = $event->wire()->forms->getFormNames();
        // Use form names as both keys and values
        $event->return = array_combine($form_names, $form_names);
    }

});
```
