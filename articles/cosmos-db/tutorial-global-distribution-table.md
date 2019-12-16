---
title: Tutoriel de distribution mondiale Azure Cosmos DB pour l’API Table
description: Découvrez le fonctionnement de la diffusion mondiale dans les comptes d’API Table Azure Cosmos DB et la façon de configurer la liste préférée des régions
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: e6cd574d1041908e91ad5e6629403c0e40d11c03
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870358"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Configurer la distribution mondiale Azure Cosmos DB à l’aide de l’API de table

Cet article décrit les tâches suivantes : 

> [!div class="checklist"]
> * Configurer la distribution mondiale à l’aide du portail Azure
> * Configurer la distribution mondiale à l’aide de [l’API Table](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Se connecter à une région de prédilection avec l’API Table

Pour tirer parti de la [distribution mondiale](distribute-data-globally.md), les applications clientes peuvent spécifier la liste ordonnée de préférences de régions à utiliser pour effectuer des opérations sur les documents. Pour ce faire, définissez la propriété [TableConnectionPolicy.PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet). Le Kit SDK de l’API Table d’Azure Cosmos DB sélectionne le point de terminaison qui convient le mieux pour communiquer en fonction de la configuration du compte, de la disponibilité régionale actuelle et de la liste de préférence fournie.

PreferredLocations doit contenir une liste séparée par des virgules des emplacements (de multihébergement) préférés pour les lectures. Chaque instance de client peut spécifier un sous-ensemble de ces régions dans l’ordre de préférence pour des lectures à faible latence. Les régions doivent être nommées à l’aide de leurs [noms d’affichage](https://msdn.microsoft.com/library/azure/gg441293.aspx), par exemple, `West US`.

Toutes les lectures sont envoyées vers la première région disponible dans la liste PreferredLocations. Si la demande échoue, le client passe à la région suivante dans la liste et ainsi de suite.

Le Kit SDK tente des opérations de lecture à partir des régions spécifiées dans PreferredLocations. Ainsi, par exemple, si le compte de base de données est disponible dans trois régions, mais que le client spécifie uniquement deux des régions sans écriture de PreferredLocations, aucune lecture n’est traitée hors de la région d’écriture, même en cas de basculement.

Le Kit SDK envoie automatiquement toutes les écritures vers la région d’écriture active.

Si la propriété PreferredLocations n’est pas définie, toutes les demandes seront traitées par la zone d’écriture en cours.

C’est ici que s’achève ce didacticiel. Découvrez comment gérer la cohérence de votre compte répliqué à l’échelle mondiale en lisant l’article [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md). Pour plus d’informations sur le fonctionnement de la réplication de base de données à l’échelle mondiale dans Azure Cosmos DB, voir [Diffuser des données à l’échelle mondiale avec Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez :

> [!div class="checklist"]
> * Configurer la diffusion mondiale à l’aide du portail Azure
> * Configurer la distribution mondiale à l’aide des API Table d’Azure Cosmos DB

