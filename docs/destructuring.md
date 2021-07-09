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

De plus, vous pouvez extraire des données *profondes* d'une structure en utilisant la déstructuration. Ceci est illustré dans l'exemple suivant :

```ts
var foo = { bar: { bas: 123 } };
var {bar: {bas}} = foo; // Effectivement `var bas = foo.bar.bas;`
```

#### Déstructuration d'objet avec rest
Vous pouvez récupérer n'importe quel nombre d'éléments d'un objet et obtenir *un objet* des éléments restants en utilisant la déstructuration d'objet avec rest.

```ts
var {w, x, ...remaining} = {w: 1, x: 2, y: 3, z: 4};
console.log(w, x, remaining); // 1, 2, {y:3,z:4}
```
Un cas d'utilisation courant consiste également à ignorer certaines propriétés. Par example :

```ts
// Exemple de fonction
function goto(point2D: {x: number, y: number}) {
  // Imaginez un code qui pourrait casser
  // si vous passez dans un objet
  // plus d'articles que désiré
}
// Un point que vous obtenez de quelque part
const point3D = {x: 1, y: 2, z: 3};
/** Une utilisation astucieuse du rest pour supprimer des propriétés supplémentaires */
const { z, ...point2D } = point3D;
goto(point2D);
```

#### Déstructuration de tableau
Une question de programmation courante : "Comment permuter deux variables sans en utiliser une troisième ?". La solution TypeScript :

```ts
var x = 1, y = 2;
[x, y] = [y, x];
console.log(x, y); // 2,1
```
Notez que la déstructuration des tableaux est en fait le compilateur qui effectue le `[0], [1], ...` et ainsi de suite pour vous. Il n'y a aucune garantie que ces valeurs existeront.

#### Array Destructuration avec rest
Vous pouvez récupérer n'importe quel nombre d'éléments d'un tableau et obtenir *un tableau* des éléments restants en utilisant la déstructuration de tableau avec rest.

```ts
var [x, y, ...remaining] = [1, 2, 3, 4];
console.log(x, y, remaining); // 1, 2, [3,4]
```

#### Déstructuration de tableau avec ignores
Vous pouvez ignorer n'importe quel index en laissant simplement son emplacement vide, c'est-à-dire `, ,` dans la partie gauche de l'affectation. Par example :

```ts
var [x, , ...remaining] = [1, 2, 3, 4];
console.log(x, remaining); // 1, [3,4]
```

#### Génération JS
La génération JavaScript pour les cibles non ES6 implique simplement de créer des variables temporaires, tout comme vous auriez à le faire vous-même sans la prise en charge du langage natif pour la déstructuration, par exemple :

```ts
var x = 1, y = 2;
[x, y] = [y, x];
console.log(x, y); // 2,1

// devient //

var x = 1, y = 2;
_a = [y,x], x = _a[0], y = _a[1];
console.log(x, y);
var _a;
```

#### Résumé
La déstructuration peut rendre votre code plus lisible et maintenable en réduisant le nombre de lignes et en clarifiant l'intention. La déstructuration des tableaux peut vous permettre d'utiliser des tableaux comme s'ils étaient des tuples.
