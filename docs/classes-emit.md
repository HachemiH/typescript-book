#### Quoi de neuf avec l'IIFE
Le js généré pour la classe aurait pu être :
```ts
function Point(x, y) {
    this.x = x;
    this.y = y;
}
Point.prototype.add = function (point) {
    return new Point(this.x + point.x, this.y + point.y);
};
```

La raison pour laquelle il est enveloppé dans une expression de fonction immédiatement invoquée (IIFE), c'est-à-dire :

```ts
(function () {

    // BODY

    return Point;
})();
```

a à voir avec l'héritage. Il permet à TypeScript de capturer la classe de base sous la forme d'une variable `_super`, par exemple :

```ts
var Point3D = (function (_super) {
    __extends(Point3D, _super);
    function Point3D(x, y, z) {
        _super.call(this, x, y);
        this.z = z;
    }
    Point3D.prototype.add = function (point) {
        var point2D = _super.prototype.add.call(this, point);
        return new Point3D(point2D.x, point2D.y, this.z + point.z);
    };
    return Point3D;
})(Point);
```

Notez que l'IIFE permet à TypeScript de capturer facilement la classe de base `Point` dans une variable `_super` et qui est utilisée de manière cohérente dans le corps de la classe.

### `__extends`
Vous remarquerez que dès que vous héritez d'une classe TypeScript génère également la fonction suivante :
```ts
var __extends = this.__extends || function (d, b) {
    for (var p in b) if (b.hasOwnProperty(p)) d[p] = b[p];
    function __() { this.constructor = d; }
    __.prototype = b.prototype;
    d.prototype = new __();
};
```
Ici, `d` fait référence à la classe dérivée et `b` fait référence à la classe de base. Cette fonction fait deux choses :
1. elle copie les membres statiques de la classe de base sur la classe enfant, c'est-à-dire `for (var p in b) if (b.hasOwnProperty(p)) d[p] = b[p];`
1. elle configure le prototype de la fonction de classe enfant pour éventuellement rechercher des membres sur le `proto` du parent, c'est-à-dire efficacement `d.prototype.__proto__ = b.prototype`

Les gens ont rarement du mal à comprendre le 1, mais beaucoup de gens ont du mal avec le 2. Une explication s'impose donc.

#### `d.prototype.__proto__ = b.prototype`

Après avoir instruit de nombreuses personnes à ce sujet, je trouve que l'explication suivante est la plus simple. Nous allons d'abord expliquer comment le code de `__extends` est équivalent au simple `d.prototype.__proto__ = b.prototype`, puis pourquoi cette ligne en elle-même est importante. Pour comprendre tout cela, vous devez savoir les choses suivantes :

1. `__proto__`
2. `prototype`
3. effet de `new` sur `this` à l'intérieur de la fonction appelée
4. effet de `new` sur `prototype` et `__proto__`

Tous les objets en JavaScript contiennent un membre `__proto__`. Ce membre n'est souvent pas accessible dans les navigateurs plus anciens (parfois la documentation se réfère à cette propriété magique comme `[[prototype]]`). Il a un objectif : si une propriété n'est pas trouvée sur un objet lors de la recherche (par exemple `obj.property`), alors elle est recherchée dans `obj.__proto__.Property`. Si elle n'est toujours pas trouvée, alors `obj.__proto__.__proto__.Property` jusqu'à ce que : *elle soit trouvée* ou jusqu'à ce que *le dernier `.__proto__` lui-même soit nul*. Cela explique pourquoi JavaScript est censé prendre en charge *l'héritage prototypale* de manière native. Cela est illustré dans l'exemple suivant, que vous pouvez exécuter dans la console Chrome ou Node.js :

```ts
var foo = {}

// setup on foo as well as foo.__proto__
foo.bar = 123;
foo.__proto__.bar = 456;

console.log(foo.bar); // 123
delete foo.bar; // suppression de la propriété de l'objet
console.log(foo.bar); // 456
delete foo.__proto__.bar; // suppression de la propriété de foo.__proto__
console.log(foo.bar); // undefined
```

Cool, pour que vous compreniez `__proto__`. Un autre fait utile est que toutes les `function`s en JavaScript ont une propriété appelée `prototype` qui a un `constructor` qui pointe vers la fonction. Ceci est illustré ci-dessous :

```ts
function Foo() { }
console.log(Foo.prototype); // {} c'est-à-dire qu'il existe et n'est pas undefined
console.log(Foo.prototype.constructor === Foo); // A un membre appelé `constructor` qui pointe vers la fonction
```

Examinons maintenant *l'effet de `new` sur `this` à l'intérieur de la fonction appelée*. Fondamentalement, `this` à l'intérieur de la fonction appelée va pointer vers l'objet nouvellement créé qui sera renvoyé par la fonction. Il est simple de voir si vous mutez une propriété sur `this` à l'intérieur de la fonction :

```ts
function Foo() {
    this.bar = 123;
}

// appel avec le nouvel opérateur
var newFoo = new Foo();
console.log(newFoo.bar); // 123
```

Maintenant, la seule autre chose que vous devez savoir est que l'appel de `new` sur une fonction affecte le` prototype` de la fonction au `__proto__` de l'objet nouvellement créé qui est renvoyé par l'appel de fonction. Voici le code que vous pouvez exécuter pour le comprendre complètement :

```ts
function Foo() { }

var foo = new Foo();

console.log(foo.__proto__ === Foo.prototype); // True!
```

C'est cela. Regardez maintenant ce qui suit directement dans `__extends`. J'ai pris la liberté de numéroter ces lignes :

```ts
1  function __() { this.constructor = d; }
2   __.prototype = b.prototype;
3   d.prototype = new __();
```

La lecture de cette fonction en sens inverse de `d.prototype = new __()` sur la ligne 3 signifie effectivement que `d.prototype = {__proto__ : __.prototype}` (en raison de l'effet de `new` sur `prototype` et `__proto__`), en la combinant avec la ligne précédente (c'est-à-dire la ligne 2 `__.prototype = b.prototype;`) vous obtenez `d.prototype = {__proto__ : b.prototype}`.

Mais attendez, nous voulions `d.prototype.__proto__` c'est-à-dire que le proto a changé et conserve l'ancien `d.prototype.constructor`. C'est là que la signification de la première ligne (c'est à dire `function __() { this.constructor = d; }`) entre en jeu. Ici, nous aurons effectivement `d.prototype = {__proto__ : __.prototype, constructor : d}` (en raison de l'effet de `new` sur `this` à l'intérieur de la fonction appelée). Donc, depuis que nous restaurons `d.prototype.constructor`, la seule chose que nous avons vraiment muté est le `__proto__` par conséquent `d.prototype.__proto__ = b.prototype`.

#### `d.prototype.__proto__ = b.prototype` importance

L'importance est qu'elle vous permet d'ajouter des fonctions membres à une classe enfant et d'hériter d'autres de la classe de base. Ceci est démontré par l'exemple simple suivant :

```ts
function Animal() { }
Animal.prototype.walk = function () { console.log('walk') };

function Bird() { }
Bird.prototype.__proto__ = Animal.prototype;
Bird.prototype.fly = function () { console.log('fly') };

var bird = new Bird();
bird.walk();
bird.fly();
```
Fondamentalement `bird.fly` sera recherché de `bird.__proto__.fly` (souvenez-vous que `new` fait le `bird.__proto__` qui pointe vers `Bird.prototype`) et `bird.walk` (un membre hérité) sera recherché de `bird.__proto__.__proto__.walk` (en tant que `bird.__proto__ == Bird.prototype` et `bird.__proto__.__proto__` == `Animal.prototype`).
