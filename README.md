# Dynamic Options

A module for ProcessWire CMS/CMF. A Fieldtype for dynamic options that are generated at runtime via a hook.

## Configuration

You can choose from a range of input field types on the Details tab of a Dynamic Options field. Your field will return a string or an array depending on if the selected input field type is for  "single item selection" or "multiple item selection".

Selectable options for a Dynamic Options field should be set in a `FieldtypeDynamicOptions::getSelectableOptions` hook in /site/ready.php. The hook should return an array of options as 'value' => 'label'.

An example hook is shown on the Details tab of a Dynamic Options field:

```php
$wire->addHookAfter('FieldtypeDynamicOptions::getSelectableOptions', function(HookEvent $event) {
    // The page being edited
    $page = $event->arguments(0);
    // The Dynamic Options field
    $field = $event->arguments(1);
    if($field->name === 'dynamic_options') {
        $event->return = array(
            'red' => 'Red',
            'green' => 'Green',
            'blue' => 'Blue',
        );
    }
});
```

## Formatted value

If a Dynamic Options field uses a "single" input type then its formatted value is a string, and if it uses a "multiple" input type then its formatted value is an array. The unformatted value of a Dynamic Options field is always an array.

## Installation

[Install](http://modules.processwire.com/install-uninstall/) the Dynamic Options module.
