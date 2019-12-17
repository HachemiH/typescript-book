## Null et Undefined

> [Vidéo Youtube sur le sujet](https://www.youtube.com/watch?v=kaUfBNzuUAI)

JavaScript (et par extension TypeScript) a deux types basiques : `null` et `undefined`. Ils sont *destinés* à signifier différentes choses :

* Quelque chose n'a pas été initialisé : `undefined`.
* Quelque chose n'est actuellement pas disponible : `null`.


### Vérification

Le fait est que vous devrez faire face aux deux. Fait intéressant en JavaScript avec `==`, `null` et `undefined` ne sont égaux que l'un à l'autre :

```ts
// Les valeurs null et undefined sont seulement `==` à elles-mêmes et l'une pour l'autre :
console.log(null == null); // true (bien sûr)
console.log(undefined == undefined); // true (bien sûr)
console.log(null == undefined); // true


// Vous n'avez pas à vous soucier des valeurs falsy lors de cette vérification
console.log(0 == undefined); // false
console.log('' == undefined); // false
console.log(false == undefined); // false
```
Privilégiez `== null` pour vérifier à la fois `undefined` ou `null`. Vous ne voulez généralement pas faire de distinction entre les deux.

```ts
function foo(arg: string | null | undefined) {
  if (arg != null) {
    // arg doit être une chaîne de caractères car `!=` exclut à la fois null et undefined.
  }
}
```

> Vous pouvez également faire `== undefined`, mais `== null` est plus conventionnel/plus court.

Il existe une exception, les valeurs `undefined` au niveau racine dont nous discutons ensuite.

### Vérification de undefined au niveau racine 

Vous vous souvenez comment je vous ai conseillé d'utiliser `== null` ? Bien sûr que oui (puisque je viens de le dire ^). Ne l'utilisez pas pour les choses au niveau racine. En mode strict, si vous utilisez `foo` et que `foo` n'est pas défini, vous obtenez une `ReferenceError` **exception** et toute la pile d'appels se déroule.

> Vous devriez utiliser le mode strict ... et en fait le compilateur TS l'insérera pour vous si vous utilisez des modules ... Nous verrons cela plus tard dans le livre afin que vous n'ayez pas à être explicite à ce sujet :)

Donc, pour vérifier si une variable est définie ou non à un niveau *global*, vous devriez utiliser normalement `typeof` :

```ts
if (typeof someglobal !== 'undefined') {
  // someglobal est désormais sûr à utiliser
  console.log(someglobal);
}
```

### limiter l'utilisation explicite de `undefined`
Parce que TypeScript vous donne la possibilité de * documenter * vos structures séparément des valeurs au lieu de choses comme :
```ts
function foo(){
  // if Something
  return {a:1,b:2};
  // else
  return {a:1,b:undefined};
}
```
Vous devez utiliser une annotation de type :
```ts
function foo():{a:number,b?:number}{
  // if Something
  return {a:1,b:2};
  // else
  return {a:1};
}
```

### Callbacks de style Node
Les fonctions callback de style Node (par exemple `(err, somethingElse) => {/ * quelque chose * /}`) sont généralement appelées avec `err` réglé sur `null` s'il n'y a pas d'erreur. En général, vous utilisez simplement une vérification truthy pour cela :

```ts
fs.readFile('someFile', 'utf8', (err,data) => {
  if (err) {
    // do something
  } else {
    // no error
  }
});
```
Lors de la création de vos propres APIs, il est *correct* d'utiliser dans ce cas `null` pour des raisons de cohérence. En toute sincérité pour vos propres API, vous devriez regarder les promesses, dans ce cas, vous n'avez en fait pas besoin de vous soucier des valeurs d'erreur absentes (vous les gérez avec `.then` vs `.catch`).

### N'utilisez pas `undefined` comme moyen de dénoter *la validité*

Par exemple, une fonction horrible comme celle-ci :

```ts
function toInt(str: string) {
  return str ? parseInt(str) : undefined;
}
```
Peut être beaucoup mieux écrit comme ceci :
```ts
function toInt(str: string): { valid: boolean, int?: number } {
  const int = parseInt(str);
  if (isNaN(int)) {
    return { valid: false };
  }
  else {
    return { valid: true, int };
  }
}
```

### JSON et sérialisation

La norme JSON prend en charge l'encodage `null` mais pas` undefined`. Lors de l'encodage JSON d'un objet avec un attribut qui est `null`, l'attribut sera inclus avec sa valeur nulle, tandis qu'un attribut avec une valeur `undefined` sera entièrement exclu.

```ts
JSON.stringify({willStay: null, willBeGone: undefined}); // {"willStay":null}
```

Par conséquent, les bases de données basées sur JSON peuvent prendre en charge des valeurs `null` mais pas les valeurs `undefined`. Étant donné que les attributs définis sur `null` sont encodés, vous pouvez transmettre l'intention d'effacer un attribut en définissant sa valeur sur `null` avant d'encoder et de transmettre l'objet à une cible distante.

La définition de valeurs d'attribut à `undefined` peut permettre d'économiser sur les coûts de stockage et de transmission, car les noms d'attribut ne seront pas encodés. Cependant, cela peut compliquer la sémantique de nettoyage des valeurs par rapport aux valeurs absentes.

### Dernières pensées
L'équipe TypeScript n'utilise pas `null` : [TypeScript coding guidelines](https://github.com/Microsoft/TypeScript/wiki/Coding-guidelines#null-and-undefined) et cela n'a causé aucun problème. Douglas Crockford pense que [`null` est une mauvaise idée](https://www.youtube.com/watch?v=PSGEjv3Tqo0&feature=youtu.be&t=9m21s) et nous devrions tous simplement utiliser `undefined`.

Cependant, les bases de code de style NodeJS utilisent `null` pour les arguments d'erreur en standard car il indique `Quelque chose n'est pas disponible actuellement`. Personnellement, je ne me soucie pas de faire la distinction entre les deux, car la plupart des projets utilisent des bibliothèques avec des opinions différentes et excluent simplement les deux avec `== null`.
