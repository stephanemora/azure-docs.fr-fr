---
title: Diagnostiquer et résoudre les problèmes lors de l’utilisation de déclencheur Azure Cosmos DB dans Azure Functions
description: Problèmes courants, les solutions de contournement et les étapes de diagnostics, lorsque vous utilisez le déclencheur Azure Cosmos DB avec Azure Functions
author: ealsur
ms.service: cosmos-db
ms.date: 04/16/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5416c576e6392b7c5714ef83f152453aeff8d964
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685770"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-trigger-in-azure-functions"></a>Diagnostiquer et résoudre les problèmes lors de l’utilisation de déclencheur Azure Cosmos DB dans Azure Functions

Cet article traite des problèmes courants, les solutions de contournement et les étapes de diagnostics, lorsque vous utilisez le [déclencheur Azure Cosmos DB](change-feed-functions.md) avec Azure Functions.

## <a name="dependencies"></a>Les dépendances

Le déclencheur Azure Cosmos DB et les liaisons dépendent les packages d’extension sur le runtime Azure Functions de base. Gardez toujours ces packages mis à jour, ils peuvent inclure des correctifs et nouvelles fonctionnalités susceptibles de résoudre les problèmes potentiels que vous pouvez rencontrer :

* Pour Azure Functions V2, consultez [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Pour Azure fonctions V1, consultez [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Cet article fait toujours référence à Azure Functions V2 chaque fois que le runtime est mentionné, sauf spécification explicite.

## <a name="consuming-the-cosmos-db-sdk-separately-from-the-trigger-and-bindings"></a>Utilisation du SDK Cosmos DB séparément à partir de déclencheur et des liaisons

Les fonctionnalités clés de package d’extension consiste à fournir la prise en charge pour le déclencheur Azure Cosmos DB et les liaisons. Il inclut également le [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md), ce qui est utile si vous souhaitez interagir avec Azure Cosmos DB par programmation sans utiliser les déclencheurs et les liaisons.

Si souhaitez à utiliser le SDK Azure Cosmos DB, assurez-vous que vous n’ajoutez à votre projet une autre référence de package NuGet. Au lieu de cela, **permettre à la référence du Kit de développement logiciel de résoudre via un package d’Extension Azure Functions**.

En outre, si vous créez manuellement votre propre instance de la [client du SDK Azure Cosmos DB](./sql-api-sdk-dotnet-core.md), vous devez suivre le modèle d’avoir qu’une seule instance du client [à l’aide d’une approche de modèle Singleton](../azure-functions/manage-connections.md#documentclient-code-example-c) . Ce processus permet d’éviter les problèmes potentiels de socket dans vos opérations.

## <a name="common-known-scenarios-and-workarounds"></a>Solutions de contournement et des scénarios courants connus

### <a name="azure-function-fails-with-error-message-either-the-source-collection-collection-name-in-database-database-name-or-the-lease-collection-collection2-name-in-database-database2-name-does-not-exist-both-collections-must-exist-before-the-listener-starts-to-automatically-create-the-lease-collection-set-createleasecollectionifnotexists-to-true"></a>Fonction Azure échoue avec le message d’erreur « soit la collection source « collection-name » (dans la base de données 'nom de la base de données') ou la collection de baux « collection2-name » (dans la base de données « database2-name ») n’existe pas. Les deux collections doivent exister avant le démarrage de l’écouteur. Pour créer automatiquement la collection de baux, set 'CreateLeaseCollectionIfNotExists' à 'true' »

Cela signifie qu’un ou les deux des conteneurs Azure Cosmos requis pour le déclencheur fonctionne n’existent pas ou ne sont pas accessibles à la fonction Azure. **L’erreur elle-même vous dira quelle base de données Azure Cosmos et conteneurs est le déclencheur que vous recherchez** en fonction de votre configuration.

1. Vérifiez le `ConnectionStringSetting` attribut et qu’il **fait référence à un paramètre qui existe dans votre application Azure Function App**. La valeur de cet attribut ne doit pas être la chaîne de connexion lui-même, mais le nom du paramètre de Configuration.
2. Vérifiez que le `databaseName` et `collectionName` existent dans votre compte Azure Cosmos. Si vous utilisez le remplacement de valeur automatique (à l’aide de `%settingName%` modèles), assurez-vous que le nom du paramètre existe dans votre application Azure Function App.
3. Si vous ne spécifiez pas un `LeaseCollectionName/leaseCollectionName`, la valeur par défaut est « Location ». Vérifiez que ce conteneur existe. Si vous le souhaitez, vous pouvez définir le `CreateLeaseCollectionIfNotExists` attribut dans votre déclencheur pour `true` créer automatiquement.
4. Vérifiez votre [configuration du pare-feu du compte Azure Cosmos](how-to-configure-firewall.md) à affiché pour voir qu’il n’est pas, il ne bloque pas la fonction Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Fonction Azure ne parvient pas commencer par « collection de débit partagé doit avoir une clé de partition »

Les versions précédentes de l’Extension pour Azure Cosmos DB ne prenait pas en charge à l’aide d’un conteneur de baux qui a été créé dans un [base de données partagée débit](./set-throughput.md#set-throughput-on-a-database). Pour résoudre ce problème, mettez à jour le [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) extension pour obtenir la dernière version.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Fonction Azure ne parvient pas commencer par « la collection de baux si partitionnée, doit comporter clé égale à l’id de partition. »

Cette erreur signifie que votre conteneur de baux actuelle est partitionnée, mais le chemin de clé de partition n’est pas `/id`. Pour résoudre ce problème, vous devez recréer le conteneur de baux avec `/id` comme clé de partition.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Vous voyez une « valeur ne peut pas être null. Nom du paramètre : o » dans vos journaux Azure Functions lorsque vous essayez d’exécuter le déclencheur

Ce problème apparaît si vous utilisez le portail Azure et que vous essayez de sélectionner le **exécuter** bouton sur l’écran lors de l’inspection d’une fonction Azure qui utilise le déclencheur. Le déclencheur ne nécessite pas pour vous permet de sélectionner Exécuter pour commencer, il démarre automatiquement lorsque la fonction Azure est déployée. Si vous souhaitez vérifier les flux de journal de la fonction Azure sur le portail Azure, accédez à votre conteneur surveillé et insérer de nouveaux éléments, vous verrez automatiquement l’exécution du déclencheur.

### <a name="my-changes-take-too-long-be-received"></a>Recevoir mon take modifications trop long

Ce scénario peut avoir plusieurs causes et d'entre eux doit être vérifiée :

1. Votre fonction Azure est déployée dans la même région que votre compte Azure Cosmos ? Pour une latence réseau optimales, la fonction Azure et votre compte Azure Cosmos doivent être COLOCALISÉES dans la même région Azure.
2. Les modifications sont produisent dans votre conteneur Azure Cosmos continue ou sporadique ?
Dans le cas ce dernier, il existe peut-être un certain délai entre les modifications en cours de stockage et la fonction Azure reprenant les. Il s’agit, car en interne, lorsque le déclencheur vérifie les modifications dans votre conteneur Azure Cosmos et trouve aucun en attente à lire, il est en veille pendant une durée configurable (5 secondes par défaut) avant de vérifier les nouvelles modifications (éviter une consommation élevée RU). Vous pouvez configurer cette durée de veille via le `FeedPollDelay/feedPollDelay` définition dans le [configuration](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) de votre déclencheur (la valeur est censée être en millisecondes).
3. Votre conteneur Azure Cosmos peut-être [limitée](./request-units.md).
4. Vous pouvez utiliser le `PreferredLocations` attribut dans votre déclencheur pour spécifier une liste séparée par des virgules des régions Azure pour définir un ordre de connexion par défaut personnalisée.

### <a name="some-changes-are-missing-in-my-trigger"></a>Certaines modifications sont manquantes dans mon déclencheur

Si vous trouvez que certaines modifications qui se sont produits dans votre conteneur Azure Cosmos pas collectés par la fonction Azure, il est une étape de l’étude initiale qui doit avoir lieu.

Lorsque votre fonction Azure reçoit les modifications, il souvent les traite et peut éventuellement, d’envoyer le résultat à une autre destination. Lorsque vous analysez des modifications manquantes, assurez-vous que vous **mesure les modifications qui sont reçues au niveau du point d’ingestion** (démarrage de la fonction Azure,) pas sur la destination.

Si des modifications sont manquantes sur la destination, cela pourrait signifier que certaines erreurs qui se produisent pendant l’exécution de la fonction Azure une fois que les modifications ont été reçues.

Dans ce scénario, le meilleur plan d’action consiste à ajouter `try/catch blocks` dans votre code et à l’intérieur de boucles qui peuvent traiter les modifications, afin de détecter tout échec pour un sous-ensemble particulier d’éléments et de les gérer en conséquence (les envoyer vers un autre stockage pour supplémentaire analyse ou une nouvelle tentative). 

> **Le déclencheur Azure Cosmos DB, par défaut, ne renouvelez un lot de modifications si une exception non gérée s’est produite** pendant l’exécution de votre code. Cela signifie que la raison que les modifications ne sont pas arrivé à la destination est car que vous ne traitent pas les.

Si vous trouvez que certaines modifications n’ont pas été reçues du tout par votre déclencheur, le scénario le plus courant est que Voici **en cours d’exécution une autre fonction Azure**. Il peut être une autre fonction Azure déployé dans Azure ou une fonction Azure en cours d’exécution localement sur l’ordinateur d’un développeur qui a **exactement la même configuration** (même surveillé et conteneurs de bail), et le vol de cette fonction Azure un sous-ensemble des modifications, vous vous attendez votre fonction Azure à traiter.

En outre, le scénario peut être validé, si vous savez combien d’instances Azure Function App, vous avez en cours d’exécution. Si vous examinez votre conteneur de baux et compter le nombre d’éléments de bail dans les valeurs distinctes de la `Owner` propriété dans les doit être égale au nombre d’instances de votre application de fonction. S’il existe plusieurs propriétaires que les instances d’application Azure Function App connus, cela signifie que ces propriétaires supplémentaires sont un « volant » les modifications.

Un moyen simple pour contourner cette situation, consiste à appliquer un `LeaseCollectionPrefix/leaseCollectionPrefix` à votre fonction avec une valeur nouvelle/différente ou, vous pouvez également tester avec un nouveau conteneur de baux.

## <a name="next-steps"></a>Étapes suivantes

* [Activer la surveillance pour vos fonctions Azure](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK résolution des problèmes](./troubleshoot-dot-net-sdk.md)