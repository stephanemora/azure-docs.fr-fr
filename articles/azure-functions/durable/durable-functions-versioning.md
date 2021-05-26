---
title: Contrôle de version dans l’extension Fonctions durables - Azure
description: Découvrez comment implémenter le contrôle de version dans l’extension Fonctions durables d’Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 05/12/2021
ms.author: azfuncdf
ms.openlocfilehash: c5d3ac097efd81b369db16d6f7b6a4bf59b7540a
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110377396"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Contrôle de version dans l’extension Fonctions durables (Azure Functions)

Au cours du cycle de vie d’une application, des fonctions seront nécessairement ajoutées, retirées ou modifiées. L’extension [Fonctions durables](durable-functions-overview.md) permet le chaînage des fonctions selon des méthodes auparavant indisponibles. Ce chaînage affectera votre mode de gestion des versions.

## <a name="how-to-handle-breaking-changes"></a>Gestion des modifications avec rupture

Il existe plusieurs exemples de modifications avec rupture. Cet article aborde les plus fréquents. Elles partagent toutes un point commun : les orchestrations de fonctions, tant nouvelles qu’existantes, sont affectées par les modifications apportées au code de la fonction.

### <a name="changing-activity-or-entity-function-signatures"></a>Modification des signatures des fonctions d’activité ou d’entité

Une modification de signature est une opération consistant à changer le nom, l’entrée et la sortie d’une fonction. Si cette modification est apportée à une fonction d’activité ou d’entité, elle peut décomposer une fonction d’orchestrateur qui dépend d’elle. Si vous mettez à jour la fonction d’orchestrateur de manière à intégrer cette modification, vous risquez de décomposer les instances en cours.

Par exemple, supposons que vous disposiez de la fonction d’orchestrateur C# suivante.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Cette fonction simpliste récupère les résultats de **Foo** et les passe à **Bar**. Partons du principe que nous devons modifier la valeur renvoyée par **Foo**, en remplaçant `bool` par `int`, afin de prendre en charge un plus grand éventail de valeurs de résultats. Le résultat ressemble à ceci :

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string result = await context.CallActivityAsync<string>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Cette modification fonctionne pour toutes les nouvelles instances de la fonction d’orchestrateur, mais décompose toutes les instances en cours. Envisageons le cas où une instance d’orchestration appelle une fonction nommée `Foo`, récupère une valeur booléenne, puis des points de contrôle. Si la modification de signature est déployée à ce stade, l’instance faisant l’objet de points de contrôle échoue immédiatement, puis reprend et exécute à nouveau l’appel à `Foo`. Cette défaillance se produit parce que le résultat de la table d’historique est `bool`, mais le nouveau code tente de le désérialiser en `string`, ce qui entraîne une exception de runtime pour les langages de type sécurisé.

Cet exemple est une des différentes manières sont une modification de signature de fonction peut arrêter des instances existantes. En général, si un orchestrateur doit modifier le mode d’appel d’une fonction, la modification risque de créer des problèmes.

### <a name="changing-orchestrator-logic"></a>Modification de la logique de l’orchestrateur

L’autre classe de problèmes de contrôle de version résulte de la modification du code de fonction d’orchestrateur de manière à modifier le chemin d’exécution d’instances en cours.

Prenons la fonction d’orchestrateur C# suivante :

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Supposons à présent que vous souhaitiez apporter une modification pour ajouter un appel de fonction entre les deux appels de fonction existants.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

Cette modification ajoute un nouvel appel de fonction au paramètre *SendNotification*, entre *Foo* et *Bar*. Aucune modification de signature n’est effectuée. Le problème survient lorsqu’une instance existante reprend à partir de l’appel à *bar*. Lors de la réexécution, si l’appel d’origine à *Foo* renvoie `true`, la réexécution de l’orchestrateur effectue l’appel dans *SendNotification*, qui n’est pas dans son historique d’exécution. Le Durable Task Framework détecte cette incohérence et lève une exception `NonDeterministicOrchestrationException` parce qu’il a rencontré un appel à *SendNotification* alors qu’il attendait un appel à *Bar*. Le même type de problème peut survenir lors de l’ajout d’appels d’API à d’autres opérations durables, telles que la création de minuteurs durables, l’attente d’événements externes, l’appel de sous-orchestrations, etc.

## <a name="mitigation-strategies"></a>Stratégies d’atténuation

Voici quelques stratégies permettant de gérer les défis associés au contrôle de version :

* Ne rien faire (déconseillé)
* Arrêter toutes les instances en cours
* Effectuer des déploiements côte à côte

### <a name="do-nothing"></a>Ne rien faire

L’approche naïve du contrôle de version consiste à ne rien faire et à laisser les instances d’orchestration en cours échouer. Selon le type de modification, les types de défaillances suivants peuvent se produire.

* Les orchestrations peuvent échouer avec une erreur `NonDeterministicOrchestrationException`.
* Les orchestrations peuvent être bloquées indéfiniment, en signalant un état `Running`.
* Si une fonction est supprimée, toute fonction tentant de l’appeler peut échouer avec une erreur.
* Si une fonction est supprimée après planification de son exécution, l’application peut rencontrer des défaillances de runtime de bas niveau dans le moteur Durable Task Framework, ce qui peut entraîner une grave dégradation des performances.

En raison de ces défaillances potentielles, la stratégie « Ne rien faire » n’est pas recommandée.

### <a name="stop-all-in-flight-instances"></a>Arrêter toutes les instances en cours

Une autre option consiste à arrêter toutes les instances en cours. Si vous utilisez le [fournisseur de stockage Azure par défaut pour l’extension Durable Functions](durable-functions-storage-providers.md#azure-storage), vous pouvez arrêter toutes les instances en effaçant le contenu des files d’attente **control-queue** et **workitem-queue**. Vous pouvez également arrêter l’application de fonction, supprimer ces files d’attente, puis redémarrer l’application. Les files d’attente sont recréées automatiquement au redémarrage de l’application. Les instances d’orchestration précédentes peuvent rester indéfiniment dans l’état « En cours d’exécution », mais elles n’encombrent pas vos journaux avec des messages d’échec et n’endommagent pas votre application. Cette approche est idéale pour le développement rapide de prototype, y compris le développement local.

> [!NOTE]
> Cette approche nécessite un accès direct aux ressources de stockage sous-jacentes, et ne convient pas pour tous les fournisseurs de stockage que l’extension Durable Functions prend en charge.

### <a name="side-by-side-deployments"></a>Effectuer des déploiements côte à côte

La méthode la plus sûre pour assurer un déploiement sécurisé des modifications consiste à les installer côte à côte avec les versions plus anciennes. Pour cela, utilisez l’une des techniques suivantes :

* Déployer toutes les mises à jour en tant que nouvelles fonctions, en laissant les fonctions existantes telles quelles. Cela n’est généralement pas recommandé en raison de la complexité qu’implique la mise à jour récursive des appelants des nouvelles versions de la fonction.
* Déployez toutes les mises à jour en tant que nouvelle application de fonction, en utilisant un autre compte de stockage.
* Déployez une nouvelle copie de l’application de fonction avec le même compte de stockage, mais en utilisant un nom de [hub de tâches](durable-functions-task-hubs.md) mis à jour. Cela entraîne la création de nouveaux artefacts de stockage que la nouvelle version de votre application peut utiliser. L’ancienne version de votre application continuera de s’exécuter en utilisant l’ensemble précédent d’artefacts de stockage.

Le déploiement côte à côte est la technique recommandée pour déployer de nouvelles versions de vos applications de fonction.

> [!NOTE]
> Cette aide pour une stratégie de déploiement côte à côte utilise des termes spécifiques du service Stockage Azure, mais s’applique généralement à tous les [fournisseurs de stockage d’extension Durable Functions](durable-functions-storage-providers.md) pris en charge.

### <a name="deployment-slots"></a>Emplacements de déploiement

Lorsque vous effectuez des déploiements côte à côte dans Azure Functions ou Azure App Service, nous vous recommandons de déployer la nouvelle version de l’application de fonction sur un nouvel [emplacement de déploiement](../functions-deployment-slots.md). Les emplacements de déploiement vous permettent d’exécuter plusieurs instances de votre application de fonction et ce, en parallèle avec l’une d’elles seulement, en tant qu’emplacement de *production*. Lorsque vous êtes prêt à exposer la nouvelle logique d’orchestration dans l’infrastructure existante, cette opération peut s’avérer aussi simple que l’échange d’une nouvelle version dans l’emplacement de production.

> [!NOTE]
> Cette stratégie est d’autant plus efficace lorsque vous utilisez des déclencheurs Webhook et HTTP pour les fonctions d’orchestrateur. Pour les déclencheurs autres que HTTP, tels que les files d’attente ou les hubs d’événements, la définition du déclencheur doit [dériver d’un paramètre d’application](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) mis à jour dans le cadre de l’opération d’échange.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur la gestion des problèmes liés à la mise à l’échelle et aux performances](durable-functions-perf-and-scale.md)
