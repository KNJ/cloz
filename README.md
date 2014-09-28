cloz.js
====

## Overview

Cloz provides simple closed objects with some simple methods.
A closed object can have closed properties which cannot be accessed directly.
If you want to read or write those properties, use 'get' or 'set' methods.

## Why use closed objects?

If you use objects for both namespace and class and your project become larger, it is recommended to distingish between the two roles.

```js
// use as namespace
var my_project = {};
my_project.model = {};
my_project.controller = {};
my_project.view = {};

// use as class property
my_project.contributors = ['someone', 'somebody'];
my_project.view.index = function(){
	return '<p>index</p>';
};
```

Therefore, functional objects, which is often used as class, should not show the tree of structure in case used as namespace.
A closed object has no information of the tree available, and you can manage objects more semantically.

```js
// cloz
// my_project is available
// but my_project.contributors is forbidden
var my_project = cloz({}, {
	contributors: ['someone', 'somebody'],
});

// cloz
// my_project.view is available
// but my_project.view.index is forbidden
my_project.view = cloz({}, {
	index: function(){
		return '<p>index</p>';
	}
});
```

## Basic usage

To create cloz (equal to closed object), a base object is needed.

```js
// base object
var base = {};

// create new cloz
var creature = cloz(base);

// If the base object is empty and never shared, the following is OK.
var creation = cloz();
```

To inherit cloz, just put it as base object.

```js
var mammal = cloz(creature);
```

To extend base object, provide the second argument.

```js
var man = cloz(mammal, {
	name: 'man',
	speak: function(){},
});
```

To get property of cloz, use `cloz.get()`.

```js
var miki = cloz(man, {
	name: 'Miki',
	hair: 'blond',
	languages: cloz(base, {
		ja: 'こんにちは。',
		en: 'Hi.',
	}),
	speak: function(language){
		return this.get('languages').get(language);
	},
});

console.log(miki.get('name')); // => "Miki"
console.log(miki.get('speak', 'ja')); // => "こんにちは。"
```

To set property of cloz, use `cloz.set()`.

```js
miki.set('hair', 'brown');
miki.set('laugh', function(){ return 'Haha.'; });
```

If preset property's value is cloz, use `cloz.extend()` instead.

```js
miki.extend('language', {
	ja: 'やあ。',
	zh: '你好。',
});

console.log(miki.get('speak', 'ja')); // => "やあ。"
console.log(miki.get('speak', 'en')); // => "Hi."
console.log(miki.get('speak', 'zh')); // => "你好。"
```

## How cloz works

Cloz's inheritance is prototypal. If a value of property of a base object is changed, that of the cloz will be also changed.
But overridden values are static.

```js
var miki_clone = cloz(miki, {
	name: 'Cloned Miki',
});

console.log(miki_clone.get('name')); // => "Cloned Miki"
console.log(miki_clone.get('hair')); // => "brown"

miki.set('name', 'Original Miki');
miki.set('hair', 'black');

console.log(miki_clone.get('name')); // => "Cloned Miki" (referred to miki_clone)
console.log(miki_clone.get('hair')); // => "black" (referred to miki)
```

You shoud **not** deep-nested cloz (3 level or more) becase that can unexpectedly affect the base object as below.

```js
var dog = cloz(mammal, {
	status: cloz(base, {
		feelings: cloz(base, {
			happy: '^o^',
		}),
	}),
});

var pochi = cloz(dog);
pochi.get('status').extend('feelings', {
	happy: '^w^',
});

// pochi's method affected dog's property
console.log(dog.get('status').get('feelings').get('happy')); // => ^w^
```

## API

### get(property [, arguments ] ])

Get the property's value or execute the function.

### gain(property [, value = null [, arguments] ])

Behave similarly to `get()` but return the default value if the property is not found.

### getAll()

Get an object containing all properties and each value.

### set(property, value)

Set the value to the property.

### extend(property, extension)

Extend cloz value.

### extension._cloz(function)

Call the function whenever the cloz or its descendant cloz is created.

```js
// output "created!" twice
var parent = cloz({},{
	_cloz: function(){
		console.log('created!');
	}
});
var child = cloz(parent);
```

### extension.__cloz(function)

Call the function when the cloz itself is created.

```js
// output "created!" only once
var parent = cloz({},{
	__cloz: function(){
		console.log('created!');
	}
});
var child = cloz(parent); // => no output
```
