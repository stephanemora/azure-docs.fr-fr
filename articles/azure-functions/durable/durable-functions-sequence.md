---
title: Chaînage de fonctions dans Fonctions durables - Azure
description: Découvrez comment exécuter un exemple de fonctions durables qui exécute une séquence de fonctions.
author: cgillum
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: de2fd1a46d931c5d1b625094940a981509bf1488
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769555"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Chaînage de fonctions dans Fonctions durables - Exemple de séquence Hello

Un chaînage de fonctions fait référence au modèle d’exécution d’une séquence de fonctions dans un ordre particulier. La sortie d’une fonction doit souvent être appliquée à l’entrée d’une autre fonction. Cet article décrit la séquence de chaînage que vous créez quand vous suivez le guide de démarrage rapide de Durable Functions ([C#](durable-functions-create-first-csharp.md) ou [JavaScript](quickstart-js-vscode.md)). Pour plus d’informations sur Durable Functions, consultez [Vue d’ensemble de Durable Functions](durable-functions-overview.md).

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Les fonctions

Cet article explique les fonctions suivantes dans l’exemple d’application :

* `E1_HelloSequence`: Une fonction orchestrator qui appelle `E1_SayHello` plusieurs fois dans une séquence. Il stocke les sorties à partir des appels de `E1_SayHello` et enregistre les résultats.
* `E1_SayHello`: Une fonction d’activité qui fait précéder une chaîne de « Hello ».

Les sections suivantes décrivent la configuration et le code utilisés pour les scripts C# et JavaScript. Le code de développement de Visual Studio est affiché à la fin de l’article.

> [!NOTE]
> Les fonctions JavaScript Durable Functions sont disponibles uniquement pour le runtime de Functions 2.0.x.

## <a name="e1_hellosequence"></a>E1_HelloSequence

### <a name="functionjson-file"></a>Fichier function.json

Si vous utilisez Visual Studio Code ou le portail Azure pour le développement, voici le contenu du fichier *function.json* pour la fonction d’orchestrateur. La plupart des fichiers *function.json* d’orchestrateur ressemblent presque exactement à cela.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Le point essentiel est le type de liaison `orchestrationTrigger`. Toutes les fonctions d’orchestrateur doivent utiliser ce type de déclencheur.

> [!WARNING]
> Pour respecter la règle « Aucune E/S » des fonctions d’orchestrateur, n’utilisez aucune liaison d’entrée ou de sortie lors de l’utilisation de la liaison de déclenchement `orchestrationTrigger`.  Si d’autres liaisons d’entrée ou de sortie sont nécessaires, elles doivent plutôt être utilisées dans le contexte des fonctions `activityTrigger`, qui sont appelées par l’orchestrateur. Pour plus d’informations, consultez l’article [Contraintes du code des fonctions d’orchestrateur](durable-functions-code-constraints.md).

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>Script C# (exemple de code Visual Studio Code et portail Azure)

Voici le code source :

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Toutes les fonctions d’orchestration C# doivent avoir un paramètre de type `DurableOrchestrationContext`, qui existe dans l’assembly `Microsoft.Azure.WebJobs.Extensions.DurableTask`. Si vous utilisez un script C#, l’assembly peut être référencée à l’aide de la notation `#r`. Cet objet de contexte vous permet d’appeler d’autres fonctions *d’activité* et de passer les paramètres d’entrée à l’aide de sa méthode `CallActivityAsync`.

Le code appelle trois fois `E1_SayHello` en séquence avec des valeurs de paramètre différentes. La valeur renvoyée de chaque appel est ajoutée à la liste `outputs`, qui est retournée à la fin de la fonction.

### <a name="javascript"></a>JavaScript

Voici le code source :

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Toutes les fonctions d’orchestration JavaScript doivent inclure le module [`durable-functions`](https://www.npmjs.com/package/durable-functions). Il s’agit d’une bibliothèque qui vous permet d’écrire des fonctions Durable Functions en JavaScript. Il existe trois différences importantes entre une fonction d’orchestration et les autres fonctions JavaScript :

1. La fonction est une [fonction de générateur.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. La fonction est encapsulée dans un appel à la méthode `orchestrator` du module `durable-functions` (ici `df`).
3. La fonction doit être synchrone. Étant donné que la méthode « orchestrator » gère l’appel à « context.done », la fonction doit simplement retourner (« return ») un résultat.

L’objet `context` contient un objet `df` qui vous permet d’appeler d’autres fonctions d’*activité* et de passer les paramètres d’entrée à l’aide de sa méthode `callActivity`. Le code appelle `E1_SayHello` trois fois à la suite avec différentes valeurs de paramètre, en se servant de `yield` pour indiquer que l’exécution doit attendre les appels de fonction d’activité asynchrones à renvoyer. La valeur renvoyée de chaque appel est ajoutée à la liste `outputs`, qui est retournée à la fin de la fonction.

## <a name="e1_sayhello"></a>E1_SayHello

### <a name="functionjson-file"></a>Fichier function.json

Le fichier *function.json* pour la fonction de l’activité `E1_SayHello` est similaire à celle de `E1_HelloSequence` sauf qu’elle utilise un type de liaison `activityTrigger` à la place d’un type de liaison `orchestrationTrigger`.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> N’importe quelle fonction appelée par une fonction d’orchestration doit utiliser la liaison `activityTrigger`.

L’implémentation de `E1_SayHello` est une opération de mise en forme de chaîne relativement simple.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Cette fonction a un paramètre de type `DurableActivityContext`, qu’elle utilise pour obtenir l’entrée qui lui a été passée par l’appel de la fonction d’orchestrateur à `CallActivityAsync<T>`.

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Contrairement à une fonction d’orchestration JavaScript, une fonction d’activité ne nécessite aucune configuration particulière. L’entrée transmise par la fonction d’orchestrateur se trouve sur l’objet `context.bindings` sous le nom de la liaison `activityTrigger`, ici `context.bindings.name`. Le nom de la liaison peut être défini en tant que paramètre de la fonction exportée et accessible directement, ce que fait l’exemple de code.

## <a name="run-the-sample"></a>Exécution de l'exemple

Pour exécuter l’orchestration `E1_HelloSequence`, envoyez la requête HTTP POST suivante.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> L’extrait de code HTTP précédent suppose qu’il existe une entrée dans le fichier `host.json`, qui supprime le préfixe `api/` par défaut de toutes les URL de fonctions de déclencheur HTTP. Le balisage pour cette configuration figure dans le fichier `host.json` dans les exemples.

Par exemple, si vous exécutez l’exemple dans une application de fonction nommée « myfunctionapp », remplacez « {hôte} » par « myfunctionapp.azurewebsites.net ».

Le résultat est une réponse HTTP 202, comme celle-ci (ajustée par souci de concision) :

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

À ce stade, l’orchestration est mise en file d’attente et commence à s’exécuter immédiatement. L’URL dans l’en-tête `Location` peut être utilisée pour vérifier l’état de l’exécution.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Le résultat est l'état de l'orchestration. Elle s’exécute et se termine rapidement, vous voyez donc le résultat dans l’état *Terminé* avec une réponse qui ressemble à ceci (ajustée par souci de concision) :

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Comme vous pouvez le voir, le `runtimeStatus` de l’instance est *Terminé* et le `output` contient le résultat sérialisé en JSON de l’exécution de la fonction d’orchestration.

> [!NOTE]
> Le point de terminaison HTTP POST qui a démarré la fonction d’orchestrateur est implémenté dans l’application exemple en tant que fonction de déclenchement HTTP nommée « HttpStart ». Vous pouvez implémenter une logique de démarrage similaire pour d’autres types de déclenchement, comme `queueTrigger`, `eventHubTrigger`, ou `timerTrigger`.

Examinez les journaux d’activité d’exécution de fonction. La fonction `E1_HelloSequence` a démarré et s’est terminée plusieurs fois en raison du comportement de relecture décrit dans la rubrique sur la [fiabilité de l’orchestration](durable-functions-orchestrations.md#reliability). En revanche, il n’y a eu que trois exécutions de `E1_SayHello` étant donné que les exécutions de la fonction ne sont pas relues.

## <a name="visual-studio-sample-code"></a>Exemple de code Visual Studio

Voici l’orchestration, présentée sous la forme d’un seul fichier C# dans un projet Visual Studio :

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Étapes suivantes

Cet exemple a illustré une orchestration simple de chaînage de fonction. L’exemple suivant montre comment implémenter le modèle fan-out/fan-in.

> [!div class="nextstepaction"]
> [Exécuter l’exemple Fan-out/fan-in](durable-functions-cloud-backup.md)
