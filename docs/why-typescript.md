# Pourquoi TypeScript
Il y a deux objectifs principaux pour TypeScript :
* Fournir un *système de type facultatif* pour JavaScript.
* Fournir les fonctionnalités planifiées des futures éditions de JavaScript aux moteurs JavaScript actuels

Le désir de ces objectifs est motivé ci-dessous.

## Le système de type TypeScript

Vous vous demandez peut-être "**Pourquoi ajouter des types à JavaScript ?**"

Les types ont prouvé leur capacité à améliorer la qualité et la compréhensibilité du code. De grandes équipes (Google, Microsoft, Facebook) sont continuellement parvenues à cette conclusion. Plus précisément :

* Les types augmentent votre agilité lors de la refactorisation. *Il est préférable pour le compilateur de détecter les erreurs que de faire échouer les choses au moment de l'exécution*.
* Les types sont l'une des meilleures formes de documentation que vous puissiez avoir. *La signature de la fonction est un théorème et le corps de la fonction en est la preuve*.

Cependant, les types ont une façon d'être inutilement cérémonieux. TypeScript est très particulier pour maintenir la barrière à l'entrée aussi basse que possible. Voici comment :

### Votre JavaScript est TypeScript
TypeScript fournit une sécurité de type pendant la compilation pour votre code JavaScript. Ce n'est pas une surprise étant donné son nom. Le truc sympa est que les types sont complètement facultatifs. Votre fichier de code JavaScript `.js` peut être renommé en fichier `.ts` et TypeScript vous rendra toujours l'équivalent `.js` valide du fichier JavaScript d'origine. TypeScript est *intentionnellement* et strictement un sur-ensemble de JavaScript avec vérification de type facultative.

### Les types peuvent être implicites
TypeScript essaiera de déduire autant d'informations de type que possible afin de vous offrir une sécurité de type avec un coût de productivité minimal lors du développement du code. Par exemple, dans l'exemple suivant, TypeScript saura que `foo` est du type `number` ci-dessous et donnera une erreur sur la deuxième ligne comme indiqué :

```ts
var foo = 123;
foo = '456'; // Error: cannot assign `string` to `number`

// Is foo a number or a string?
```
Cette inférence de type est bien motivée. Si vous faites des choses comme indiqué dans cet exemple, alors, dans le reste de votre code, vous ne pouvez pas être certain que `foo` est un `number` ou un `string`. Ces problèmes surviennent souvent dans les grandes bases de code multi-fichiers. Nous approfondirons les règles d'inférence de type plus tard.

### Les types peuvent être explicites
Comme nous l'avons mentionné précédemment, TypeScript inférera autant que possible en toute sécurité. Cependant, vous pouvez utiliser des annotations pour :
1. Aider le compilateur, et surtout documenter du code pour le prochain développeur qui devra le lire (qui pourrait être vous dans le futur !).
1. Vous assurer que ce que le compilateur voit est ce que vous pensiez qu'il devrait voir. C'est votre compréhension du code qui correspond à une analyse algorithmique du code (effectuée par le compilateur).

TypeScript utilise des annotations de type postfix, très populaires dans d'autres langages (par exemple ActionScript et F#).

```ts
var foo: number = 123;
```
Donc, si vous écrivez du code incorrect, le compilateur provoquera une erreur, par exemple :

```ts
var foo: number = '123'; // Error: cannot assign a `string` to a `number`
```

Nous discuterons des détails de toutes les syntaxes d'annotation prises en charge par TypeScript dans un chapitre ultérieur.

### Les types sont structurels
Dans certains langages (en particulier ceux nominalement typés), la frappe statique entraîne une cérémonie inutile car même si *vous savez* que le code fonctionnera correctement, la sémantique du langage vous oblige à copier des éléments. Voilà pourquoi des trucs comme [automapper pour C#](http://automapper.org/) est *vital* pour C#. Dans TypeScript, parce que nous voulons vraiment que ce soit facile pour les développeurs JavaScript avec une surcharge cognitive minimale, les types sont *structurels*. Cela signifie que le *duck typing* est une construction de langage de première classe. Prenons l'exemple suivant. La fonction `iTakePoint2D` acceptera tout ce qu'elle contient (`x` et `y`) qu'elle attend comme un ensemble :

```ts
interface Point2D {
    x: number;
    y: number;
}
interface Point3D {
    x: number;
    y: number;
    z: number;
}
var point2D: Point2D = { x: 0, y: 10 }
var point3D: Point3D = { x: 0, y: 10, z: 20 }
function iTakePoint2D(point: Point2D) { /* do something */ }

iTakePoint2D(point2D); // exact match okay
iTakePoint2D(point3D); // extra information okay
iTakePoint2D({ x: 0 }); // Error: missing information `y`
```

### Les erreurs de frappe n'empêchent pas l'émission de JavaScript
Pour vous permettre de migrer facilement votre code JavaScript vers TypeScript, même s'il y a des erreurs de compilation, par défaut TypeScript *émettra du JavaScript* valide du mieux qu'il peut, par exemple :

```ts
var foo = 123;
foo = '456'; // Error: cannot assign a `string` to a `number`
```

will emit the following js:

```ts
var foo = 123;
foo = '456';
```

Vous pouvez donc mettre à niveau progressivement votre code JavaScript vers TypeScript. C'est très différent du nombre d'autres compilateurs de langage et c'est une autre raison de passer à TypeScript.

### Les types peuvent être ambiants
L'un des principaux objectifs de conception de TypeScript était de vous permettre d'utiliser facilement et en toute sécurité les bibliothèques JavaScript existantes dans TypeScript. TypeScript le fait au moyen de *déclaration*. TypeScript vous fournit une échelle mobile de combien d'efforts vous voulez mettre dans vos déclarations, plus vous mettez d'efforts, plus vous obtenez de sécurité de type + d'intelligence de code. Notez que les définitions de la plupart des bibliothèques JavaScript populaires ont déjà été écrites pour vous par la [communauté DefinitelyTyped](https://github.com/borisyankov/DefinitelyTyped) donc pour la plupart des usages :

1. Le fichier de définition existe déjà.
2. Ou à tout le moins, vous disposez déjà d'une vaste liste de modèles de déclaration TypeScript bien révisés.

Comme exemple rapide de la façon dont vous allez créer votre propre fichier de déclaration, considérez un exemple trivial de [jquery](https://jquery.com/). Par défaut (comme on peut s'y attendre d'un bon code JS), TypeScript s'attend à ce que vous déclariez (c'est-à-dire, utilisez `var` quelque part) avant d'utiliser une variable :
```ts
$('.awesome').show(); // Error: cannot find name `$`
```
Comme solution rapide *vous pouvez dire à TypeScript* qu'il y a bien quelque chose appelé `$` :
```ts
declare var $: any;
$('.awesome').show(); // Okay!
```
Si vous le souhaitez, vous pouvez vous appuyer sur cette définition de base et fournir plus d'informations pour vous protéger contre les erreurs :
```ts
declare var $: {
    (selector:string): any;
};
$('.awesome').show(); // Okay!
$(123).show(); // Error: selector needs to be a string
```

Nous discuterons en détails de la création de définitions TypeScript pour le JavaScript existant une fois que vous en saurez plus sur TypeScript (par exemple, des choses comme `interface` et `any`).

## Futur JavaScript => Maintenant
TypeScript fournit un certain nombre de fonctionnalités prévues dans ES6 pour les moteurs JavaScript actuels (qui ne prennent en charge que ES5, etc.). L'équipe TypeScript ajoute activement ces fonctionnalités et cette liste ne fera qu'augmenter avec le temps et nous couvrirons cela dans sa propre section. Mais tout comme un spécimen, voici un exemple de classe :

```ts
class Point {
    constructor(public x: number, public y: number) {
    }
    add(point: Point) {
        return new Point(this.x + point.x, this.y + point.y);
    }
}

var p1 = new Point(0, 10);
var p2 = new Point(10, 20);
var p3 = p1.add(p2); // { x: 10, y: 30 }
```

et la belle grosse fonction fléchée :

```ts
var inc = x => x+1;
```

### Résumé
Dans cette section, nous vous avons fourni les objectifs de motivation et de conception de TypeScript. Cela posé, nous pouvons maintenant creuser dans les moindres détails de TypeScript.

[](Interfaces are open ended)
[](Type Inferernce rules)
[](Cover all the annotations)
[](Cover all ambients : also that there are no runtime enforcement)
[](.ts vs. .d.ts)
