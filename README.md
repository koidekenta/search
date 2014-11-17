# CakePHP Search

Search provides a search module for CakePHP applications.

## Requirements

The master branch has the following requirements:

* CakePHP 3.0.0 or greater.
* PHP 5.4.16 or greater.
* SQLite or another database driver that CakePHP can talk to. By default DebugKit will use SQLite, if you
  need to use a different database see the Database Configuration section below.

## Installation

* Install the plugin with composer from your CakePHP Project's ROOT directory (where composer.json file is located)
```sh
php composer.phar require cakephp/debug_kit "dev-master"
```

* Load the plugin
```php
Plugin::load('Search');
```

## Usage

* Add a search config method in your model
```php
use Search\Manager;

...

public function searchConfiguration() {
  $search = new Manager($this);
  $search
    ->value('currency_id', [
      'field' => $this->alias() . '.currency_id'
    ])
    ->like('name', [
      'before' => true,
      'after' => true,
      'field' => [$this->alias() . '.name']
    ]);
  return $search;
}
```

* Add The Behavior in your initialize method
```php
public function initialize(array $config) {
  ...
  $this->addBehavior('Search.Search');
  ...
}
```

* Example of index controller for a model Country
```php
public function index() {
  $query = $this->Country
    ->find('search', $this->request->query)
    ->where(['name !=' => null])
    ->order(['Country.id' => 'asc'])
    ->contain([
      'Cities'
    ]);
  $this->set('countries', $this->paginate($query));
}
```

* Then add the component search in the necessary methods (for our example index)
```php
public function index() {
  $this->loadComponent('Search.Prg');
}
```

* Instead, you can add this in your AppController to enable component in all index methods
```php
public function initialize() {
    parent::initialize();
    if ($this->request->action === 'index'):
      $this->loadComponent('Search.Prg');
    endif;
  }
```
