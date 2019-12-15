# Votre JavaScript est TypeScript

Il y avait (et continuera d'être) beaucoup de concurrents dans les compilateurs *quelques syntaxes* vers *JavaScript*. TypeScript est différent d'eux en ce que *Votre JavaScript est TypeScript*. Voici un schéma :

![JavaScript is TypeScript](https://raw.githubusercontent.com/basarat/typescript-book/master/images/venn.png)

Cependant, cela signifie que *vous devez apprendre JavaScript* (la bonne nouvelle est *vous devez **seulement** apprendre JavaScript*). TypeScript standardise simplement toutes les façons dont vous fournissez *une bonne documentation* sur JavaScript.

* Le simple fait de vous donner une nouvelle syntaxe n'aide pas à détecter les bogues - mais peut vous aider à écrire des bogues plus propres/moins nombreux (par exemple CoffeeScript).
* Créer un nouveau langage vous éloigne trop de vos runtimes et communautés - mais pourrait vous aider plus facilement s'il est déjà familier (par exemple Dart - plus proche pour les développeurs Java/C#).

TypeScript est juste du JavaScript avec de la documentation.

> JSNext est ouvert à l'interprétation - tout ce qui est proposé pour la prochaine version de JS ne parvient pas aux navigateurs. TypeScript ne prend en charge les propositions qu'une fois qu'elles ont atteint le [stage 3](https://tc39.es/process-document/).

## Améliorer JavaScript

TypeScript essaiera de vous protéger contre des parties de JavaScript qui n'ont jamais fonctionné (vous n'avez donc pas besoin de vous souvenir de ce genre de choses) :

```ts
[] + []; // JavaScript vous donnera "" (ce qui n'a pas de sens), TypeScript affichera une erreur

//
// d'autres choses absurdes en JavaScript
// - ne donnant pas d'erreur d'exécution (rendant le débogage difficile)
// - mais TypeScript donnera une erreur pendant la compilation (rendant le débogage inutile)
//
{} + []; // JS : 0, TS Error
[] + {}; // JS : "[object Object]", TS Error
{} + {}; // JS : NaN or [object Object][object Object] depending upon browser, TS Error
"hello" - 1; // JS : NaN, TS Error

function add(a,b) {
  return
    a + b; // JS : undefined, TS Error 'unreachable code detected'
}
```

Essentiellement, TypeScript est un linter de JavaScript. Il fait juste mieux que d'autres linters qui n'ont pas *d'informations de type*.

## Vous devez encore apprendre JavaScript

Cela dit, TypeScript est très pragmatique sur le fait que *vous écrivez en JavaScript* donc il y a des choses sur JavaScript que vous devez encore savoir afin de ne pas être pris au dépourvu. Discutons-en ensuite.

> Note : TypeScript est un sur-ensemble de JavaScript. Juste avec une documentation qui peut être utilisée par les compilateurs/IDE ;)
