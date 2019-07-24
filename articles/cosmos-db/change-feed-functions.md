---
title: Comment utiliser le flux de modification Azure Cosmos DB avec Azure Functions
description: Utiliser le flux de modification Azure Cosmos DB avec Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 08429ca76823b9e6c80a197cc390a5964c4198e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969010"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Architectures basées sur des événements serverless avec Azure Cosmos DB et Azure Functions

Azure Functions fournit le moyen le plus simple pour vous connecter au [flux de modification](change-feed.md). Vous pouvez créer de petites fonctions Azure Functions réactives qui se déclencheront automatiquement sur chaque nouvel événement dans le flux de modification de votre conteneur Azure Cosmos.

![Fonctions basées sur les événements serverless et opérant avec le déclencheur Azure Cosmos DB](./media/change-feed-functions/functions.png)

Avec le [déclencheur Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger), vous pouvez tirer parti de la mise à l’échelle du [processeur de flux de modification](./change-feed-processor.md) et de la fonctionnalité fiable de détection d’événement sans conserver d’[infrastructure Worker](./change-feed-processor.md#implementing-the-change-feed-processor-library). Concentrez-vous sur la logique de votre fonction Azure sans vous soucier du reste du pipeline source d’événements. Vous pouvez même combiner le déclencheur avec d’autres [liaisons Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Actuellement, le déclencheur Azure Cosmos DB est pris en charge avec l’API Core (SQL) uniquement.

## <a name="requirements"></a>Configuration requise

Pour implémenter un flux serverless basé sur des événements, vous avez besoin des éléments suivants :

* **Conteneur supervisé :** le conteneur supervisé désigne le conteneur Azure Cosmos actuellement supervisé. Il stocke les données à partir desquelles le flux de modification est généré. Toutes les insertions et les modifications (par exemple, CRUD) apportées au conteneur supervisé sont répercutées dans le flux de modification du conteneur.
* **Conteneur de baux** : le conteneur de baux gère l’état entre plusieurs instances de fonction Azure serverless dynamiques et permet une mise à l’échelle dynamique. Ce conteneur de baux peut être créé manuellement ou automatiquement par le déclencheur Azure Cosmos DB. Pour créer automatiquement un conteneur de baux, définissez l’indicateur *CreateLeaseCollectionIfNotExists* dans la [configuration](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Les conteneurs de baux partitionnés sont nécessaires pour avoir une définition de clé de partition `/id`.

## <a name="create-your-azure-cosmos-db-trigger"></a>Créer votre déclencheur Azure Cosmos DB

La création de votre fonction Azure avec un déclencheur Azure Cosmos DB est maintenant prise en charge sur tous les IDE Azure Functions et toutes les intégrations CLI :

* [Extension Visual Studio](../azure-functions/functions-develop-vs.md) pour les utilisateurs Visual Studio.
* [Extension Visual Studio Core](https://code.visualstudio.com/tutorials/functions-extension/create-function) pour les utilisateurs Visual Studio Code.
* Et enfin [outils Core CLI](../azure-functions/functions-run-local.md#create-func) pour une expérience sans dépendance d’IDE multiplateforme.

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>Exécuter votre déclencheur Azure Cosmos DB localement

Vous pouvez exécuter votre [fonction Azure localement](../azure-functions/functions-develop-local.md) avec l’[émulateur Azure Cosmos DB](./local-emulator.md) pour créer et développer vos flux basés sur les événements serverless sans abonnement Azure et sans frais.

Si vous souhaitez tester des scénarios en direct dans le cloud, vous pouvez [tester gratuitement Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) sans carte de crédit ni abonnement Azure.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le flux de modification, consultez les articles suivants :

* [Présentation du flux de modification](change-feed.md)
* [Manières de lire le flux de modification](read-change-feed.md)
* [Utilisation de la bibliothèque du processeur de flux de modification](change-feed-processor.md)
* [Comment utiliser la bibliothèque du processeur de flux de modification](change-feed-processor.md)
* [Traitement de base de données serverless à l’aide d’Azure Cosmos DB et d’Azure Functions](serverless-computing-database.md)
