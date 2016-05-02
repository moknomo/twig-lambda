# Twig Lambda
> Lambda expressions for Twig and filters that make use of them

----------------------------------------------------------------

<a name="examples"></a>
## Quick examples

Listing names of all authors ordered by age:
```twig
{% for author in articles|map(=> _.author)|unique|sortBy(=> _.age) %}
    * {{ author.name }}, {{ author.age }}
{% endfor %}
```

Counting elements starting from specified letter:
```twig
{% for key, count in ['foo', 'bar', 'foobar']|countBy(=> _|first|capitalize) %}
    * {{ count }} elements start from {{ key }}.
{% endfor %}
```

----------------------------------------------------------------

<a name="install"></a>
## Installation

### Install via Composer:
```bash
composer require dpolac/twig-lambda
```

### Add the extension to Twig:
```php
$twig->addExtension(new \DPolac\TwigLambda\Extension\LambdaExtension());
```

----------------------------------------------------------------

## Usage

**Filters:**
    [map](#map)
    [filter](#filter)
    [unique](#unique)
    [uniqueBy](#uniqueby)
    [groupBy](#groupby)
    [sortBy](#sortby)
    [countBy](#countby)
    <br>
**Tests:**
    [any](#any)
    [every](#every)
    <br>
**Functions:**
    [call](#call)

<a name="lambda"></a>
### Lambda expression
To create lambda expression prepend any valid Twig expression
with `=>` operator. Inside of the lambda expression you can use
any variable from the outside. There are also two special
variables available:
  * `_` (single underscore) - first argument,
  * `__` (double underscore) - array of arguments counted
    from zero.

```
=> _.name
=> _ * 2
=> _|first
=> 'foobar'
=> _ is even
=> __[0] + __[1]
```

----------------------------------------------------------------

<a name="map"></a>
### |map
**Aliases:** `|select`, `|each`<br>
**Signature:** `array|map(lambda)`

Applies a given function to each element and returns
array of results in the same order.

```twig
{% for i in  [1, 2, 3, 4]|map(=> _ * 2) %}
    {{ i }} {# prints '2 4 6 8' #}
{% endfor %}
```

----------------------------------------------------------------

<a name="filter"></a>
### |filter
**Aliases:** `|where`<br>
**Signature:** `array|filter(lambda)`

Returns array of elements that passes a test specified by lambda.

```twig
{% for i in [1, 2, 3, 4, 5, 6]|filter(=> _ is even) %}
    {{ i }} {# prints '2 4 6' #}
{% endfor %}
```

----------------------------------------------------------------

<a name="unique"></a>
### |unique
**Signature:** `array|unique`

Returns array with every element occurring only once.

----------------------------------------------------------------

<a name="groupby"></a>
### |groupBy
**Signature:** `array|groupBy(lambda)`

Sorts an array into groups by the result of lambda.

```twig
{% for key, group in ['foo', 'bar', 'foobar', 'barbar']|groupBy(=> _|first|capitalize) %}
    = {{ key }}
    {% for i in group %}
        * {{ i }}
    {% endfor %}
{% endfor %}
```
will produce
```
    = F
        * foo
        * foobar
    = B
        * bar
        * barbar
```

----------------------------------------------------------------

<a name="sortby"></a>
### |sortBy
**Signature:** `array|sortBy(lambda[, direction = 'ASC'])`

Sorts array by values returned by lambda.
Direction can be 'ASC' or 'DESC'.

```twig
{% for i in ['bar', 'fo', 'foobar', 'foob']|sortBy(=> _|length, 'DESC') %}
    {{ i }} {# prints 'foobar foob bar fo' #}
{% endfor %}
```

----------------------------------------------------------------

<a name="countby"></a>
### |countBy
**Signature:** `array|countBy(lambda)`

Sorts an array into groups and returns a count for the number of
objects in each group.

If lambda returns true, false or null, it will be converted to
string 'true', 'false' or 'null'. Float will be converted to
integer.

```twig
{% for key, count in ['foo', 'bar', 'foobar']|countBy(=> _|first|capitalize) %}
    * {{ count }} elements start from {{ key }}.
{% endfor %}
```
will produce
```
    * 2 elements start from F
    * 1 elements start from B
```

----------------------------------------------------------------

<a name="any"></a>
### is any
**Alias:** `is some`<br>
**Signature:** `array is any(lambda)`

Returns true if lambda returns true for any element from
an array.

**Returns false if array is empty.**

```twig
{{ [1, 2, 3] is any(=> _ is even) ? "There is even element in the array." }}
{# prints 'There is even element in the array.' #}
```

----------------------------------------------------------------

<a name="every"></a>
### is every
**Alias:** `is all`<br>
**Signature:** `array is every(lambda)`

Returns true if lambda returns true for every element from
an array.

**Returns true if array is empty.**

```twig
{{ [1, 2, 3] is any(=> _ is even) ? "There is even element in the array." }}
{# prints 'There is even element in the array.' #}
```

----------------------------------------------------------------

<a name="call"></a>
### call()
**Signature:** `call(lambda [, arguments:array])`

Calls lambda and returns its result. You can provide array
of arguments.

This function is provided to allow creating twig macros taking
lambda as an argument.

```twig
{{ call(=> _ * 2, [10]) }}
{# prints '20' #}
{{ call(=> _.foo, [{foo: 12}]} }}
{# prints '12' #}
```
