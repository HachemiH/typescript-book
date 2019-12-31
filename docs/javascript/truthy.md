## Truthy

JavaScript a un concept de `truthy`, c'est-à-dire que les choses qui s'évaluent comme `true` le seraient dans certaines positions (par exemple, les conditions `if` et les opérateurs booléens `&&` et `||`). Les choses suivantes sont truthy en JavaScript. Un exemple est tout nombre autre que `0`, par exemple :

```ts
if (123) { // Sera traité comme `true`
  console.log('Any number other than 0 is truthy');
}
```

Quelque chose qui n'est pas vrai s'appelle `falsy`.

Voici un tableau pratique pour vos références.

| Type de variable                                             | Quand c'est *falsy* | Quand c'est *truthy*             |
|--------------------------------------------------------------|---------------------|----------------------------------|
| `boolean`                                                    | `false`             | `true`                           |
| `string`                                                     | `''` (string vide)  | toute autre chaîne de caractères |
| `number`                                                     | `0`  `NaN`          | toute autre chaîne de caractères |
| `null`                                                       | toujours            | jamais                           |
| `undefined`                                                  | toujours            | jamais                           |
| Tout autre objet, y compris des objets vides comme `{}`,`[]` | jamais              | toujours                         |


### Être explicite

> Le pattern `!!` 

Très souvent, il est utile d'expliciter que l'intention est de traiter la valeur comme un `boolean` et de la convertir en un *vrai booléen* (l'un de `true` | `false`). Vous pouvez facilement convertir des valeurs en un vrai booléen en les préfixants avec `!!` par exemple `!!foo`. C'est juste `!` Utilisé *deux fois*. Le premier `!` convertit la variable (dans ce cas `foo`) en booléen mais inverse la logique (*truthy* -`!`> `false`, *falsy* -`!`> `true`). Le second le bascule à nouveau pour correspondre à la nature de l'objet d'origine (par exemple *truthy* -`!`> `false` -`!`> `true`).


Il est courant d'utiliser ce modèle dans de nombreux endroits, par exemple :

```js
// Variables directes
const hasName = !!name;

// En tant que propriétés d'objets
const someObj = {
  hasName: !!name
}

// par exemple dans ReactJS JSX
{!!someName && <div>{someName}</div>}
```
