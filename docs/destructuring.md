### Déstructuration

TypeScript prend en charge les formes de déstructuration suivantes (littéralement nommées après la dé-structuration, c'est-à-dire la rupture de la structure) :

1. Déstructuration d'objets
1. Déstructuration de tableaux

Il est facile de considérer la déstructuration comme l'inverse de la *structuration*. La méthode de *structuration* en JavaScript est l’objet littéral :

```ts
var foo = {
    bar: {
        bas: 123
    }
};
```

Sans l'incroyable support de *structuration* intégré à JavaScript, créer de nouveaux objets à la volée serait en effet très fastidieux. La déstructuration apporte le même niveau de commodité pour extraire des données d'une structure.


#### Déstructuration d'objets
La déstructuration est utile car elle permet de faire en une seule ligne, ce qui nécessiterait autrement plusieurs lignes. Considérons le cas suivant :

```ts
var rect = { x: 0, y: 10, width: 15, height: 20 };

// Affectation par destructuring
var {x, y, width, height} = rect;
console.log(x, y, width, height); // 0,10,15,20

rect.x = 10;
({x, y, width, height} = rect); // affectation aux variables existantes à l'aide de parenthèses externes
console.log(x, y, width, height); // 10,10,15,20
```
Ici en l'absence de déstructuration il faudrait extraire `x,y,width,height` un par un à partir de `rect`.

Pour affecter une variable extraite à un nouveau nom de variable, vous pouvez procéder comme suit :

```ts
// structure
const obj = {"some property": "some value"};

// destructure
const {"some property": someProperty} = obj;
console.log(someProperty === "some value"); // true
```

Additionally you can get *deep* data out of a structure using destructuring. This is shown in the following example:

```ts
var foo = { bar: { bas: 123 } };
var {bar: {bas}} = foo; // Effectively `var bas = foo.bar.bas;`
```

#### Object Destructuring with rest
You can pick up any number of elements from an object and get *an object* of the remaining elements using object destructuring with rest.

```ts
var {w, x, ...remaining} = {w: 1, x: 2, y: 3, z: 4};
console.log(w, x, remaining); // 1, 2, {y:3,z:4}
```
A common use case is also to ignore certain properties. For example:
```ts
// Example function
function goto(point2D: {x: number, y: number}) {
  // Imagine some code that might break
  // if you pass in an object
  // with more items than desired
}
// Some point you get from somewhere
const point3D = {x: 1, y: 2, z: 3};
/** A nifty use of rest to remove extra properties */
const { z, ...point2D } = point3D;
goto(point2D);
```

#### Array Destructuring
A common programming question: "How to swap two variables without using a third one?". The TypeScript solution:

```ts
var x = 1, y = 2;
[x, y] = [y, x];
console.log(x, y); // 2,1
```
Note that array destructuring is effectively the compiler doing the `[0], [1], ...` and so on for you. There is no guarantee that these values will exist.

#### Array Destructuring with rest
You can pick up any number of elements from an array and get *an array* of the remaining elements using array destructuring with rest.

```ts
var [x, y, ...remaining] = [1, 2, 3, 4];
console.log(x, y, remaining); // 1, 2, [3,4]
```

#### Array Destructuring with ignores
You can ignore any index by simply leaving its location empty i.e. `, ,` in the left hand side of the assignment. For example:
```ts
var [x, , ...remaining] = [1, 2, 3, 4];
console.log(x, remaining); // 1, [3,4]
```

#### JS Generation
The JavaScript generation for non ES6 targets simply involves creating temporary variables, just like you would have to do yourself without native language support for destructuring e.g.

```ts
var x = 1, y = 2;
[x, y] = [y, x];
console.log(x, y); // 2,1

// becomes //

var x = 1, y = 2;
_a = [y,x], x = _a[0], y = _a[1];
console.log(x, y);
var _a;
```

#### Summary
Destructuring can make your code more readable and maintainable by reducing the line count and making the intent clear. Array destructuring can allow you to use arrays as though they were tuples.
