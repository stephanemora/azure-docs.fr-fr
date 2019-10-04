---
title: Contraintes de code d’orchestrateur durable - Azure Functions
description: Relecture des fonctions d’orchestration et contraintes de code pour Azure Durable Functions.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/18/2019
ms.author: azfuncdf
ms.openlocfilehash: 87851a4879760c76950f765d05de4662ecb04bea
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935538"
---
# <a name="orchestrator-function-code-constraints"></a>Contraintes de code des fonctions d’orchestrateur

Durable Functions est une extension d’[Azure Functions](../functions-overview.md) qui vous permet de créer des fonctions avec état. Vous pouvez utiliser une [fonction d’orchestrateur](durable-functions-orchestrations.md) pour orchestrer l’exécution d’autres fonctions durables dans une application de fonction. Les fonctions d’orchestrateur présentent un état, sont fiables et peuvent être de longue durée.

## <a name="orchestrator-code-constraints"></a>Contraintes du code d’orchestrateur

Les fonctions d’orchestrateur utilisent l’[approvisionnement en événements](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) pour garantir une exécution fiable et maintenir l’état des variables locales. Le [comportement de réexécution](durable-functions-orchestrations.md#reliability) du code d’orchestrateur crée des contraintes sur le type de code qui peut être écrit dans une fonction d’orchestrateur. Par exemple, le code d’orchestrateur doit être *déterministe*.  Les fonctions d’orchestrateur seront réexécutées plusieurs fois et doivent générer le même résultat chaque fois.

Les sections suivantes fournissent des instructions simples pour garantir que votre code est déterministe.

### <a name="using-deterministic-apis"></a>Utilisation des API déterministes

Les fonctions d’orchestrateur sont libres d’appeler n’importe quelle API de leur choix dans leur langage cible. Toutefois, il est important que les fonctions d’orchestrateur appellent uniquement des API *déterministes*. Une *API déterministe* est une API qui retourne toujours la même valeur pour la même entrée, quel que soit le moment ou la fréquence où elle est appelée.

Voici quelques exemples d’API qui doivent être évitées, car elles *ne sont pas* déterministes. Ces restrictions s’appliquent uniquement aux fonctions d’orchestrateur. Les autres types de fonction n’ont pas de telles restrictions.

| Catégorie d’API | Motif | Solution de contournement |
| ------------ | ------ | ---------- |
| Dates/heures  | Les API qui renvoient la date ou l’heure _actuelle_ ne sont pas déterministes, car la valeur renvoyée est différente pour chaque réexécution. | Utilisez l’API [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (.NET) ou `currentUtcDateTime` (JavaScript), qui est sûre pour la réexécution. |
| Identificateurs GUID/UUID  | Les API qui renvoient un identificateur GUID/UUID _aléatoire_ ne sont pas déterministes, car la valeur générée est différente à chaque réexécution. | Utilisez [NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) (.NET) ou `newGuid` (JavaScript) pour générer en toute sécurité des GUID aléatoires. |
| Nombres aléatoires | Les API qui renvoient des nombres aléatoires ne sont pas déterministes, car la valeur générée est différente à chaque réexécution. | Utilisez une fonction d’activité pour renvoyer des nombres aléatoires à une orchestration. Les valeurs de retour des fonctions d’activité sont toujours sûres pour la réexécution. |
| Liaisons | Les liaisons d’entrée et de sortie effectuent généralement des opérations d’E/S et ne sont pas déterministes. Même les liaisons du [client d’orchestration](durable-functions-bindings.md#orchestration-client) et du [client d’entité](durable-functions-bindings.md#entity-client) ne doivent pas être utilisées directement par une fonction d’orchestrateur. | Utilisez des liaisons d’entrée et de sortie dans les fonctions clientes ou d’activité. |
| Réseau | Les appels réseau impliquent des systèmes externes et ne sont pas déterministes. | Utilisez les fonctions d’activité pour effectuer des appels réseau. Si vous devez effectuer un appel HTTP à partir de votre fonction d’orchestrateur, vous avez également la possibilité d’utiliser les [API HTTP durables](durable-functions-http-features.md#consuming-http-apis). |
| API de blocage | Les API de blocage telles que `Thread.Sleep` (.NET) ou d’autres API similaires peuvent entraîner des problèmes de performances et de mise à l’échelle pour les fonctions d’orchestrateur et doivent être évitées. Dans le plan de consommation Azure Functions, elles peuvent même entraîner des frais d’exécution inutiles. | Utilisez des alternatives aux API de blocage lorsqu’elles sont disponibles, telles que `CreateTimer`, pour introduire des retards lors de l’exécution de l’orchestration. Les retards de type [minuteur durable](durable-functions-timers.md) ne sont pas pris en compte dans la durée d’exécution d’une fonction d’orchestrateur. |
| API asynchrones | Le code d’orchestrateur doit **lancer une opération asynchrone uniquement** à l’aide de l’API [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) ou de l’API de l’objet `context.df`. Par exemple, pas de `Task.Run`, `Task.Delay` ou de `HttpClient.SendAsync` dans .NET, ou de `setTimeout()` et de `setInterval()` en JavaScript. Durable Task Framework exécute le code d’orchestrateur sur un thread unique et ne peut pas interagir avec d’autres threads planifiés par d’autres API asynchrones. | Seuls des appels asynchrones *durables* doivent être effectués par une fonction d’orchestrateur. Tous les autres appels d’API asynchrones doivent être effectués à partir de fonctions d’activité. |
| Fonctions JavaScript asynchrones | Les fonctions d’orchestrateur JavaScript ne peuvent pas être `async`, car le runtime node.js ne garantit pas que les fonctions asynchrones sont déterministes. | Les fonctions d’orchestrateur JavaScript doivent être déclarées en tant que fonctions de générateur synchrones. |
| API de thread | Durable Task Framework exécute le code d’orchestrateur sur un thread unique et ne peut pas interagir avec d’autres threads. L’introduction de nouveaux threads dans l’exécution d’une orchestration peut entraîner des blocages ou des exécutions non déterministes. | Les API de thread ne doivent presque jamais être utilisées dans les fonctions d’orchestrateur. Si elles sont nécessaires, elles doivent être limitées aux fonctions d’activité. |
| Variables statiques | Les variables statiques non constantes doivent être évitées dans les fonctions d’orchestrateur, car leurs valeurs peuvent changer au fil du temps, générant un comportement d’exécution non déterministe. | Utilisez des constantes ou limitez l’utilisation des variables statiques à des fonctions d’activité. |
| Variables d’environnement | N’utilisez pas de variables d’environnement dans les fonctions d’orchestrateur. Leurs valeurs peuvent changer au fil du temps, générant un comportement d’exécution non déterministe. | Les variables d’environnement doivent être référencées uniquement à partir de fonctions clientes ou de fonctions d’activité. |
| Boucles infinies | Évitez les boucles infinies dans les fonctions d’orchestrateur. Durable Task Framework enregistre l’historique d’exécution à mesure que la fonction d’orchestration s’exécute, si bien qu’une instance d’orchestrateur peut finir par manquer de mémoire en raison d’une boucle infinie. | Pour les scénarios de boucle infinie, utilisez des API comme [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) (.NET) ou `continueAsNew` (JavaScript) pour redémarrer l’exécution de la fonction et ignorer l’historique d’exécution précédent. |

Bien que ces contraintes puissent sembler décourageantes au départ, dans la pratique, elles ne sont pas difficiles à suivre. Durable Task Framework tente de détecter des violations des règles mentionnées ci-dessus et lève une expression `NonDeterministicOrchestrationException`. Notez toutefois que même si ce comportement de détection est utile, vous ne devez pas en dépendre.

## <a name="versioning"></a>Contrôle de version

Une orchestration durable peut s’exécuter en continu pendant des jours, des mois, des années voire même [indéfiniment](durable-functions-eternal-orchestrations.md). Toutes les mises à jour de code apportées aux applications Durable Functions qui affectent des orchestrations encore non terminées peuvent perturber leur comportement de réexécution. Il est donc important de planifier soigneusement les mises à jour du code. Pour obtenir une description plus détaillée de la façon de gérer les versions de votre code, consultez l’article sur le [contrôle de version](durable-functions-versioning.md).

## <a name="durable-tasks"></a>Tâches durables

> [!NOTE]
> Cette section décrit en détail l’implémentation interne de Durable Task Framework. Vous pouvez utiliser Fonctions durables sans disposer de ces informations. Elles sont uniquement destinées à vous aider à mieux comprendre le comportement de réexécution.

Les tâches qui peuvent être attendues en toute sécurité dans les fonctions d’orchestrateur sont parfois appelées des *tâches durables*. Ces tâches sont créées et gérées par Durable Task Framework. Les tâches retournées par `CallActivityAsync`, `WaitForExternalEvent` et `CreateTimer` dans les fonctions d’orchestrateur .NET en sont des exemples.

Ces *tâches durables* sont gérées en interne à l’aide d’une liste d’objets `TaskCompletionSource` dans .NET. Durant la réexécution, ces tâches sont créées dans le cadre de l’exécution du code d’orchestrateur et exécutées à mesure que le répartiteur énumère les événements d’historique correspondants. L’exécution est réalisée de manière synchrone à l’aide d’un seul thread jusqu’à ce que tout l’historique ait été réexécuté. Toutes les tâches durables non terminées à la fin de la réexécution de l’historique feront l’objet d’actions appropriées. Par exemple, un message peut être empilé pour appeler une fonction d’activité.

Le comportement d’exécution décrit ici devrait vous aider à mieux comprendre pourquoi le code des fonctions d’orchestrateur ne doit jamais appliquer une commande `await` ou `yield` à une tâche non durable : le thread du répartiteur ne peut pas attendre qu’elle se termine et tout rappel par cette tâche pourrait corrompre l’état de suivi de la fonction d’orchestrateur. Certaines vérifications de l’exécution sont configurées pour détecter de telles violations.

Pour plus d’informations sur la façon dont Durable Task Framework exécute les fonctions d’orchestrateur, consultez le [code source des tâches durables sur GitHub](https://github.com/Azure/durabletask). Consultez en particulier [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) et [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment appeler des orchestrations secondaires](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Découvrez comment gérer les versions](durable-functions-versioning.md)
