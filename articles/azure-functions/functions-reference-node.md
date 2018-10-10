---
title: Information de référence pour les développeurs JavaScript sur Azure Functions | Microsoft Docs
description: Découvrez comment développer des fonctions à l’aide de JavaScript.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 03/04/2018
ms.author: glenga
ms.openlocfilehash: 24f7faa0fb111e4e537a7db3f5e1eea709d1ca59
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957727"
---
# <a name="azure-functions-javascript-developer-guide"></a>Guide des développeurs JavaScript sur Azure Functions
Ce guide contient des informations sur les complexités de l’écriture de fonctions Azure avec JavaScript.

Une fonction JavaScript est une `function` exportée qui s’exécute quand elle est déclenchée ([les déclencheurs sont configurés dans function.json](functions-triggers-bindings.md)). Chaque fonction reçoit un objet `context` qui est utilisé pour la réception et l’envoi des données de liaison, la journalisation et la communication avec le runtime.

Cet article suppose que vous ayez déjà lu l’article [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md). Il est également recommandé d’avoir suivi un tutoriel sous « Démarrages rapides » pour [créer votre première fonction](functions-create-first-function-vs-code.md).

## <a name="folder-structure"></a>Structure de dossiers

La structure de dossiers requise pour un projet JavaScript est similaire à la structure ci-après. Notez que vous pouvez changer cette structure par défaut : consultez la section [scriptFile](functions-reference-node.md#using-scriptfile) plus loin pour plus d’informations.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
 | - bin
```

À la racine du projet se trouve un fichier [host.json](functions-host-json.md) partagé que vous pouvez utiliser pour configurer l’application de fonction. Chaque fonction a un dossier avec ses propres fichier de code (.js) et fichier de configuration de liaison (function.json).

Les extensions de liaison requises dans la [version 2.x](functions-versions.md) du runtime Functions sont définies dans le fichier `extensions.csproj`, les fichiers de bibliothèque proprement dits se trouvant dans le dossier `bin`. Quand vous développez localement, vous devez [inscrire les extensions de liaison](functions-triggers-bindings.md#local-development-azure-functions-core-tools). Quand vous développez des fonctions dans le portail Azure, cet enregistrement est effectué pour vous.

## <a name="exporting-a-function"></a>Exporter une fonction

Les fonctions JavaScript doivent être exportées au moyen de [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (ou [`exports`](https://nodejs.org/api/modules.html#modules_exports)). Dans le cas par défaut, votre fonction exportée doit être la seule exportation à partir de son fichier, l’exportation nommée `run` ou l’exportation nommée `index`. L’emplacement par défaut de votre fonction est `index.js`, où `index.js` partage le même répertoire parent que le fichier `function.json` correspondant. Notez que le nom du répertoire parent de `function.json` est toujours le nom de votre fonction. 

Pour configurer l’emplacement de fichier et le nom d’exportation de votre fonction, consultez les informations relatives à la [configuration du point d’entrée de votre fonction](functions-reference-node.md#configure-function-entry-point) plus loin.

Le point d’entrée de votre fonction exportée doit toujours prendre un objet `context` en tant que premier paramètre.

```javascript
// You must include a context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
// You can also use 'arguments' to dynamically handle inputs
module.exports = async function(context) {
    context.log('Number of inputs: ' + arguments.length);
    // Iterates through trigger and input binding data
    for (i = 1; i < arguments.length; i++){
        context.log(arguments[i]);
    }
};
```

Les déclencheurs et les liaisons d’entrée (liaisons de `direction === "in"`) peuvent être transmis à la fonction en tant que paramètres. Elles sont transmises à la fonction dans l’ordre dans lequel elles sont définies dans le fichier *function.json*. Vous pouvez également gérer les entrées de manière dynamique en utilisant l’objet [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) JavaScript. Par exemple, si vous avez `function(context, a, b)` et le remplacez par `function(context, a)`, vous pouvez toujours obtenir la valeur `b` dans le code de fonction en faisant référence à `arguments[2]`.

Toutes les liaisons, quelle que soit leur direction, sont également transmises sur l’objet `context` à l’aide de la propriété `context.bindings`.

## <a name="context-object"></a>Objet de contexte
Le runtime utilise un objet `context` pour transmettre des données vers et à partir de votre fonction et vous permettre de communiquer avec le runtime.

L’objet `context` est toujours le premier paramètre d’une fonction et doit toujours être inclus, car il possède des méthodes telles que `context.done` et `context.log` nécessaires pour utiliser correctement le runtime. Vous pouvez nommer l’objet comme vous le souhaitez (par exemple, `ctx` ou `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Propriété context.bindings

```
context.bindings
```
Retourne un objet nommé qui contient toutes vos données d’entrée et de sortie. Par exemple, les définitions de liaison suivantes dans votre *function.json* vous permettent d’accéder au contenu d’une file d’attente à partir de l’objet `context.bindings.myInput` et d’assigner des sorties à une file d’attente à l’aide de `context.bindings.myOutput`.

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Notez que vous pouvez choisir de définir les données de liaison de sortie à l’aide de la méthode `context.done` au lieu de l’objet `context.binding` (voir ci-dessous).

### <a name="contextbindingdata-property"></a>Propriété context.bindingData

```
context.bindingData
```
Retourne un objet nommé qui contient les métadonnées de déclencheur et les données d’appel de fonction (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Pour obtenir un exemple de métadonnées de déclencheur, consultez cet [exemple de hubs d’événement](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>Méthode context.done
```
context.done([err],[propertyBag])
```

Informe le runtime que votre code est terminé. Si votre fonction utilise la déclaration [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) JavaScript (disponible avec 8 nœuds ou plus dans Functions version 2.x), vous n’avez pas besoin d’utiliser `context.done()`. Le rappel `context.done` est appelé de manière implicite.

Si votre fonction n’est pas une fonction asynchrone, **vous devez appeler**  `context.done` pour indiquer au runtime que votre fonction est terminée. Sinon, l’exécution arrivera à expiration.

La méthode `context.done` vous permet de transmettre à la fois une erreur définie par l’utilisateur au runtime et un objet JSON contenant les données de liaison de sortie. Les propriétés transmises à `context.done` remplacent tout ce qui est défini sur l’objet `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Méthode context.log  

```
context.log(message)
```
Vous permet d’écrire dans les journaux de fonction de streaming au niveau de trace par défaut. Des méthodes de journalisation supplémentaires sont disponibles sur `context.log` pour vous permettre d’écrire des journaux de fonction à d’autres niveaux de trace :


| Méthode                 | Description                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | Écrit dans la journalisation du niveau d’erreur, ou à un niveau inférieur.   |
| **warn(_message_)**    | Écrit dans la journalisation du niveau d’avertissement, ou à un niveau inférieur. |
| **info(_message_)**    | Écrit dans la journalisation du niveau d’information, ou à un niveau inférieur.    |
| **verbose(_message_)** | Écrit dans la journalisation du niveau détaillé.           |

L’exemple suivant écrit un journal au niveau de trace d’avertissement :

```javascript
context.log.warn("Something has happened."); 
```
Vous pouvez [configurer le seuil du niveau de trace pour la journalisation](#configure-the-trace-level-for-console-logging) dans le fichier host.json. Pour plus d’informations sur l’écriture de journaux, consultez [Écriture de sorties de trace](#writing-trace-output-to-the-console) plus loin.

Consultez [Supervision des fonctions Azure](functions-monitoring.md) pour en savoir plus sur l’affichage et l’interrogation des journaux de fonction.

## <a name="binding-data-type"></a>Type de données d’une liaison

Pour définir le type de données pour une liaison d’entrée, utilisez la propriété `dataType` dans la définition de la liaison. Par exemple, pour lire le contenu d’une requête HTTP au format binaire, utilisez le type `binary` :

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Les options pour `dataType` sont `binary`, `stream` et `string`.

## <a name="writing-trace-output-to-the-console"></a>Écrire la sortie de trace dans la console 

Dans Functions, vous utilisez les méthodes `context.log` pour écrire la sortie de trace dans la console. Dans Functions v2.x, les sorties de trace via `console.log` sont capturées au niveau de l’application de fonction. Cela signifie que les sorties de `console.log` ne sont pas liées à un appel de fonction spécifique et qu’elles ne sont donc pas affichées dans les journaux d’une fonction spécifique. Elles sont, toutefois, propagées à Application Insights. Dans Functions v1.x, vous ne pouvez pas utiliser `console.log` pour écrire dans la console. 

Lorsque vous appelez `context.log()`, votre message est écrit dans la console au niveau de trace par défaut, qui est le niveau de trace d’_informations_. Le code suivant écrit dans la console au niveau de trace d’informations :

```javascript
context.log({hello: 'world'});  
```

Ce code est équivalent au code ci-dessus :

```javascript
context.log.info({hello: 'world'});  
```

Ce code écrit dans la console au niveau de trace d’erreur :

```javascript
context.log.error("An error has occurred.");  
```

Étant donné que le niveau d’_erreur_ constitue le niveau de trace le plus élevé, cette trace est écrite dans la sortie à tous les niveaux de trace tant que la journalisation est activée.

Toutes les méthodes `context.log` prennent en charge le même format de paramètre que celui pris en charge par la [méthode util.format](https://nodejs.org/api/util.html#util_util_format_format) Node.js. Prenons le code suivant, qui écrit des journaux de fonction en utilisant le niveau de trace par défaut :

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Vous pouvez écrire ce même code au format suivant :

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Configurer le niveau de trace pour la journalisation de la console

Functions vous permet de définir le niveau de trace du seuil pour écrire dans la console, ce qui facilite le contrôle de l’écriture des traces dans la console à partir de vos fonctions. Utilisez la propriété `tracing.consoleLevel` dans le fichier host.json pour définir le seuil de toutes les traces écrites dans la console. Ce paramètre s’applique à toutes les fonctions dans votre Function App. L’exemple suivant définit le seuil de trace permettant d’activer la journalisation détaillée :

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

Les valeurs de **consoleLevel** correspondent aux noms des méthodes `context.log`. Pour désactiver toutes les journalisations de trace dans la console, définissez **consoleLevel** sur _désactivé_. Pour plus d’informations, consultez l’article de référence sur [host.json](functions-host-json.md).

## <a name="http-triggers-and-bindings"></a>Déclencheurs et liaisons HTTP

Les déclencheurs HTTP et webhook ainsi que les liaisons de sortie HTTP utilisent les objets de requête et de réponse pour représenter la messagerie HTTP.  

### <a name="request-object"></a>Objet Requête

L’objet (de demande) `context.req` comporte les propriétés suivantes :

| Propriété      | Description                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Objet qui contient le corps de la demande.               |
| _headers_     | Objet qui contient les en-têtes de la demande.                   |
| _method_      | Méthode HTTP de la demande.                                |
| _originalUrl_ | URL de la demande.                                        |
| _params_      | Objet qui contient les paramètres de routage de la demande. |
| _query_       | Objet qui contient les paramètres de la requête.                  |
| _rawBody_     | Corps du message en tant que chaîne.                           |


### <a name="response-object"></a>Objet Réponse

L’objet (de réponse) `context.res` comporte les propriétés suivantes :

| Propriété  | Description                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Objet qui contient le corps de la réponse.         |
| _headers_ | Objet qui contient les en-têtes de la réponse.             |
| _isRaw_   | Indique que la mise en forme est ignorée pour la réponse.    |
| _statut_  | Code d’état HTTP de la réponse.                     |

### <a name="accessing-the-request-and-response"></a>Accès à la demande et à la réponse 

Quand vous utilisez des déclencheurs HTTP, de nombreuses méthodes vous permettent d’accéder à des objets de demande et de réponse HTTP :

+ À partir des propriétés `req` et `res` sur l’objet `context`. Avec cette méthode, vous pouvez utiliser le modèle classique pour accéder aux données HTTP à partir de l’objet de contexte, au lieu d’utiliser le modèle `context.bindings.name` complet. L’exemple suivant montre comment accéder aux objets `req` et `res` sur `context` :

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ À partir de des liaisons dites d’entrée et de sortie. Avec cette méthode, le déclencheur HTTP et les liaisons fonctionnent de la même manière que toute autre liaison. L’exemple suivant définit l’objet de réponse en utilisant une liaison nommée `response` : 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ _[Réponse uniquement]_  En appelant `context.res.send(body?: any)`. Une réponse HTTP est créée avec l’entrée `body` comme corps de réponse. `context.done()` est appelé de manière implicite.

+ _[Réponse uniquement]_  En appelant `context.done()`. Un type spécial de liaison HTTP renvoie la réponse transmise à la méthode `context.done()`. La liaison de sortie HTTP suivante définit un paramètre de sortie `$return` :

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version"></a>Version de Node

Le tableau suivant montre la version de Node.js qui est utilisée par chaque version majeure du runtime Functions :

| Version de Functions | Version de Node.js | 
|---|---|
| 1.x | 6.11.2 (verrouillée par le runtime) |
| 2.x  | Versions de Node.js _Active LTS_ et _Current_ (8.11.1 et 10.6.0 sont recommandées). Définissez la version à l’aide du [paramètre d’application](functions-how-to-use-azure-function-app-settings.md#settings) WEBSITE_NODE_DEFAULT_VERSION.|

Vous pouvez voir la version que le runtime utilise en vérifiant le paramètre d’application ci-dessus ou en affichant `process.version` à partir de n’importe quelle fonction.

## <a name="dependency-management"></a>Gestion des dépendances
Pour utiliser les bibliothèques de communauté dans votre code JavaScript, comme indiqué dans l’exemple ci-dessous, vous devez vérifier que toutes les dépendances sont installées sur votre application de fonction dans Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

Notez que vous devez définir un fichier `package.json` à la racine de votre application de fonction. Cela permet à toutes les fonctions de l’application de partager les mêmes packages mis en cache, pour des performances optimales. En cas de conflit de version, vous pouvez ajouter un fichier `package.json` dans le dossier d’une fonction spécifique pour le résoudre.  

Vous pouvez installer des packages sur votre application de fonction de deux façons : 

### <a name="deploying-with-dependencies"></a>Déploiement avec des dépendances
1. Installez tous les packages requis localement en exécutant `npm install`.

2. Déployez votre code, puis vérifiez que le dossier `node_modules` est inclus dans le déploiement. 


### <a name="using-kudu"></a>Utilisation de Kudu
1. Accédez à `https://<function_app_name>.scm.azurewebsites.net`

2. Cliquez sur **Console de débogage** > **CMD**.

3. Accédez à `D:\home\site\wwwroot`, puis faites glisser le fichier package.json vers le dossier **wwwroot** dans la partie supérieure de la page.  
    Il existe d’autres manières de télécharger des fichiers dans votre Function App. Pour plus d’informations, consultez [Comment mettre à jour les fichiers du conteneur de fonctions](functions-reference.md#fileupdate). 

4. Une fois le fichier package.json chargé, exécutez la commande `npm install` dans la **console d’exécution à distance Kudu**.  
    Les packages d’actions indiqués dans le fichier package.json sont téléchargés et Function App redémarre.

## <a name="environment-variables"></a>Variables d’environnement
Pour obtenir une variable d’environnement ou une valeur de paramètre d’application, utilisez `process.env`, comme illustré ici dans la fonction `GetEnvironmentVariable` :

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

## <a name="configure-function-entry-point"></a>Configurer le point d’entrée de la fonction

Vous pouvez utiliser les propriétés `function.json` `scriptFile` et `entryPoint` pour configurer l’emplacement et le nom de votre fonction exportée. Elles peuvent être importantes si votre code JavaScript est transpilé.

### <a name="using-scriptfile"></a>Utilisation de `scriptFile`

Par défaut, une fonction JavaScript est exécutée à partir de `index.js`, fichier qui partage le même répertoire parent que son `function.json` correspondant.

Vous pouvez utiliser la propriété `scriptFile` pour obtenir une structure de dossiers semblable à celle-ci :
```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - nodeFunction.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

Le `function.json` pour `myNodeFunction` doit inclure une propriété `scriptFile` qui pointe vers le fichier contenant la fonction exportée à exécuter.
```json
{
  "scriptFile": "../lib/nodeFunction.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Utilisation de `entryPoint`

Dans `scriptFile` (ou `index.js`), une fonction doit être exportée à l’aide de `module.exports` afin qu’elle soit trouvée et exécutée. Par défaut, la fonction qui s’exécute quand elle est déclenchée est la seule exportation à partir de ce fichier, l’exportation nommée `run` ou l’exportation nommée `index`.

Pour effectuer la configuration, utilisez `entryPoint` dans `function.json` :
```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

Dans Functions v2.x, qui prend en charge le paramètre `this` dans les fonctions utilisateur, le code de la fonction peut alors se présenter comme suit :
```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };
    
    function logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

Dans cet exemple, il est important de noter que même si un objet est en cours d’exportation, rien ne garantit la conservation de l’état d’une exécution à l’autre.

## <a name="considerations-for-javascript-functions"></a>Considérations relatives aux fonctions JavaScript

Quand vous utilisez des fonctions JavaScript, tenez compte des considérations décrites dans les sections suivantes.

### <a name="choose-single-vcpu-app-service-plans"></a>Choisir des plans App Service à processeur virtuel unique

Lorsque vous créez une application de fonction qui utilise le plan App Service, nous vous recommandons de sélectionner un plan à processeur virtuel unique plutôt qu’un plan à plusieurs processeurs virtuels. À l’heure actuelle, Functions exécute les fonctions JavaScript plus efficacement sur des machines virtuelles à processeur virtuel unique. Le recours à de plus grandes machines virtuelles ne produit pas les améliorations de performances attendues. Le cas échéant, vous pouvez faire une mise à l’échelle horizontale manuellement en ajoutant des instances de machine virtuelle à processeur virtuel unique, ou vous pouvez activer la mise à l’échelle automatique. Pour plus d’informations, consultez [Mettre à l’échelle le nombre d’instances manuellement ou automatiquement](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Prise en charge de typeScript et CoffeeScript
Comme il n’existe encore aucune prise en charge directe pour l’auto-compilation de TypeScript/CoffeeScript via le runtime, cela nécessite une gestion externe au runtime, au moment du déploiement. 

### <a name="cold-start"></a>Démarrage à froid
Quand vous développez des fonctions Azure dans le modèle d’hébergement serverless, les démarrages à froid sont une réalité. « Démarrage à froid » fait référence au fait que le démarrage de votre application de fonction prend plus de temps quand elle démarre pour la première fois après une période d’inactivité. En particulier, pour les fonctions JavaScript avec de grandes arborescences de dépendances, cela peut entraîner des ralentissements majeurs. Pour accélérer le processus, si possible, [exécutez vos fonctions en tant que fichier de package](run-functions-from-deployment-package.md). De nombreuses méthodes de déploiement optent par défaut pour ce modèle, mais si vous êtes confronté à de grands démarrages à froid et que vous n’effectuez pas l’exécution à partir d’un fichier de package, cette approche peut être source d’une grande amélioration.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les ressources suivantes :

* [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
* [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](functions-triggers-bindings.md)

