cloz.js
====

## Overview

Cloz provides simple closed objects with some simple methods.
A closed object can have closed properties which cannot be accessed directly.
If you want to read or write those properties, use 'get' or 'set' methods.

## Why use closed objects?

If you use objects for both namespace and class and your project become larger, it is recommended to distingish between the two roles.
Thus, functional objects, which is often used as class, should not show the tree of structure in case used as namespace.
A closed object has no information of the tree available, and you can manage objects more semantically.
