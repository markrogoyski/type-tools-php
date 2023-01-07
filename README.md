# Type tools

![Packagist PHP Version Support](https://img.shields.io/packagist/php-v/smoren/type-tools)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/Smoren/type-tools-php/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/Smoren/type-tools-php/?branch=master)
[![Coverage Status](https://coveralls.io/repos/github/Smoren/type-tools-php/badge.svg?branch=master)](https://coveralls.io/github/Smoren/type-tools-php?branch=master)
![Build and test](https://github.com/Smoren/type-tools-php/actions/workflows/test_master.yml/badge.svg)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Helpers for different operations with PHP data types.

## How to install to your project
```
composer require smoren/type-tools
```

## Quick Reference

### Unique Extractor
| Method                     | Description                                          | Code Snippet                                |
|----------------------------|------------------------------------------------------|---------------------------------------------|
| [`getString`](#Get-String) | Returns unique string of the given variable          | `UniqueExtractor::getString($var, $strict)` |
| [`getHash`](#Get-Hash)     | Returns unique md5 hash string of the given variable | `UniqueExtractor::getHash($var, $strict)`   |

### Object Accessor
| Method                                                                | Description                                                         | Code Snippet                                                            |
|-----------------------------------------------------------------------|---------------------------------------------------------------------|-------------------------------------------------------------------------|
| [`getPropertyValue`](#Get-Property-Value)                             | Returns value of the object property                                | `ObjectAccessor::getPropertyValue($object, $propertyName))`             |
| [`getPropertyValueByGetter`](#Get-Property-Value-By-Getter)           | Returns property value by getter                                    | `ObjectAccessor::getPropertyValueByGetter($object, $propertyName))`     |
| [`hasAccessibleProperty`](#Has-Accessible-Property)                   | Returns true if object has accessible property by name or by getter | `ObjectAccessor::hasAccessibleProperty($object, $propertyName)`         |
| [`hasPublicProperty`](#Has-Public-Property)                           | Returns true if object has public property                          | `ObjectAccessor::hasPublicProperty($object, $propertyName)`             |
| [`hasPropertyAccessibleByGetter`](#Has-Property-Accessible-By-Getter) | Returns true if object has property that is accessible by getter    | `ObjectAccessor::hasPropertyAccessibleByGetter($object, $propertyName)` |
| [`hasProperty`](#Has-Property)                                        | Returns true if object has property                                 | `ObjectAccessor::hasProperty($object, $propertyName)`                   |
| [`hasPublicMethod`](#Has-Public-Method)                               | Returns true if object has public method                            | `ObjectAccessor::hasPublicMethod($object, $methodName)`                 |
| [`hasMethod`](#Has-Method)                                            | Returns true if object has method                                   | `ObjectAccessor::hasMethod($object, $methodName)`                       |

### Map Extractor
| Method              | Description                                            | Code Snippet                                              |
|---------------------|--------------------------------------------------------|-----------------------------------------------------------|
| [`get`](#Get)       | Returns value from the container by key                | `MapExtractor::get($container, $key, $defaultValue)`      |
| [`exists`](#Exists) | Returns true if accessible key exists in the container | `MapExtractor::exists($container, $key)`                  |

## Usage

### Unique Extractor

Tool for extracting unique IDs and hashes of any PHP variables and data structures.

Works in two modes: strict and non-strict.

In strict mode:
- scalars: unique strictly by type;
- objects: unique by instance;
- arrays: unique by serialized value;
- resources: result is unique by instance.

In non-strict mode:
- scalars: unique by value;
- objects: unique by serialized value;
- arrays: unique by serialized value;
- resources: result is unique by instance.

#### Get String

Returns unique string of the given variable.

```UniqueExtractor::getString(mixed $var, bool $strict): string```

```php
use Smoren\TypeTools\UniqueExtractor;

$intValue = 5;
$floatValue = 5.0;

$intValueStrictUniqueId = UniqueExtractor::getString($intValue, true);
$floatValueStrictUniqueId = UniqueExtractor::getString($floatValue, true);

var_dump($intValueStrictUniqueId === $floatValueStrictUniqueId);
// false

$intValueNonStrictUniqueId = UniqueExtractor::getString($intValue, false);
$floatValueNonStrictUniqueId = UniqueExtractor::getString($floatValue, false);

var_dump($intValueNonStrictUniqueId === $floatValueNonStrictUniqueId);
// true
```
#### Get Hash

Returns unique md5 hash string of the given variable.

```UniqueExtractor::getHash(mixed $var, bool $strict): string```

```php
use Smoren\TypeTools\UniqueExtractor;

$intValue = 5;
$floatValue = 5.0;

$intValueStrictHash = UniqueExtractor::getHash($intValue, true);
$floatValueStrictHash = UniqueExtractor::getHash($floatValue, true);

var_dump($intValueStrictHash === $floatValueStrictHash);
// false

$intValueNonStrictHash = UniqueExtractor::getHash($intValue, false);
$floatValueNonStrictHash = UniqueExtractor::getHash($floatValue, false);

var_dump($intValueNonStrictHash === $floatValueNonStrictHash);
// true
```

### Object Accessor

Tool for reflecting and accessing object properties and methods.

#### Get Property Value

Returns value of the object property.

```ObjectAccessor::getPropertyValue(object $object, string $propertyName): mixed```

Can access property by its name or by getter.

```php
use Smoren\TypeTools\ObjectAccessor;

class MyClass {
    public int $publicProperty = 1;
    private int $privateProperty = 2;
    
    public function getPrivateProperty(): int
    {
        return $this->privateProperty;
    }
}

$myObject = new MyClass();

// Getting by name:
var_dump(ObjectAccessor::getPropertyValue($myObject, 'publicProperty'));
// 1

// Getting by getter (getPrivateProperty()):
var_dump(ObjectAccessor::getPropertyValue($myObject, 'privateProperty'));
// 2
```

#### Get Property Value By Getter

Returns property value by getter.

```ObjectAccessor::getPropertyValueByGetter(object $object, string $propertyName): mixed```

```php
use Smoren\TypeTools\ObjectAccessor;

class MyClass {
    private int $privateProperty = 2;
    
    public function getPrivateProperty(): int
    {
        return $this->privateProperty;
    }
}

$myObject = new MyClass();

// Getting by getter (getPrivateProperty()):
var_dump(ObjectAccessor::getPropertyValueByGetter($myObject, 'privateProperty'));
// 2
```

#### Has Accessible Property

Returns true if object has property that is accessible by name or by getter.

```ObjectAccessor::hasAccessibleProperty(object $object, string $propertyName): bool```

```php
use Smoren\TypeTools\ObjectAccessor;

class MyClass {
    public int $publicProperty = 1;
    private int $privateProperty = 2;
    private int $notAccessibleProperty = 3;
    
    public function getPrivateProperty(): int
    {
        return $this->privateProperty;
    }
}

$myObject = new MyClass();

// Accessible by name:
var_dump(ObjectAccessor::hasAccessibleProperty($myObject, 'publicProperty'));
// true

// Accessible by getter:
var_dump(ObjectAccessor::hasAccessibleProperty($myObject, 'privateProperty'));
// true

// Not accessible:
var_dump(ObjectAccessor::hasAccessibleProperty($myObject, 'notAccessibleProperty'));
// false
```

#### Has Public Property

Returns true if object has public property.

```ObjectAccessor::hasPublicProperty(object $object, string $propertyName): bool```

```php
use Smoren\TypeTools\ObjectAccessor;

class MyClass {
    public int $publicProperty = 1;
    private int $privateProperty = 2;
}

$myObject = new MyClass();

var_dump(ObjectAccessor::hasPublicProperty($myObject, 'publicProperty'));
// true

var_dump(ObjectAccessor::hasPublicProperty($myObject, 'privateProperty'));
// false
```

#### Has Property Accessible By Getter

Returns true if object has property that is accessible by getter.

```ObjectAccessor::hasPropertyAccessibleByGetter(object $object, string $propertyName): bool```

```php
use Smoren\TypeTools\ObjectAccessor;

class MyClass {
    private int $privateProperty = 2;
    private int $notAccessibleProperty = 3;
    
    public function getPrivateProperty(): int
    {
        return $this->privateProperty;
    }
}

$myObject = new MyClass();

var_dump(ObjectAccessor::hasPropertyAccessibleByGetter($myObject, 'privateProperty'));
// true

var_dump(ObjectAccessor::hasPropertyAccessibleByGetter($myObject, 'notAccessibleProperty'));
// false
```

#### Has Property

Returns true if object has property.

```ObjectAccessor::hasProperty(object $object, string $propertyName): bool```

```php
use Smoren\TypeTools\ObjectAccessor;

class MyClass {
    public int $publicProperty = 1;
    private int $privateProperty = 2;
}

$myObject = new MyClass();

var_dump(ObjectAccessor::hasProperty($myObject, 'publicProperty'));
// true

var_dump(ObjectAccessor::hasProperty($myObject, 'privateProperty'));
// true

var_dump(ObjectAccessor::hasProperty($myObject, 'anotherProperty'));
// false
```

#### Has Public Method

Returns true if object has public method.

```ObjectAccessor::hasPublicMethod(object $object, string $methodName): bool```

```php
use Smoren\TypeTools\ObjectAccessor;

class MyClass {
    public function publicMethod(): int
    {
        return 1;
    }
    
    private function privateMethod(): int
    {
        return 2;
    }
}

$myObject = new MyClass();

var_dump(ObjectAccessor::hasPublicMethod($myObject, 'publicMethod'));
// true

var_dump(ObjectAccessor::hasPublicMethod($myObject, 'privateMethod'));
// false
```

#### Has Method

Returns true if object has method.

```ObjectAccessor::hasMethod(object $object, string $methodName): bool```

```php
use Smoren\TypeTools\ObjectAccessor;

class MyClass {
    public function publicMethod(): int
    {
        return 1;
    }
    
    private function privateMethod(): int
    {
        return 2;
    }
}

$myObject = new MyClass();

var_dump(ObjectAccessor::hasMethod($myObject, 'publicMethod'));
// true

var_dump(ObjectAccessor::hasMethod($myObject, 'privateMethod'));
// true
```

### Map Accessor

Tool for map-like accessing of different containers by string keys.

Can access:
- properties of objects (by name or by getter);
- elements of arrays and ArrayAccess objects (by key).

#### Get

Returns value from the container by key or default value if key does not exist or not accessible.

```MapAccessor::get(mixed $container, string $key, mixed $defaultValue = null): mixed```

```php
use Smoren\TypeTools\MapAccessor;

$array = [
    'a' => 1,
];

var_dump(MapAccessor::get($array, 'a', 0));
// 1

var_dump(MapAccessor::get($array, 'b', 0));
// 0

var_dump(MapAccessor::get($array, 'b'));
// null

class MyClass {
    public int $publicProperty = 1;
    private int $privateProperty = 2;
    private int $notAccessibleProperty = 3;
    
    public function getPrivateProperty(): int
    {
        return $this->privateProperty;
    }
}

$myObject = new MyClass();

// Accessible by name:
var_dump(MapAccessor::get($myObject, 'publicProperty', 0));
// 1

// Accessible by getter:
var_dump(MapAccessor::get($myObject, 'privateProperty'));
// 2

// Not accessible:
var_dump(MapAccessor::get($myObject, 'notAccessibleProperty', -1));
// -1

var_dump(MapAccessor::get($myObject, 'notAccessibleProperty'));
// null

// Nonexistent:
var_dump(MapAccessor::get($myObject, 'nonexistentProperty', -1));
// -1

var_dump(MapAccessor::get($myObject, 'nonexistentProperty'));
// null
```

#### Exists

Returns true if accessible key exists in the container.

```MapAccessor::exists(mixed $container, string $key): bool```

```php
use Smoren\TypeTools\MapAccessor;

$array = [
    'a' => 1,
];

var_dump(MapAccessor::exists($array, 'a'));
// true

var_dump(MapAccessor::exists($array, 'b'));
// false
class MyClass {
    public int $publicProperty = 1;
    private int $privateProperty = 2;
    private int $notAccessibleProperty = 3;
    
    public function getPrivateProperty(): int
    {
        return $this->privateProperty;
    }
}

$myObject = new MyClass();

// Accessible by name:
var_dump(MapAccessor::exists($myObject, 'publicProperty'));
// true

// Accessible by getter:
var_dump(MapAccessor::exists($myObject, 'privateProperty'));
// true

// Not accessible:
var_dump(MapAccessor::get($myObject, 'notAccessibleProperty'));
// false

// Nonexistent:
var_dump(MapAccessor::get($myObject, 'nonexistentProperty', -1));
// false
```

## Unit testing
```
composer install
composer test-init
composer test
```