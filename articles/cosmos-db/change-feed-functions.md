---
title: Comment utiliser le flux de modification Azure Cosmos DB avec Azure Functions
description: Utiliser le flux de modification Azure Cosmos DB avec Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 41cbb657a4fc83b498c5cc9a6a16397a619aa075
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034044"
---
# <a name="how-to-use-azure-cosmos-db-change-feed-with-azure-functions"></a>Comment utiliser le flux de modification Azure Cosmos DB avec Azure Functions

Si vous utilisez Azure Functions, la façon la plus simple de se connecter à un flux de modification est d’ajouter un [déclencheur Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger) à votre application Azure Functions. Lorsque vous créez un déclencheur Cosmos DB dans une application Azure Functions, vous sélectionnez le conteneur Cosmos auquel vous souhaitez vous connecter, et la fonction se déclenche chaque fois que vous apportez une modification au conteneur.

Les déclencheurs peuvent être créés via le portail Azure Functions, via le portail Azure Cosmos DB, ou par programmation. Pour en savoir plus, consultez l’article [traitement de base de données serverless à l’aide d’Azure Cosmos DB et d’Azure Functions](serverless-computing-database.md).

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region"></a>Comment puis-je configurer des fonctions Azure pour lire à partir d’une région spécifique ?

Il est possible de définir les [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations) lorsque vous utilisez le déclencheur Azure Cosmos DB pour spécifier une liste de régions. C'est la même chose que de personnaliser la ConnectionPolicy, pour que le déclencheur soit lu à partir de vos régions préférées. Dans l’idéal, vous souhaitez lire à partir de la région la plus proche dans laquelle Azure Functions est déployé.

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Quelle est la taille par défaut des lots dans Azure Functions ?

La taille par défaut est 100 éléments pour chaque invocation d’Azure Functions. Vous pouvez toutefois modifier ce nombre dans le fichier function.json. Voici la [liste complète des options de configuration](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Si vous développez en local, mettez à jour les paramètres de l’application dans le fichier local.settings.json.

### <a name="i-am-monitoring-a-container-and-reading-its-change-feed-however-i-dont-get-all-the-inserted-documents-some-items-are-missing"></a>Je suis en train d’analyser un conteneur et de lire son flux de modification, mais certains documents ne s’affichent pas. Manque-t-il des articles ?

Assurez-vous qu’aucune autre instance d’Azure Functions ne lit actuellement le même conteneur avec le même conteneur de bail. Les documents manquants sont traités par les autres instances d’Azure Functions qui utilisent le même bail.

Par conséquent, si vous créez plusieurs instances d’Azure Functions pour lire le même flux de modification, celles-ci doivent utiliser des conteneurs de bail différents ou la configuration « leasePrefix » pour partager le même conteneur. Toutefois, lorsque vous utilisez la bibliothèque du processeur de flux de modification, vous pouvez démarrer plusieurs instances d’Azure Functions, et le kit de développement logiciel (SDK) répartit automatiquement les documents dans les différentes instances pour vous.

### <a name="azure-cosmos-item-is-updated-every-second-and-i-dont-get-all-the-changes-in-azure-functions-listening-to-change-feed"></a>L’élément Azure Cosmos est mis à jour chaque seconde, et je ne reçois pas toutes les modifications dans les instances d’Azure Functions qui écoutent le flux de modification.

Azure Functions interroge en continu le flux de modification sur les modifications, avec un retard par défaut maximum de 5 secondes. Si aucune modification n’attend d’être lue ou si des modifications sont en attente après que le déclencheur a été appliqué, la fonction les lit tout de suite. Toutefois, s’il n’y a aucune modification en attente, la fonction attend 5 secondes et interroge pour obtenir plus de modifications.

Si votre document reçoit plusieurs modifications dans le même intervalle nécessaire au déclencheur pour interroger sur les nouvelles modifications, il est possible que vous receviez la dernière version du document et non pas la version intermédiaire.

Si vous souhaitez interroger le flux de modification à un intervalle inférieur à 5 secondes, par exemple, chaque seconde, vous pouvez configurer le temps d’interrogation « feedPollDelay », consultez [la configuration complète](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations). Cet intervalle est défini en millisecondes et configuré sur 5000 par défaut. Un intervalle inférieur à 1 seconde est possible, mais pas recommandé, parce que vous commencez à utiliser plus de mémoire du processeur.

### <a name="can-multiple-azure-functions-read-one-containers-change-feed"></a>Plusieurs instances d’Azure Functions peuvent-elles lire le flux de modification d’un même conteneur ?

Oui. Plusieurs instances d’Azure Functions peuvent lire le flux de modification d’un même conteneur. Toutefois, les instances d’Azure Functions doivent avoir une valeur « leaseCollectionPrefix » différente.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-in-a-batch-of-10-documents-and-i-get-an-error-at-seventh-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-document-ie-seventh-document-in-my-next-feed"></a>Je traite un flux de modification à l’aide d’Azure Functions pour un lot de 10 documents et j’obtiens une erreur au niveau du septième document. Dans ce cas, les trois derniers documents ne sont pas traités. Comment puis-je démarrer le traitement à partir du document à l’origine de l’erreur (c’est-à-dire le septième) dans mon prochain flux ?

Pour gérer l’erreur, le modèle recommandé consiste à inclure votre code dans un wrapper avec un bloc try/catch et, si vous itérez sur la liste des documents, à inclure chaque itération dans un wrapper dans son propre bloc try/catch. Interceptez l’erreur et placez ce document dans une file d’attente (lettres mortes), puis définissez une logique pour traiter les documents qui ont généré l’erreur. Si vous avez un lot de 200 documents et qu’un seul document génère une erreur, cette méthode vous évite d’annuler le lot complet.

En cas d’erreur, vous n’avez pas besoin de rembobiner le point de vérification jusqu’au début. Les documents continueront à s’afficher dans le flux de modification. N’oubliez pas que le flux de modification conserve le dernier instantané final de chaque document. Vous risquez donc de perdre le précédent instantané du document. Le flux de modification conserve uniquement la dernière version du document et peut, entre autres, revenir au document pour le modifier.

À mesure que vous modifiez votre code, les documents disparaîtront les uns après les autres de la file d’attente de lettres mortes. Azure Functions est appelé automatiquement par le système de flux de modification et le point de vérification est géré en interne par la fonction Azure. Si vous souhaitez restaurer le point de vérification et en contrôler chaque aspect, envisagez d’utiliser le kit de développement logiciel (SDK) du processeur de flux de modification.

### <a name="are-there-any-extra-costs-for-using-the-azure-cosmos-db-trigger"></a>L’utilisation du déclencheur Azure Cosmos DB génère-t-elle des coûts supplémentaires ?

Le déclencheur Azure Cosmos DB tire parti de la bibliothèque du processeur de flux de modification en interne. Par conséquent, il requiert une collection supplémentaire, appelée collection de baux, pour conserver l’état et les points de contrôle partiels. Cette gestion de l’état est nécessaire pour pouvoir mettre à l’échelle dynamiquement et continuer au cas où vous souhaitez arrêter votre instance Azure Functions et poursuivre le traitement ultérieurement. Pour plus d’informations, consultez [Comment utiliser la bibliothèque du processeur de flux de modification](change-feed-processor.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le flux de modification, consultez les articles suivants :

* [Présentation du flux de modification](change-feed.md)
* [Manières de lire le flux de modification](read-change-feed.md)
* [Utilisation de la bibliothèque du processeur de flux de modification](change-feed-processor.md)
* [Comment utiliser la bibliothèque du processeur de flux de modification](change-feed-processor.md)
* [Traitement de base de données serverless à l’aide d’Azure Cosmos DB et d’Azure Functions](serverless-computing-database.md)
