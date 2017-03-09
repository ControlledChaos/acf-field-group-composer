# acf-field-group-composer

[![standard-readme compliant](https://img.shields.io/badge/readme%20style-standard-brightgreen.svg?style=flat-square)](https://github.com/RichardLitt/standard-readme)
[![Build Status](https://img.shields.io/travis/bleech/acf-field-group-composer.svg?style=flat-square)](https://travis-ci.org/bleech/acf-field-group-composer)
[![Code Quality](https://img.shields.io/scrutinizer/g/bleech/acf-field-group-composer.svg?style=flat-square)](https://scrutinizer-ci.com/g/bleech/acf-field-group-composer)
[![Code Coverage](https://img.shields.io/coveralls/bleech/acf-field-group-composer.svg?style=flat-square)](https://coveralls.io/github/bleech/acf-field-group-composer)

> Configuration builder for advanced custom fields field groups

This plugin helps you create reusable ACF fields and field groups with code.

## Table of Contents

- [Background](#background)
- [Install](#install)
- [Usage](#usage)
- [API](#api)
- [Maintainers](#maintainers)
- [Contribute](#contribute)
- [License](#license)

## Background

ACF provides a great interface to create custom meta boxes in WordPress. However, there are to major downsides using it out of the box.

First, configuring it via its GUI can be cumbersome if you have a lot of custom fields. Also fields created via the GUI are only stored in the database. That means migrating them between different stages of a development setup can only be done by migrating the database as well.

Second, the local JSON feature, or `add_local_field_group`, is handy for checking the config into SCM and deploying it to different stages but it lacks some customizability, like reusing already specified fields in different context.

acf-field-group-composer helps circumventing these downsides. It let's you use the build in `add_local_field_group` function, automatically adds unique keys to all fields added, and, if a field is not an array, but a string, it will apply a filter with that name and use the return value as a new field in the config.

## Install

TODO: install via WordPress instructions

To install via composer, run

```bash
composer require flyntwp/acf-field-group-composer
```

## Usage

To register a field group run

```php
ACFComposer\ACFComposer::registerFieldGroup($config);
```

The `$config` variable should be of the same format as the one you would pass to `acf_add_local_field_group`. There are only two things that are different:
1. You do not have to specify a `key` in any field or group.
2. A field group needs a unique `name` property.

Following the minimal example [from ACF](https://www.advancedcustomfields.com/resources/register-fields-via-php)

```php
$config = [
  'name' => 'group_1',
  'title' => 'My Group',
  'fields' => [
    [
      'label' => 'Sub Title',
      'name' => 'sub_title',
      'type' => 'text',
    ]
  ],
  'location' => [
    [
      [
      'param' => 'post_type',
      'operator' => '==',
      'value' => 'post',
      ],
    ],
  ],
];
```

In order to make use of the additional functionality of **acf-field-group-composer** you can extract the specified field and return in a filter. The name of the filter can be anything, however, we recommend a meaningful naming scheme like in the following example.

```php
add_filter('MyProject/ACF/fields/Field_1', function ($field) {
  return [
    'label' => 'Sub Title',
    'name' => 'sub_title',
    'type' => 'text',
  ]
});

$config = [
  'name' => 'group_1',
  'title' => 'My Group',
  'fields' => [
    'MyProject/ACF/fields/Field_1'
  ],
  'location' => [
    [
      [
      'param' => 'post_type',
      'operator' => '==',
      'value' => 'post',
      ]
    ]
  ]
];
```

The same can be done for the location.

```php
add_filter('MyProject/ACF/locations/PostTypePost', function ($location) {
  return [
    'param' => 'post_type',
    'operator' => '==',
    'value' => 'post',
  ]
});

$config = [
  'name' => 'group_1',
  'title' => 'My Group',
  'fields' => [
    'MyProject/ACF/fields/Field_1'
  ],
  'location' => [
    [
      'MyProject/ACF/locations/PostTypePost'
    ]
  ]
];
```

Combining the previous steps will yield the following result

```php
add_filter('MyProject/ACF/fields/Field_1', function ($field) {
  return [
    'label' => 'Sub Title',
    'name' => 'sub_title',
    'type' => 'text',
  ]
});

add_filter('MyProject/ACF/locations/PostTypePost', function ($location) {
  return [
    'param' => 'post_type',
    'operator' => '==',
    'value' => 'post',
  ]
});

$config = [
  'name' => 'group_1',
  'title' => 'My Group',
  'fields' => [
    'MyProject/ACF/fields/Field_1'
  ],
  'location' => [
    [
      'MyProject/ACF/locations/PostTypePost'
    ]
  ]
];

ACFComposer\ACFComposer::registerFieldGroup($config);
```

## API

### ACFComposer\ACFComposer (class)

#### registerFieldGroup (static)

Main function of this package. Resolves a given field group config and registers an acf field group via `acf_add_local_field_group`.

```php
public static function registerFieldGroup(array $config)
```

### ACFComposer\ResolveConfig (class)

#### forFieldGroup (static)

Validate and generate a field group config from a given config array by adding keys and replacing filter strings with actual content.

```php
public static function forFieldGroup(array $config)
```

#### forField (static)

Validate and generate a field config from a given config array by adding keys and replacing filter strings with actual content.

```php
public static function forField(array $config, array $parentKeys = [])
```

#### forLayout (static)

Validate and generate a layout config from a given config array by adding keys and replacing filter strings with actual content.

```php
public static function forLayout(array $config, array $parentKeys = [])
```

#### forLocation (static)

Validate a location config from a given config array.

```php
public static function forLocation(array $config)
```

## Maintainers

This project is maintained by [bleech](https://github.com/bleech).

Main people in charge of the repo are:

- [Dominik Tränklein](https://github.com/domtra)
- [Doğa Gürdal](https://github.com/Qakulukiam)

## Contribute

To contribute, please use github [issues](https://github.com/bleech/acf-field-group-composer/issues). PRs accepted.

Small note: If editing the README, please conform to the [standard-readme](https://github.com/RichardLitt/standard-readme) specification.

## License

MIT © bleech
