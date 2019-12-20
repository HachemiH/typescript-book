### Classes
La raison pour laquelle il est important d'avoir des classes en JavaScript comme élément de première classe est que :
1. [Les classes offrent une abstraction structurelle utile](./tips/classesAreUseful.md)
1. Cela fournit aux développeurs un moyen cohérent d'utiliser des classes au lieu que chaque framework (emberjs, reactjs, etc.) propose sa propre version.
1. Les développeurs orientés objet comprennent déjà les classes.

Enfin, les développeurs JavaScript peuvent *avoir une `class`*. Ici, nous avons une classe de base appelée Point :
```ts
class Point {
    x: number;
    y: number;
    constructor(x: number, y: number) {
        this.x = x;
        this.y = y;
    }
    add(point: Point) {
        return new Point(this.x + point.x, this.y + point.y);
    }
}

var p1 = new Point(0, 10);
var p2 = new Point(10, 20);
var p3 = p1.add(p2); // {x:10,y:30}
```
Cette classe génère le JavaScript suivant sur une émission ES5 :
```ts
var Point = (function () {
    function Point(x, y) {
        this.x = x;
        this.y = y;
    }
    Point.prototype.add = function (point) {
        return new Point(this.x + point.x, this.y + point.y);
    };
    return Point;
})();
```
Il s'agit d'un modèle de classe JavaScript traditionnel assez idiomatique maintenant en tant que construction de langage de première classe.

### Héritage
Les classes en TypeScript (comme les autres langages) prennent en charge l'héritage *unique* en utilisant le mot clé `extend` comme indiqué ci-dessous :

```ts
class Point3D extends Point {
    z: number;
    constructor(x: number, y: number, z: number) {
        super(x, y);
        this.z = z;
    }
    add(point: Point3D) {
        var point2D = super.add(point);
        return new Point3D(point2D.x, point2D.y, this.z + point.z);
    }
}
```
Si vous avez un constructeur dans votre classe, vous *devez* appeler le constructeur parent à partir de votre constructeur (TypeScript vous le signalera). Cela garantit que les éléments dont il a besoin pour définir `this` sont définis. Après l'appel à `super`, vous pouvez ajouter tout ce que vous voulez faire dans votre constructeur (ici, nous ajoutons une autre propriété `z`).

Notez que vous remplacez facilement les fonctions des propriétés parents (ici, nous remplaçons `add`) et utilisez toujours la fonctionnalité de la super classe dans vos propriétés (en utilisant la syntaxe `super`).

### Statiques
Les classes TypeScript prennent en charge les propriétés `static` qui sont partagées par toutes les instances de la classe. Un endroit naturel pour les mettre (et y accéder) est sur la classe elle-même et c'est ce que fait TypeScript :

```ts
class Something {
    static instances = 0;
    constructor() {
        Something.instances++;
    }
}

var s1 = new Something();
var s2 = new Something();
console.log(Something.instances); // 2
```

Vous pouvez avoir des propriétés statiques ainsi que des fonctions statiques.

### Modificateurs d'accès
TypeScript prend en charge les modificateurs d'accès `public`,`private` et `protected` qui déterminent l'accessibilité d'une propriété de `class` comme indiqué ci-dessous :

| accessible dans     | `public` | `protected` | `private` |
|---------------------|----------|-------------|-----------|
| classes             | oui      | oui         | oui       |
| classes enfants     | oui      | oui         | non       |
| instances de classe | oui      | non         | non       |


Si aucun modificateur d'accès n'est spécifié, il est implicitement `public` car il correspond à la nature *pratique* de JavaScript 🌹.

Notez qu'à l'exécution (dans le JS généré) ceux-ci n'ont aucune signification mais vous donneront des erreurs durant le temps de compilation si vous les utilisez incorrectement. Un exemple est illustré ci-dessous :

```ts
class FooBase {
    public x: number;
    private y: number;
    protected z: number;
}

// EFFET SUR LES INSTANCES
var foo = new FooBase();
foo.x; // okay
foo.y; // ERROR : private
foo.z; // ERROR : protected

// EFFET SUR LES ENFANTS
class FooChild extends FooBase {
    constructor() {
      super();
        this.x; // okay
        this.y; // ERROR: private
        this.z; // okay
    }
}
```

Comme toujours, ces modificateurs fonctionnent pour les propriétés et les fonctions.

### Abstract
`abstract` peut être considéré comme un modificateur d'accès. Nous le présentons séparément car contrairement aux modificateurs mentionnés précédemment, il peut être sur une `classe` ainsi que sur n'importe quel membre de la classe. Avoir un modificateur `abstract` signifie principalement qu'une telle fonctionnalité *ne peut pas être directement invoquée* et qu'une classe enfant doit fournir la fonctionnalité.

* Les **classes** `abstract` ne peuvent pas être instanciées directement. Au lieu de cela, l'utilisateur doit créer une `classe` qui hérite de la `abstract class`.
* Les **membres** `abstract` ne peuvent pas être accessibles directement et une classe enfant doit fournir la fonctionnalité.

### Le constructeur est facultatif

La classe n'a pas besoin d'avoir de constructeur, par exemple ce qui suit est parfaitement valide.

```ts
class Foo {}
var foo = new Foo();
```

### Définir à l'aide du constructeur

Avoir un membre dans une classe et l'initialiser comme ci-dessous :

```ts
class Foo {
    x: number;
    constructor(x:number) {
        this.x = x;
    }
}
```
C'est un modèle si courant que TypeScript fournit un raccourci où vous pouvez préfixer le membre avec un *modificateur d'accès* et il est automatiquement déclaré dans la classe et copié à partir du constructeur. Ainsi, l'exemple précédent peut être réécrit comme (notice `public x: number`) :

```ts
class Foo {
    constructor(public x:number) {
    }
}
```

### Initialiseur de propriété
Il s'agit d'une fonctionnalité intéressante prise en charge par TypeScript (à partir d'ES7 en fait). Vous pouvez initialiser n'importe quel membre de la classe en dehors du constructeur de classe, utile pour fournir la valeur par défaut (notice `members = []`)

```ts
class Foo {
    members = [];  // Initialiser directement
    add(x) {
        this.members.push(x);
    }
}
```
