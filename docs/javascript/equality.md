## Égalité

Une chose à laquelle il faut faire attention en JavaScript est la différence entre `==` et `===`. Comme JavaScript essaie d'être résilient contre les erreurs de programmation `==` essaie de faire une coercition de type entre deux variables, par exemple convertit une chaîne en nombre afin que vous puissiez comparer avec un nombre comme indiqué ci-dessous :

```js
console.log(5 == "5"); // true   , TS Error
console.log(5 === "5"); // false , TS Error
```

Cependant, les choix que JavaScript fait ne sont pas toujours idéaux. Par exemple, dans l'exemple ci-dessous, la première instruction est fausse car `""` et `"0"` sont tous les deux des chaînes de caractères mais ne sont clairement pas égaux. Cependant, dans le deuxième cas, `0` et la chaîne vide (`""`) sont falsy (c'est-à-dire qu'ils se comportent comme `false`) et sont donc égales par rapport à un `==`. Les deux déclarations sont fausses lorsque vous utilisez `===`.

```js
console.log("" == "0"); // false
console.log(0 == ""); // true

console.log("" === "0"); // false
console.log(0 === ""); // false
```

> Notez que `string == number` et `string === number` sont tous deux des erreurs pendant la compilation dans TypeScript, donc vous n'avez normalement pas à vous en préoccuper.

Similaire à `==` vs `===`, il y a `!=` vs `!==`

Donc, ProTip : Utilisez toujours `===` et `!==` sauf pour les vérifications nulles, que nous couvrirons plus tard.

## Égalité structurelle
Si vous voulez comparer deux objets pour l'égalité structurelle, `==`/`===` ne sont ***pas*** suffisants, par exemple :

```js
console.log({a:123} == {a:123}); // False
console.log({a:123} === {a:123}); // False
```
To do such checks use the [deep-equal](https://www.npmjs.com/package/deep-equal) npm package e.g. 

```js
import * as deepEqual from "deep-equal";

console.log(deepEqual({a:123},{a:123})); // True
```

Cependant, très souvent, vous n'avez pas besoin de vérifications approfondies et tout ce dont vous avez vraiment besoin est de vérifier par un `id`, par exemple :

```ts
type IdDisplay = {
  id: string,
  display: string
}
const list: IdDisplay[] = [
  {
    id: 'foo',
    display: 'Foo Select'
  },
  {
    id: 'bar',
    display: 'Bar Select'
  },
]

const fooIndex = list.map(i => i.id).indexOf('foo');
console.log(fooIndex); // 0
```
