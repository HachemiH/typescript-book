* [Getting Started with TypeScript](#getting-started-with-typescript)
* [TypeScript Version](#typescript-version)

# Premiers pas avec TypeScript

TypeScript se compile en JavaScript. JavaScript est ce que vous allez réellement exécuter (dans le navigateur ou sur le serveur). Vous allez donc avoir besoin des éléments suivants :

* Compilateur TypeScript (OSS disponible [dans les sources](https://github.com/Microsoft/TypeScript/) et sur [NPM](https://www.npmjs.com/package/typescript))
* Un éditeur TypeScript (vous pouvez utiliser le bloc-notes si vous le souhaitez mais j'utilise [vscode 🌹](https://code.visualstudio.com/) avec une [extension que j'ai écrit](https://marketplace.visualstudio.com/items?itemName=basarat.god). Aussi [beaucoup d'autres IDES le supportent également]( https://github.com/Microsoft/TypeScript/wiki/TypeScript-Editor-Support))


## Version de TypeScript

Au lieu d'utiliser la version *stable* du compilateur TypeScript, nous présenterons de nombreuses nouveautés dans ce livre qui peuvent ne pas encore être associées à un numéro de version. Je recommande généralement aux gens d'utiliser la version nightly car **la suite de tests du compilateur détecte plus de bugs au fil du temps**.

Vous pouvez l'installer en ligne de commande en tapant :

```
npm install -g typescript@next
```

Et maintenant, la ligne de commande `tsc` sera la plus récente et la meilleure. Divers IDE la prennent également en charge, par exemple :

* Vous pouvez demander à vscode d'utiliser cette version en créant un `.vscode/settings.json` avec le contenu suivant :

```json
{
  "typescript.tsdk": "./node_modules/typescript/lib"
}
```

## Obtenir le code source
La source de ce livre est disponible dans le repository Github https://github.com/basarat/typescript-book/tree/master/code la plupart des exemples de code peuvent être copiés dans vscode et vous pouvez jouer avec eux tels quels. Pour les exemples de code qui nécessitent une configuration supplémentaire (par exemple, les modules npm), nous vous ajouterons des liens à l'exemple de code avant de présenter le code, par exemple :

`this/will/be/the/link/to/the/code.ts`
```ts
// Ce sera le code en discussion
```

Avec une installation de développement prête, passons maintenant à la syntaxe TypeScript.
