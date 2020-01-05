* [Arrow Functions](#arrow-functions)
* [Tip: Arrow Function Need](#tip-arrow-function-need)
* [Tip: Arrow Function Danger](#tip-arrow-function-danger)
* [Tip: Libraries that use `this`](#tip-arrow-functions-with-libraries-that-use-this)
* [Tip: Arrow Function inheritance](#tip-arrow-functions-and-inheritance)
* [Tip: Quick object return](#tip-quick-object-return)

### Fonctions Fléchées

On l'appelle affectueusement la *grosse flèche* (parce que `->` est une flèche fine et `=>` est une grosse flèche) et aussi appelée *fonction lambda* (à cause d'autres langages). Une autre caractéristique couramment utilisée est la grosse fonction fléchée `()=> something`. La motivation pour une *grosse flèche* est :
1. Vous n'avez pas besoin de continuer à taper `function`
2. Il capture lexicalement le sens de `this`
2. Il capture lexicalement le sens de `arguments`

Pour un langage qui prétend être fonctionnel, en JavaScript, vous avez tendance à taper beaucoup `function`. La grosse flèche facilite la création d'une fonction
```ts
var inc = (x)=>x+1;
```
`this` a toujours été un point sensible dans JavaScript. Comme l'a dit un sage "je déteste JavaScript car il a tendance à perdre trop facilement le sens de `this`". Les grosses flèches corrigent cela en capturant la signification de `this` dans le contexte environnant. Considérez cette classe JavaScript pure :

```ts
function Person(age) {
    this.age = age;
    this.growOld = function() {
        this.age++;
    }
}
var person = new Person(1);
setTimeout(person.growOld,1000);

setTimeout(function() { console.log(person.age); },2000); // 1, aurait du être 2
```
Si vous exécutez ce code dans le navigateur, `this` dans la fonction va pointer vers `window` parce que `window` va être ce qui exécute la fonction `growOld`. Le correctif consiste à utiliser une fonction fléchée :
```ts
function Person(age) {
    this.age = age;
    this.growOld = () => {
        this.age++;
    }
}
var person = new Person(1);
setTimeout(person.growOld,1000);

setTimeout(function() { console.log(person.age); },2000); // 2
```
La raison pour laquelle cela fonctionne est que la référence à `this` est capturée par la fonction fléchée depuis l'extérieur du corps de la fonction. Cela équivaut au code JavaScript suivant (ce que vous écririez vous-même si vous n'aviez pas TypeScript) :
```ts
function Person(age) {
    this.age = age;
    var _this = this;  // capture this
    this.growOld = function() {
        _this.age++;   // utilise le this capturé
    }
}
var person = new Person(1);
setTimeout(person.growOld,1000);

setTimeout(function() { console.log(person.age); },2000); // 2
```
Notez que puisque vous utilisez TypeScript, vous pouvez être encore plus doux dans la syntaxe et combiner des flèches avec des classes :
```ts
class Person {
    constructor(public age:number) {}
    growOld = () => {
        this.age++;
    }
}
var person = new Person(1);
setTimeout(person.growOld,1000);

setTimeout(function() { console.log(person.age); },2000); // 2
```

> [Une vidéo sympa sur ce pattern 🌹](https://egghead.io/lessons/typescript-make-usages-of-this-safe-in-class-methods)

#### Astuce: Besoin de fonction fléchée
Au-delà de la syntaxe concise, vous n'avez *besoin* d'utiliser la grosse flèche que si vous allez donner la fonction à quelqu'un d'autre à appeler. Effectivement :
```ts
var growOld = person.growOld;
// Plus tard, quelqu'un d'autre l'appelle :
growOld();
```
Si vous allez l'appeler vous-même, c'est-à-dire
```ts
person.growOld();
```
alors `this` va être le contexte d'appel correct (dans cet exemple `person`).

#### Astuce : Danger de la fonction fléchée

En fait, si vous voulez que `this` *soit le contexte d'appel* vous ne devez *pas utiliser la fonction fléchée*. C'est le cas des callbacks utilisés par des bibliothèques comme jquery, underscore, mocha et autres. Si la documentation mentionne des fonctions sur `this`, vous devriez probablement simplement utiliser une `function` au lieu d'une grosse flèche. De même, si vous prévoyez d'utiliser `arguments`, n'utilisez pas de fonction fléchée.

#### Astuce: les fonctions fléchées avec les bibliothèques qui utilisent `this`
De nombreuses bibliothèques le font par exemple `jQuery` iterables (un exemple https://api.jquery.com/jquery.each/) utilisera `this` pour vous passer l'objet sur lequel il est en train d'itérer. Dans ce cas, si vous souhaitez accéder au `this` passé de la bibliothèque ainsi qu'au contexte environnant, utilisez simplement une variable temporaire comme `_self` comme vous le feriez en l'absence de fonctions fléchées.

```ts
let _self = this;
something.each(function() {
    console.log(_self); // la valeur de portée lexicale
    console.log(this); // la valeur passée de la librairie
});
```

#### Astuce : fonctions fléchées et héritage
Les fonctions fléchées comme propriétés sur les classes fonctionnent bien avec l'héritage :

```ts
class Adder {
    constructor(public a: number) {}
    add = (b: number): number => {
        return this.a + b;
    }
}
class Child extends Adder {
    callAdd(b: number) {
        return this.add(b);
    }
}
// Demo to show it works
const child = new Child(123);
console.log(child.callAdd(123)); // 246
```

Cependant, ils ne fonctionnent pas avec le mot clé `super` lorsque vous essayez de remplacer la fonction dans une classe enfant. Les propriétés continuent sur `this`. Puisqu'il n'y a qu'un seul `this` ces fonctions ne peuvent pas participer à un appel à `super` (`super` ne fonctionne que sur les membres du prototype). Vous pouvez facilement la contourner en créant une copie de la méthode avant de la remplacer chez l'enfant.

```ts
class Adder {
    constructor(public a: number) {}
    // Cette fonction est désormais sûre à transmettre
    add = (b: number): number => {
        return this.a + b;
    }
}

class ExtendedAdder extends Adder {
    // Créer une copie du parent avant de créer le nôtre
    private superAdd = this.add;
    // Now create our override
    add = (b: number): number => {
        return this.superAdd(b);
    }
}
```

### Astuce: retour rapide d'objet

Parfois, vous avez besoin d'une fonction qui renvoie simplement un objet littéral simple. Cependant, quelque chose comme

```ts
// MAUVAISE FAÇON DE LE FAIRE
var foo = () => {
    bar: 123
};
```
est analysé comme un *bloc* contenant un *Label JavaScript* par les runtimes JavaScript (à cause de la spécification JavaScript).

>  Si cela n'a pas de sens, ne vous inquiétez pas, car vous obtenez une belle erreur de compilation de TypeScript disant "unused label"(label inutilisé) de toute façon. Les labels sont une ancienne fonctionnalité JavaScript (et la plupart du temps inutilisée) que vous pouvez ignorer en tant que GOTO moderne (considérée comme mauvaise par les développeurs expérimentés 🌹)

Vous pouvez le corriger en entourant l'objet littéral avec `()` :

```ts
// Correct 🌹
var foo = () => ({
    bar: 123
});
```
