---
title: Résolution des erreurs courantes dans les API d’Azure Cosmos DB pour Mongodb
description: Ce document explique comment résoudre les problèmes courants rencontrés dans les API d’Azure Cosmos DB pour MongoDB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: 5b3d3993a497240c1ea18f0fcf852c0e834f6e79
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735705"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Résoudre les problèmes courants dans les API d’Azure Cosmos DB pour MongoDB

Azure Cosmos DB implémente les protocoles de transmission de mêmes bases de données NoSQL, y compris MongoDB. En raison de l’implémentation du protocole réseau, vous pouvez interagir de manière transparente avec Azure Cosmos DB à l’aide des kits SDK clients existants, des pilotes et des outils qui fonctionnent avec les bases de données NoSQL. Azure Cosmos DB n’utilise pas de tout code source des bases de données pour fournir des API câble compatibles pour une des bases de données NoSQL. N’importe quel pilote client MongoDB qui comprend les versions du protocole câble peut se connecter à Azure Cosmos DB.

Bien que les API d’Azure Cosmos DB pour MongoDB est compatible avec la version 3.2 du protocole de câble de MongoDB (les opérateurs de requête et les fonctionnalités ajoutées dans la version 3.4 sont actuellement disponibles en version préliminaire), il existe certains codes d’erreur personnalisés qui correspondent à Azure Cosmos DB erreurs spécifiques. Cet article explique les différentes erreurs, les codes d’erreur et les étapes pour résoudre ces erreurs.

## <a name="common-errors-and-solutions"></a>Erreurs courantes et solutions

| Error               | Code  | Description  | Solution  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Le nombre total d’unités de requête consommées est supérieur au taux d’unités de requête provisionné pour la collection et a été limité. | Envisagez la mise à l’échelle le débit alloués à un conteneur ou un ensemble de conteneurs à partir du portail Azure ou vous pouvez réessayer l’opération. |
| ExceededMemoryLimit | 16501 | En tant que service multi-locataire, l’opération a dépassé l’allocation de mémoire du client. | Réduisez l’étendue de l’opération en fixant un critère de requête plus restrictif, ou contactez le support technique via le [portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exemple : `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Problèmes de version de câble de MongoDB | - | Les versions antérieures des pilotes MongoDB ne peuvent pas détecter le Azure Cosmos nom du compte dans les chaînes de connexion. | Ajouter *appName = @**accountName** @*  à la fin de votre API Cosmos DB pour la chaîne de connexion MongoDB, où ***accountName*** est le nom de votre compte Cosmos DB . |


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser Studio 3T](mongodb-mongochef.md) avec l’API Azure Cosmos DB pour MongoDB.
- Découvrez comment [utiliser Robo 3T](mongodb-robomongo.md) avec l’API Azure Cosmos DB pour MongoDB.
- Explorez les [exemples](mongodb-samples.md) MongoDB avec l’API Azure Cosmos DB pour MongoDB.

