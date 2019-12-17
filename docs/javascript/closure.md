## Closure

La meilleure chose que JavaScript ait jamais eu, sont les closures. Une fonction en JavaScript a accès à toutes les variables définies dans le contexte externe. Les closures sont mieux expliquées avec des exemples :

```ts
function outerFunction(arg) {
    var variableInOuterFunction = arg;

    function bar() {
        console.log(variableInOuterFunction); // Accéder à une variable depuis le contexte externe
    }

    // Appelez la fonction locale pour démontrer qu'elle a accès à arg
    bar();
}

outerFunction("hello closure"); // logs hello closure!
```

Vous pouvez voir que la fonction interne a accès à une variable (variableInOuterFunction) à partir du contexte externe. Les variables de la fonction externe ont été fermées par (ou liées dans) la fonction interne. D'où le terme **closure** (fermeture). Le concept en lui-même est assez simple et assez intuitif.

Maintenant, la partie impressionnante : la fonction interne peut accéder aux variables du contexte externe *même après le retour de la fonction externe*. En effet, les variables sont toujours liées dans la fonction interne et ne dépendent pas de la fonction externe. Regardons à nouveau un exemple :

```ts
function outerFunction(arg) {
    var variableInOuterFunction = arg;
    return function() {
        console.log(variableInOuterFunction);
    }
}

var innerFunction = outerFunction("hello closure!");

// Notez que outerFunction a été retournée
innerFunction(); // logs hello closure!
```

### Raison pour laquelle c'est génial
Il vous permet de composer des objets facilement, par exemple le pattern revealing module : 

```ts
function createCounter() {
    let val = 0;
    return {
        increment() { val++ },
        getVal() { return val }
    }
}

let counter = createCounter();
counter.increment();
console.log(counter.getVal()); // 1
counter.increment();
console.log(counter.getVal()); // 2
```

À un niveau élevé, c'est aussi ce qui rend possible quelque chose comme Node.js (ne vous inquiétez pas si ça ne percute pas dans votre cerveau en ce moment. Ça finira par le faire 🌹)

```ts
// Pseudo code to explain the concept
server.on(function handler(req, res) {
    loadData(req.id).then(function(data) {
        // le `res` a été fermé et est disponible
        res.send(data);
    })
});
```
