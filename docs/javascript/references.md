## Références

Au-delà des littéraux, tout objet en JavaScript (y compris les fonctions, les tableaux, les expressions rationnelles, etc.) sont des références. Cela signifie ce qui suit.

### Les mutations à travers toutes les références

```js
var foo = {};
var bar = foo; // bar est une référence au même objet

foo.baz = 123;
console.log(bar.baz); // 123
```

### L'égalité est pour les références

```js
var foo = {};
var bar = foo; // bar est une référence
var baz = {}; // baz est un *nouvel objet* distinct de `foo`

console.log(foo === bar); // true
console.log(foo === baz); // false
```
