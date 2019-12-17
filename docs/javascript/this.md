## this

Tout accès au mot-clé `this` dans une fonction est contrôlé par la façon dont la fonction est réellement appelée. Il est communément appelé le "contexte d'appel".

Voici un exemple :

```ts
function foo() {
  console.log(this);
}

foo(); // déconnecte le global par ex. `window` dans les navigateurs
let bar = {
  foo
}
bar.foo(); // Déconnecte `bar` parce que `foo` a été appelé sur `bar`
```

Soyez donc conscient de votre utilisation de `this`. Si vous voulez déconnecter `this` dans une classe à partir du contexte d'appel, utilisez une fonction fléchée [plus à ce sujet plus tard][arrow].

[arrow]:../arrow-functions.md
