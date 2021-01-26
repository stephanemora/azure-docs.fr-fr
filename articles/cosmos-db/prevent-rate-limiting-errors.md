---
title: Empêcher les erreurs de limitation de débit pour les opérations de l’API Azure Cosmos DB pour MongoDB.
description: Découvrez comment empêcher les opérations de votre API Azure Cosmos DB pour MongoDB de déclencher des erreurs de limitation de débit avec la fonctionnalité SSR (Server Side Retry).
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 73c2aba3028f42621f241bd8f295e83e0ef96e68
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540343"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>Empêcher les erreurs de limitation de débit pour les opérations de l’API Azure Cosmos DB pour MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Les opérations de l’API Azure Cosmos DB pour MongoDB peuvent échouer avec des erreurs de limitation de débit (16500/429) si elles dépassent la limite de débit d’une collection (RU). 

Vous pouvez activer la fonctionnalité de nouvelle tentative côté serveur (Server Side Retry, SSR) et laisser le serveur réessayer automatiquement ces opérations. Les demandes font l’objet d’une nouvelle tentative après un bref délai pour toutes les collections dans votre compte. Cette fonctionnalité est une alternative pratique à la gestion des erreurs de limitation de débit dans l’application cliente.


## <a name="use-the-azure-portal"></a>Utilisation du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Accédez à votre API Azure Cosmos DB pour le compte MongoDB.

1. Accédez au volet **Fonctionnalités** dans la section **Paramètres**.

1. Sélectionnez **Server Side Retry** (Nouvelle tentative côté serveur).

1. Cliquez sur **Activer** pour activer cette fonctionnalité pour tous les collections dans votre compte.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="Capture d’écran de la fonctionnalité de nouvelle tentative côté serveur pour l’API Azure Cosmos DB pour MongoDB":::


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la résolution des erreurs courantes, consultez cet article :

* [Résoudre des problèmes courants dans l’API Azure Cosmos DB pour MongoDB](mongodb-troubleshoot.md)
