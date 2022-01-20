# Twig components extension

[![Latest Version on Packagist](https://img.shields.io/packagist/v/performing/twig-components.svg?style=flat-square)](https://packagist.org/packages/performing/twig-components)
[![GitHub Tests Action Status](https://img.shields.io/github/workflow/status/giorgiopogliani/twig-components/Tests)](https://github.com/giorgiopogliani/twig-components/actions?query=workflow%3ATests+branch%3Amaster)
[![Total Downloads](https://img.shields.io/packagist/dt/performing/twig-components.svg?style=flat-square)](https://packagist.org/packages/performing/twig-components)

This is a php package for automatically create twig components as tags. This is highly inspired from laravel blade components.  

## Installation

You can install the package via composer:

```bash
composer require performing/twig-components
```

## Configuration

This package should work anywhere where twig is available.

```php
/** @var \Twig\Environment $twig */

use Performing\TwigComponents\Setup;

Setup::init($twig, '/relative/directory/to/components');
```

To enable the package just pass your twig environment object to the function and specify your components folder relative to your twig templates folder.

### Example Craft MS

In Craft CMS you should do something like this. The `if` statement ensure you don't get `'Unable to register extension "..." as extensions have already been initialized'` as error.
```php
// Module.php
if (Craft::$app->request->getIsSiteRequest()) {    
    Event::on(
        Plugins::class,
        Plugins::EVENT_AFTER_LOAD_PLUGINS,
        function (Event $event) {
            $twig = Craft::$app->getView()->getTwig();
            \Performing\TwigComponents\Setup::init($twig, '/components');
        }
    );
}
```


## Usage

The components are just twig templates in a folder of your chioce (e.g. `components`) and can be used anywhere in your twig templates. The slot variable is any content you will add between the opening and the close tag.

```html
{# /components/button.twig #}
<button>
  {{ slot }}
</button>
```

### Custom syntax

To reach a component you need to use custom tag `x` followed by a `:`  and the filename of your component.
```twig
{# /index.twig #}
{% x:button %}
    <strong>Click me</strong>
{% endx %}
```

You can also pass any params like you would using an `include`. The benefit is that you will have the powerful `attributes` variable to merge attributes or to change you component behaviour.
```twig
{# /index.twig #}
{% x:button with {'class' : 'text-white'} %}
    <strong>Click me</strong>
{% endx %}

{# /components/button.twig #}
<button {{ attributes.merge({ class: 'rounded px-4' }) }}>
    {{ slot }}
</button>

{# Rendered #}
<button class="text-white rounded-md px-4 py-2">
    <strong>Click me</strong>
</button>
```

To reach components that are in **sub-folders** you can use dot-notation syntax.
```twig
{# /components/button/primary.twig #}
<button>
    {{ slot }}
</button>

{# /index.twig #}
{% x:button.primary %}
    <strong>Click me</strong>
{% endx %}
```

### Html lihe syntax
The same behaviour can be obtained with a special html syntax. The previus component example can alse be used in this way.

```twig
{# /index.twig #}
<x-button class='bg-blue-600'>
  <span class="text-lg">Click here!</span>
</x-button>
```

### Named slots
```twig
{# /components/card.twig #}
<div {{ attributes.class('bg-white shadow p-3 rounded') }}>
    <h2 {{ title.attributes.class('font-bold') }}>
        {{ title }}
    </h2>
    <div>
        {{ body }}
    </div>
</<div>

{# /index.twig #}
<x-card>
    <x-slot name="title" class="text-2xl">Titile</x-slot>
    <x-slot name="body" >Body text</x-slot>
</x-card>
```

Also with the standard syntax.

```twig
{# /index.twig #}
{% x:card %}
    {% slot:title with {class: "text-2xl"}
        Titile
    {% endslot %}
    {% slot:body
        Titile
    {% endslot %}
{% endx %}
```

### Attributes
You can pass any attribute to the component in different ways. To interprate the content as twig you need to prepend the attribute name with a `:` but it works also in other ways.

```twig
<x-button 
    :any="'evaluate' ~ 'twig'"
    other="{{'this' ~ 'works' ~ 'too'}}" 
    another="or this"
    this="{{'this' ~ 'does'}}{{ 'not work' }}"
>
    Submit
</x-button>
```


### Twig Namespaces

In addition to the specified directory, you can also reference components from a twig namespace by prepending the namespace and a `:` to the component name. With a namespace defined like so:

```php
// register namespace with twig template loader
$loader->addPath(__DIR__ . '/some/other/dir', 'ns');
```

components can be included with the following:

```twig
{% x:ns:button with {class:'bg-blue-600'} %}
  <span class="text-lg">Click here!</span>
{% endx %}

{# or #}

<x-ns:button class='bg-blue-600'>
  <span class="text-lg">Click here!</span>
</x-ns:button>
```

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.


## Testing

```bash
composer test
```

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
