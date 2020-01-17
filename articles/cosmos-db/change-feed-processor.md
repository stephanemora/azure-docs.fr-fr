---
title: Bibliothèque du processeur de flux de modification dans Azure Cosmos DB
description: Découvrez comment utiliser la bibliothèque du processeur de flux de modification Azure Cosmos DB pour lire le flux de modification et les composants du processeur de flux de modification.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 3e7f5e46068844da538864fdfaa03ca7023e4372
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445579"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Processeur de flux de modification dans Azure Cosmos DB 

Le processeur de flux de modification fait partie du [kit de développement logiciel Azure Cosmos DB V3](https://github.com/Azure/azure-cosmos-dotnet-v3). Elle simplifie le processus de lecture du flux de modification et répartit efficacement le traitement des événements sur plusieurs consommateurs.

Le principal avantage de la bibliothèque du processeur de flux de modification est son comportement à tolérance de panne qui garantit une livraison « au moins une fois » de tous les événements dans le flux de modification.

## <a name="components-of-the-change-feed-processor"></a>Composants du processeur de flux de modification

Il existe quatre composants principaux dans l’implémentation du processeur de flux de modification : 

1. **Conteneur supervisé :** le conteneur supervisé est constitué des données à partir desquelles le flux de modification est généré. Toutes les insertions et les mises à jour apportées au conteneur supervisé sont répercutées dans le flux de modification du conteneur.

1. **Conteneur de baux :** Le conteneur de baux fait office d’entrepôt d’état et coordonne le traitement du flux de modification entre plusieurs rôles de travail. Le conteneur de baux peut être stocké dans le même compte que le conteneur surveillé ou dans un compte distinct. 

1. **L’hôte :** Un hôte est une instance d’application qui utilise le processeur de flux de modification pour écouter les modifications. Plusieurs instances avec la même configuration de bail peuvent s’exécuter en parallèle, mais chaque instance doit avoir un **nom d’instance** différent. 

1. **Le délégué :** Le délégué est le code qui définit ce que vous, le développeur, souhaitez faire avec chaque lot de modifications que le processeur de flux de modification lit. 

Pour mieux comprendre comment ces quatre éléments du processeur de flux de modification interagissent, examinons un exemple à l’aide du schéma suivant. Le conteneur surveillé stocke les documents et utilise « City » comme clé de partition. Nous voyons que les valeurs de clé de partition sont distribuées dans des plages qui contiennent des éléments. Il existe deux instances d’hôte et le processeur de flux de modification affecte différentes plages de valeurs de clés de partition à chaque instance pour optimiser la distribution de calcul. Chaque plage est lue en parallèle et sa progression est gérée séparément des autres plages dans le conteneur de baux.

![Exemple de processeur de flux de modification](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>Implémentation du processeur de flux de modification

Le point d’entrée est toujours le conteneur surveillé, à partir d’une instance `Container` que vous appelez `GetChangeFeedProcessorBuilder` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Le premier paramètre est un nom distinct qui décrit l’objectif de ce processeur et le deuxième nom est l’implémentation de délégué qui gérera les modifications. 

Voici un exemple de délégué :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Enfin, vous définissez un nom pour cette instance de processeur avec `WithInstanceName` et avec quel conteneur vous souhaitez maintenir l’état du bail `WithLeaseContainer`.

Appeler `Build` vous donnera l’instance de processeur que vous pouvez démarrer en appelant `StartAsync`.

## <a name="processing-life-cycle"></a>Cycle de vie du traitement

Le cycle de vie normal d’une instance d’hôte est le suivant :

1. Lire le flux de modification.
1. Si aucune modification n’est apportée, veillez pendant un intervalle de temps prédéfini (personnalisable avec `WithPollInterval` dans le générateur) et accédez à #1.
1. En cas de modifications, envoyez-les au **délégué**.
1. Lorsque le délégué finit de traiter **correctement** les modifications, mettez à jour le magasin de baux avec le dernier point traité dans le temps et accédez à #1.

## <a name="error-handling"></a>Gestion des erreurs

Le processeur de flux de modification résiste aux erreurs de code utilisateur. Cela signifie que si votre implémentation de délégué a une exception non gérée (étape #4), le thread qui traite ce lot de modifications particulier sera arrêté et un nouveau thread sera créé. Le nouveau thread vérifie le dernier point dans le temps du magasin de baux pour cette plage de valeurs de clé de partition, et redémarre à partir de là, en envoyant efficacement le même lot de modifications au délégué. Ce comportement se poursuivra jusqu’à ce que votre délégué traite correctement les modifications et c’est la raison pour laquelle le processeur de flux de modification a une garantie « au moins une fois », car si le code de délégué lève, il réessaiera ce lot.

## <a name="dynamic-scaling"></a>Mise à l’échelle dynamique

Comme mentionné dans l’introduction, le processeur de flux de modification peut distribuer automatiquement le calcul sur plusieurs instances. Vous pouvez déployer plusieurs instances de votre application à l’aide du processeur de flux de modification et en tirer parti, les seules exigences principales sont les suivantes :

1. Toutes les instances doivent avoir la même configuration de conteneur de baux.
1. Toutes les instances doivent avoir le même nom de flux de travail.
1. Chaque instance doit avoir un nom d’instance différent (`WithInstanceName`).

Si ces trois conditions s’appliquent, le processeur de flux de modification utilise un algorithme de distribution égal, répartit tous les baux dans le conteneur de baux sur toutes les instances en cours d’exécution et parallélise le calcul. Un bail ne peut appartenir qu’à une seule instance à la fois, ce qui signifie que le nombre maximal d’instances est égal au nombre de baux.

Le nombre d’instances peut croître et diminuer, et le processeur de flux de modification ajuste la charge de façon dynamique en la redistribuant en conséquence.

En outre, le processeur de flux de modification peut s’ajuster de manière dynamique à l’échelle des conteneurs en raison des augmentations de débit ou de stockage. Lorsque votre conteneur croît, le processeur de flux de modification gère en toute transparence ces scénarios en augmentant les baux de manière dynamique et en distribuant les nouveaux baux aux instances existantes.

## <a name="change-feed-and-provisioned-throughput"></a>Flux de modification et débit provisionné

Vous êtes facturé pour les unités de requête consommées, car des unités de requête sont consommées lorsque vous déplacez des données vers et à partir des conteneurs Cosmos. La facturation tient compte de toutes les unités de requête consommées par le conteneur de baux.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Exemples d’utilisation sur GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Exemples supplémentaires sur GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le processeur de flux de modification, consultez les articles suivants :

* [Présentation du flux de modification](change-feed.md)
* [Guide pratique pour effectuer une migration à partir de la bibliothèque du processeur de flux de modification](how-to-migrate-from-change-feed-library.md)
* [Utilisation de l’estimateur de flux de modification](how-to-use-change-feed-estimator.md)
* [Heure de début du processeur de flux de modification](how-to-configure-change-feed-start-time.md)
