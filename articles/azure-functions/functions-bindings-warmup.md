---
title: Déclencheur de préchauffage Azure Functions
description: Découvrez comment utiliser le déclencheur de préchauffage dans Azure Functions.
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: azure functions, fonctions, traitement des événements, préchauffage, démarrage à froid, premium, calcul dynamique, architecture serverless
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: 6884c8f1bf7a462b5d93f5c9ea23a2f64021fd9e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328573"
---
# <a name="azure-functions-warm-up-trigger"></a>Déclencheur de préchauffage Azure Functions

Cet article explique comment utiliser le déclencheur de préchauffage dans Azure Functions. Le déclencheur de préchauffage est pris en charge uniquement pour les applications de fonction qui s’exécutent dans un [plan Premium](functions-premium-plan.md). Un déclencheur de préchauffage est appelé lorsqu’une instance est ajoutée pour mettre à l’échelle une application de fonction en cours d’exécution. Vous pouvez utiliser un déclencheur de préchauffage pour précharger des dépendances personnalisées pendant le [processus de préchauffage](./functions-premium-plan.md#pre-warmed-instances) afin que vos fonctions soient prêtes à commencer le traitement des requêtes immédiatement. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Packages - Functions 2.x

Le package NuGet [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions), version **3.0.5 ou ultérieure**, est requis. Le code source du package se trouve dans le référentiel GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/). 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Déclencheur

Le déclencheur de préchauffage vous permet de définir une fonction qui sera exécutée sur une instance quand elle est ajoutée à votre application en cours d’exécution. Vous pouvez utiliser une fonction de préchauffage pour ouvrir des connexions, charger des dépendances ou exécuter toute autre logique personnalisée avant que votre application ne commence à recevoir le trafic. 

Le déclencheur de préchauffage est destiné à créer des dépendances partagées qui seront utilisées par les autres fonctions de application. [Consultez des exemples de dépendances partagées ici](./manage-connections.md#client-code-examples).

Notez que le déclencheur de préchauffage est uniquement appelé pendant les opérations de montée en puissance, et non lors des redémarrages ou d’autres démarrages sans échelle. Vous devez vous assurer que votre logique peut charger toutes les dépendances nécessaires sans utiliser le déclencheur de préchauffage. Le chargement différé est un bon modèle pour y parvenir.

## <a name="trigger---example"></a>Déclencheur - exemple

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui s’exécutera sur chaque nouvelle instance quand elle sera ajoutée à votre application. Aucun attribut de valeur renvoyée n’est requis.


* Votre fonction doit être nommée ```warmup``` (non sensible à la casse) et il ne peut y avoir qu’une seule fonction de préchauffage par application.
* Pour utiliser le préchauffage comme une fonction de bibliothèque de classes .NET, vérifiez que vous disposez d’une référence de package à **Microsoft.Azure.WebJobs.Extensions >= 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


Les commentaires d’espace réservé indiquent les emplacements dans l’application afin de déclarer et d’initialiser les dépendances partagées. 
[En savoir plus sur les dépendances partagées ici](./manage-connections.md#client-code-examples).

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)


L’exemple suivant montre un déclencheur de préchauffage dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui s’exécutera sur chaque nouvelle instance quand elle sera ajoutée à votre application.

Votre fonction doit être nommée ```warmup``` (non sensible à la casse), et il ne peut y avoir qu’une seule fonction de préchauffage par application.

Voici le fichier *function.json* :

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

La section [configuration](#trigger---configuration) décrit ces propriétés.

Voici le code de script C# qui lie à `HttpRequest` :

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

L’exemple suivant montre un déclencheur de préchauffage dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui s’exécutera sur chaque nouvelle instance quand elle sera ajoutée à votre application.

Votre fonction doit être nommée ```warmup``` (non sensible à la casse) et il ne peut y avoir qu’une seule fonction de préchauffage par application.

Voici le fichier *function.json* :

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

La section [configuration](#trigger---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

L’exemple suivant montre un déclencheur de préchauffage dans un fichier *function.json* et une [fonction Python](functions-reference-python.md) qui s’exécutera sur chaque nouvelle instance quand elle sera ajoutée à votre application.

Votre fonction doit être nommée ```warmup``` (non sensible à la casse) et il ne peut y avoir qu’une seule fonction de préchauffage par application.

Voici le fichier *function.json* :

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

La section [configuration](#trigger---configuration) décrit ces propriétés.

Voici le code Python :

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="javatabjava"></a>[Java](#tab/java)

L’exemple suivant montre un déclencheur de préchauffage dans un fichier *function.json* et une [fonction Java](functions-reference-java.md) qui s’exécutera sur chaque nouvelle instance quand elle sera ajoutée à votre application.

Votre fonction doit être nommée ```warmup``` (non sensible à la casse) et il ne peut y avoir qu’une seule fonction de préchauffage par application.

Voici le fichier *function.json* :

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

Voici le code Java :

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Déclencheur - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), l’attribut `WarmupTrigger` est disponible pour configurer la fonction.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Cet exemple montre comment utiliser l’attribut [warmup](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs).

Notez que votre fonction doit être appelée ```Warmup``` et qu’il ne peut y avoir qu’une seule fonction de préchauffage par application.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Pour obtenir un exemple complet, consultez l’[exemple de déclencheur](#trigger---example).

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

Les attributs ne sont pas pris en charge par le script C#.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Les attributs ne sont pas pris en charge par JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Les attributs ne sont pas pris en charge par Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Le déclencheur de préchauffage n’est pas pris en charge dans Java en tant qu’attribut.

---

## <a name="trigger---configuration"></a>Déclencheur - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `WarmupTrigger`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
| **type** | n/a| Obligatoire : doit être défini sur `warmupTrigger`. |
| **direction** | n/a| Obligatoire : doit être défini sur `in`. |
| **name** | n/a| Obligatoire : nom de variable utilisé dans le code de fonction.|

## <a name="trigger---usage"></a>Déclencheur - utilisation

Aucune information supplémentaire n’est fournie à une fonction de déclencheur de préchauffage quand elle est appelée.

## <a name="trigger---limits"></a>Déclencheur : limites

* Le déclencheur de préchauffage est uniquement disponible pour les applications qui s’exécutent dans le cadre d’un [plan Premium](./functions-premium-plan.md).
* Le déclencheur de préchauffage est uniquement appelé pendant les opérations de montée en puissance, et non lors des redémarrages ou d’autres démarrages sans échelle. Vous devez vous assurer que votre logique peut charger toutes les dépendances nécessaires sans utiliser le déclencheur de préchauffage. Le chargement différé est un bon modèle pour y parvenir.
* Le déclencheur de préchauffage ne peut pas être appelé une fois qu’une instance est déjà en cours d’exécution.
* Il ne peut y avoir qu’une seule fonction de déclencheur de préchauffage par application de fonction.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
