---
title: Configurer les paramètres de Cosmos DB dans l’API Azure pour FHIR
description: Cet article décrit comment configurer les paramètres de Cosmos DB dans l’API Azure pour FHIR
author: matjazl
ms.service: healthcare-apis
ms.topic: reference
ms.date: 05/22/2019
ms.author: matjazl
ms.openlocfilehash: 3d4f2ade64c55c757634e9c2b68aa9dcef51f117
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67033763"
---
# <a name="configure-cosmos-db-settings"></a>Configurer les paramètres de Cosmos DB 

L’API Azure pour FHIR utilise Cosmos DB comme base de données sous-jacente pour stocker les données FHIR. Quand vous configurez le nouveau service API Azure pour FHIR, vous avez la possibilité de spécifier le nombre d’unités de requête (RU) dans Cosmos DB que le service utilisera.

Pour en savoir plus sur les unités de requête Cosmos DB, consultez [Unités de requête dans Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/request-units)

Pour changer ce paramètre dans le portail Azure, accédez à votre API Azure pour FHIR et ouvrez le panneau Cosmos DB, puis changez le débit à la valeur souhaitée en fonction des performances requises. Vous pouvez choisir une valeur maximale de 10 000 RU/s.* Si vous avez besoin d’une valeur plus élevée, contactez le support Azure.

***Remarque :** Avec une valeur plus élevée, vous augmentez le débit Cosmos DB et donc le coût du service.

![](media/cosmosdb/cosmosdb-config.png)
