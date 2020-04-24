# Laravel Sortable

This package provides a trait that adds sortable behaviour to an Eloquent model.

The value of the order column of a new record of a model is determined by the maximum value of the order column of all or a subset group of records of that model + 1.

The package also provides a query scope to fetch all the records in the right order.

This package is a fork of the popular [spatie/eloquent-sortable](https://github.com/spatie/eloquent-sortable) with added functionality to allow sorting on subsets of models as well as moving a model to a specific position.

## Installation

Via Composer

``` bash
$ composer require statch/sortable
```

## Usage

To add sortable behaviour to your model you must:

1. Use the trait `Statch\Sortable`.
2. Optionally specify which column will be used as the order column. The default is `position`.

### Examples

*Simple ordered model*

```php
use Statch\Sortable;

class MyModel extends Eloquent
{

    use SortableTrait;

    public $sortable = [
        'sort_on_creating' => true,
        'order_column'      => 'position',
    ];

    ...
}
```

*Ordered model with a grouping column*

```php
use Statch\Sortable;

class MyModel extends Eloquent
{

    use SortableTrait;

    public $sortable = [
        'sort_on_creating'  => true,
        'order_column'      => 'position',
        'group_column'      => 'group_id',
    ];

    ...
}
```

*Ordered model grouped on multiple columns*

```php
use Statch\Sortable;

class MyModel extends Eloquent
{

    use SortableTrait;

    public $sortable = [
        'sort_on_creating'  => true,
        'order_column'      => 'position',
        'group_column'      => ['group_id','user_id'],
    ];

    ...
}
```

If you don't set a value for `$sortable['order_column']` the package will assume an order column name of `position`.

If you don't set a value `$sortable['sort_on_creating']` the package will automatically assign the next highest order value to the new model;

Assuming that the db table for `MyModel` is empty:

```php
$myModel = new MyModel();
$myModel->save(); // order_column for this record will be set to 1

$myModel = new MyModel();
$myModel->save(); // order_column for this record will be set to 2

$myModel = new MyModel();
$myModel->save(); // order_column for this record will be set to 3
```

The trait also provides an ordered query scope. All models will be returned ordered by 'group' and then 'position' if you have not applied a where() method for your group column on your query,

```php
$orderedRecords = MyModel::ordered()->get();

$groupedOrderedRecords = MyModel::where('group_id', 2)->ordered()->get();

$allRecords = MyModel::ordered()->get();
```

You can set a new order for all the records using the `setNewOrder`-method

```php
/**
 * the record for model id 3 will have record_column value 1
 * the record for model id 1 will have record_column value 2
 * the record for model id 2 will have record_column value 3
 */
MyModel::setNewOrder([3,1,2]);
```

Optionally you can pass the starting order number as the second argument.

```php
/**
 * the record for model id 3 will have record_column value 11
 * the record for model id 1 will have record_column value 12
 * the record for model id 2 will have record_column value 13
 */
MyModel::setNewOrder([3,1,2], 10);
```

You can also move a model up or down with these methods:

```php
$myModel->moveOrderDown();
$myModel->moveOrderUp();
```

You can also move a model to the first or last position:

```php
$myModel->moveToStart();
$myModel->moveToEnd();
```

You can swap the order of two models:

```php
MyModel::swapOrder($myModel, $anotherModel);
```

You can move a model to a specific position:

```php
$myModel->moveToPosition(4);
```



## Change log

Please see the [changelog](changelog.md) for more information on what has changed recently.

## Testing

``` bash
$ composer test
```

## Contributing

Please see [contributing.md](contributing.md) for details and a todolist.

## Security

If you discover any security related issues, please email corrado.striuli@gmail.com instead of using the issue tracker.

## Credits

- [Corrado Striuli][link-author]
- [All Contributors][link-contributors]

## License

MIT. Please see the [license file](license.md) for more information.

[link-author]: https://bitbucket.com/statch
[link-contributors]: ../../contributors
