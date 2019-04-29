---
title: Comment utiliser le flux de modification Azure Cosmos DB avec Azure Functions
description: Utiliser le flux de modification Azure Cosmos DB avec Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 18780deba1910b3ea77f7313bfb6d204dacabd82
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112019"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Architectures basées sur les événements sans serveur avec Azure Cosmos DB et Azure Functions

Azure Functions fournit le moyen le plus simple pour vous connecter à la [le flux de modification](change-feed.md). Vous pouvez créer des petites fonctions Azure réactif qui sera déclenché automatiquement sur chaque nouvel événement dans le flux de modification de votre conteneur Azure Cosmos.

![Fonctions sans serveur basée sur les événements fonctionne avec le déclencheur Azure Cosmos DB](./media/change-feed-functions/functions.png)

Avec le [déclencheur Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger), vous pouvez tirer parti du [processeur de flux de modification](./change-feed-processor.md)de mise à l’échelle et la fonctionnalité de détection d’événement fiable sans la nécessité de maintenir les [worker infrastructure](./change-feed-processor.md#implementing-the-change-feed-processor-library). Concentrez-vous sur une logique de votre fonction Azure sans vous soucier du reste du pipeline event sourcing. Vous pouvez même combiner le déclencheur avec n’importe quel autre [liaisons Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Actuellement, le déclencheur Azure Cosmos DB est pris en charge pour une utilisation avec le noyau (API SQL) uniquement.

## <a name="requirements"></a>Configuration requise

Pour implémenter un flux basé sur des événements sans serveur, vous devez :

* **Conteneur surveillé**: Conteneur surveillé est le conteneur Azure Cosmos en cours d’analyse, et stocke les données à partir de laquelle le flux de modification est généré. Toutes les insertions et les modifications (par exemple, CRUD) au conteneur surveillé sont répercutées dans le flux de modification du conteneur.
* **Le conteneur de bail**: Le conteneur de bail gère l’état entre plusieurs et les instances de Azure Function sans serveur dynamique et permet la mise à l’échelle dynamique. Ce conteneur de bail peut être créé manuellement ou automatiquement par le Trigger.To de base de données Azure Cosmos automatiquement créer le conteneur de bail, définissez la *CreateLeaseCollectionIfNotExists* indicateur dans le [configuration](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Conteneurs de bail partitionnées sont nécessaires pour avoir un `/id` définition de la clé de partition.

## <a name="create-your-azure-cosmos-db-trigger"></a>Créer votre déclencheur de base de données Azure Cosmos

Création de votre fonction Azure avec un déclencheur Azure Cosmos DB est maintenant pris en charge sur tous les IDE de fonctions Azure et les intégrations de CLI :

* [Extension Visual Studio](../azure-functions/functions-develop-vs.md) pour les utilisateurs de Visual Studio.
* [Extension de Visual Studio Core](https://code.visualstudio.com/tutorials/functions-extension/create-function) pour les utilisateurs de Visual Studio Code.
* Et enfin [outils Core CLI](../azure-functions/functions-run-local.md#create-func) pour une expérience d’agnostique IDE inter-plateformes.

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>Exécution locale de votre déclencheur Azure Cosmos DB

Vous pouvez exécuter votre [Azure Function localement](../azure-functions/functions-develop-local.md) avec la [émulateur Azure Cosmos DB](./local-emulator.md) pour créer et développer vos flux en fonction des événements sans serveur sans un abonnement Azure et sans frais.

Si vous souhaitez tester les scénarios en direct dans le cloud, vous pouvez [essai gratuit de Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) sans carte de crédit ou abonnement Azure requis.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant continuer à en savoir plus sur les flux de modification dans les articles suivants :

* [Présentation du flux de modification](change-feed.md)
* [Manières de lire le flux de modification](read-change-feed.md)
* [Utilisation de la bibliothèque du processeur de flux de modification](change-feed-processor.md)
* [Comment utiliser la bibliothèque du processeur de flux de modification](change-feed-processor.md)
* [Traitement de base de données serverless à l’aide d’Azure Cosmos DB et d’Azure Functions](serverless-computing-database.md)
