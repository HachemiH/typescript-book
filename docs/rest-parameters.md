### Les paramètres Rest 
Les paramètres Rest (indiqués par `...argumentName` pour le dernier argument) vous permettent d'accepter rapidement plusieurs arguments dans votre fonction et de les obtenir sous forme de tableau. Ceci est démontré dans l'exemple ci-dessous.

```ts
function iTakeItAll(first, second, ...allOthers) {
    console.log(allOthers);
}
iTakeItAll('foo', 'bar'); // []
iTakeItAll('foo', 'bar', 'bas', 'qux'); // ['bas','qux']
```

Les paramètres Rest peuvent être utilisés dans n'importe quelle fonction, que ce soit `function`/`()=>`/`class member`.
