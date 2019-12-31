### const

`const` est un ajout très apprécié offert par ES6/TypeScript. Il vous permet d'être immuable avec des variables. C'est bon à partir d'une documentation ainsi que d'une perspective d'exécution. Pour utiliser const, remplacez simplement `var` par `const` :

```ts
const foo = 123;
```

> La syntaxe est bien meilleure (à mon humble avis) que d'autres langages qui forcent l'utilisateur à taper quelque chose comme `let constant foo`, c'est-à-dire une variable + un spécificateur de comportement.

`const` est une bonne pratique pour la lisibilité et la maintenabilité et évite d'utiliser des *littéraux magiques* par exemple :

```ts
// Low readability
if (x > 10) {
}

// Better!
const maxRows = 10;
if (x > maxRows) {
}
```

#### les déclarations const doivent être initialisées
Voici une erreur de compilation :

```ts
const foo; // ERROR: const declarations must be initialized
```

#### Le côté gauche de l'affectation ne peut pas être une constante
Les constantes sont immuables après la création, donc si vous essayez de les affecter à une nouvelle valeur, cela donnera erreur de compilation :

```ts
const foo = 123;
foo = 456; // ERROR: Left-hand side of an assignment expression cannot be a constant
```

#### Portée de block 
Un `const` a une portée de bloc comme nous l'avons vu avec [`let`](./let.md) :

```ts
const foo = 123;
if (true) {
    const foo = 456; // Autorisé car c'est une nouvelle variable limitée à ce bloc `if`
}
```

#### Immuabilité profonde
Un `const` fonctionne également avec les littéraux d'objet, ce qui concerne la protection de la *référence* de la variable :

```ts
const foo = { bar: 123 };
foo = { bar: 456 }; // ERROR : Left hand side of an assignment expression cannot be a constant
```

Cependant, il permet toujours aux sous-propriétés des objets d'être mutées, comme indiqué ci-dessous :

```ts
const foo = { bar: 123 };
foo.bar = 456; // Allowed!
console.log(foo); // { bar: 456 }
```

#### Préférez const

Utilisez toujours `const`, sauf si vous prévoyez d'initialiser une variable paresseusement, ou de faire une réaffectation (utilisez `let` pour ces cas).
