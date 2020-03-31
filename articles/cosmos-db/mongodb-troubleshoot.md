---
title: Résoudre des erreurs courantes dans l’API Azure Cosmos DB pour MongoDB
description: Ce document détaille les façons de résoudre les problèmes couramment rencontrés dans l’API Azure Cosmos DB pour MongoDB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941844"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Résoudre des problèmes courants dans l’API Azure Cosmos DB pour MongoDB

Azure Cosmos DB implémente les protocoles câblés de bases de données NoSQL courantes, dont MongoDB. En raison de l’implémentation d’un protocole câblé, vous pouvez interagir de manière transparente avec Azure Cosmos DB à l’aide des kits de développement logiciel (SDK) client existants, des pilotes et des outils qui fonctionnent avec des bases de données NoSQL. Azure Cosmos DB n’utilise pas le code source des bases de données pour fournir des API compatibles avec le protocole de transmission des bases de données NoSQL. Les pilotes de client MongoDB comprenant les versions de protocole câblé sont en mesure de se connecter de façon à Azure Cosmos DB.

Bien que l’API Azure Cosmos DB pour MongoDB soit compatible avec la version 3.2 du protocole câblé de MongoDB (les opérateurs et fonctionnalités de requête ajoutés dans la version 3.4 sont actuellement disponibles en préversion), il subsiste néanmoins certains codes d’erreur personnalisés correspondant à des erreurs spécifiques à Azure Cosmos DB. Cet article explique différentes erreurs, codes d’erreur ainsi que les étapes à suivre pour les résoudre.

## <a name="common-errors-and-solutions"></a>Erreurs courantes et solutions

| Error               | Code  | Description  | Solution  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Le nombre total d’unités de requête consommées est supérieur au taux d’unités de requête provisionné pour la collection et a été limité. | Adaptez le débit assigné à un conteneur ou à un ensemble de conteneurs à partir du portail Azure ou renouvelez l’opération. |
| ExceededMemoryLimit | 16501 | En tant que service multi-locataire, l’opération a dépassé l’allocation de mémoire du client. | Réduisez l’étendue de l’opération en fixant un critère de requête plus restrictif, ou contactez le support technique via le [portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Exemple : `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Le chemin de l’index correspondant à l’élément order-by spécifié est exclu / La requête order by n’a pas d’index composite correspondant à partir duquel elle peut être traitée. | 2 | La requête demande un tri sur un champ qui n’est pas indexé. | Créez un index correspondant (ou un index composite) pour la requête de tri tentée. |
| Problèmes relatifs à la version câblée de MongoDB | - | Les anciennes versions des pilotes MongoDB ne sont pas en mesure de détecter le nom du compte Azure Cosmos dans les chaînes de connexion. | Ajoutez *appName=@**accountName**@* à la fin de la chaîne de connexion de votre API Cosmos DB pour MongoDB, où ***accountName*** correspond au nom de votre compte Cosmos DB. |


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser Studio 3T](mongodb-mongochef.md) avec l’API Azure Cosmos DB pour MongoDB.
- Découvrez comment [utiliser Robo 3T](mongodb-robomongo.md) avec l’API Azure Cosmos DB pour MongoDB.
- Explorez les [exemples](mongodb-samples.md) MongoDB avec l’API Azure Cosmos DB pour MongoDB.

