---
title: Quotas du service Azure Cosmos DB
description: Quotas et limites par défaut du service Azure Cosmos DB sur différents types de ressources.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 5681efc202df511745532e4a314e88b319e9880a
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623401"
---
# <a name="azure-cosmos-db-service-quotas"></a>Quotas du service Azure Cosmos DB

Cet article fournit une vue d’ensemble des quotas par défaut appliqués à différentes ressources dans Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Stockage et débit

Une fois que vous avez créé un compte Azure Cosmos dans votre abonnement, vous pouvez y gérer des données en [créant des bases de données, des conteneurs et des éléments](databases-containers-items.md). Vous pouvez provisionner le débit au niveau d’un conteneur ou d’une base de données en termes d’[unités de requête (RU/s)](request-units.md). Le tableau suivant liste les limites de stockage et de débit par conteneur/base de données.

| Ressource | Limite par défaut |
| --- | --- |
| Nombre maximal d’unités de requête par conteneur ([mode provisionné avec débit dédié](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 par défaut. Vous pouvez l’augmenter en [soumettant un ticket de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). |
| Nombre maximal d’unités de requête par base de données ([mode provisionné avec débit partagé](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 par défaut. Vous pouvez l’augmenter en [soumettant un ticket de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). |
| Nombre maximal d’unités de requête par clé de partition (logique) | 10 000 |
| Volume de stockage maximal sur tous les éléments par clé de partition (logique)| 20 Go |
| Nombre maximal de clés de partition (logiques) distinctes | Illimité |
| Volume de stockage maximal par conteneur | Illimité |
| Volume de stockage maximal par base de données | Illimité |
| Taille maximale des pièces jointes par compte (la fonctionnalité de pièce jointe est dépréciée) | 2 Go |
| Unités de requête minimales requises par 1 Go | 10 RU/s |

> [!NOTE]
> Pour découvrir les meilleures pratiques en matière de gestion des charges de travail qui ont des clés de partition nécessitant des limites plus élevées de stockage ou de débit, voir [Créer une clé de partition synthétique](synthetic-partition-keys.md).
>

Un conteneur Cosmos (ou une base de données de débit partagé) doit avoir un débit minimum de 400 unités de requête. À mesure que le conteneur croît, le débit minimal pris en charge dépend également des facteurs suivants :

* Le débit minimal que vous pouvez définir sur un conteneur dépend du débit maximal déjà approvisionné sur le conteneur. Par exemple, si votre débit a été porté à 10 000 RU, le plus petit débit provisionné possible serait de 1000 unités de requête.
* Le débit minimal sur une base de données de débit partagé dépend également du nombre total de conteneurs que vous avez créés dans une base de données de débit partagé, mesuré à 100 unités de requête par conteneur. Par exemple, si vous avez créé cinq conteneurs au sein d’une base de données de débit partagé, le débit doit être au moins de 500 unités de requête.

Les débits actuel et minimal d’un conteneur ou d’une base de données peuvent être récupérés à partir du portail Azure ou des SDK. Pour plus d’informations, consultez [Provisionner le débit sur les conteneurs et les bases de données](set-throughput.md). 

> [!NOTE]
> Dans certains cas, vous pourrez peut-être réduire le débit à moins de 10 %. Utilisez l’API pour récupérer le nombre minimal d’unités de requête par conteneur.
>

En résumé, voici les limites minimales de RU provisionnées. 

| Ressource | Limite par défaut |
| --- | --- |
| Nombre minimal d’unités de requête par conteneur ([mode provisionné avec débit dédié](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Nombre minimal d’unités de requête par base de données ([mode provisionné avec débit partagé](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Nombre minimal d’unités de requête par conteneur au sein d’une base de données de débit partagé | 100 |

Cosmos DB prend en charge la mise à l’échelle élastique du débit (RU) par conteneur ou base de données par le biais des kits SDK ou du portail. Chaque conteneur peut être mis à l’échelle de façon synchrone et immédiate au sein d’une plage d’échelle de facteur 10 à 100 entre les valeurs minimale et maximale. Si la valeur de débit demandée est en dehors de la plage, la mise à l’échelle est exécutée de façon asynchrone. La mise à l’échelle asynchrone peut prendre quelques minutes à quelques heures selon le débit demandé et la taille de stockage de données dans le conteneur.  

## <a name="control-plane-operations"></a>Opérations du plan de contrôle

Vous pouvez [provisionner et gérer votre compte Azure Cosmos](how-to-manage-database-account.md) avec le portail Azure, Azure PowerShell, Azure CLI et des modèles Azure Resource Manager. Le tableau suivant liste les limites par abonnement, compte et nombre d’opérations.

| Ressource | Limite par défaut |
| --- | --- |
| Nombre maximal de comptes de base de données par abonnement | 50 par défaut. Vous pouvez l’augmenter en [soumettant un ticket de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).|
| Nombre maximal de basculements régionaux | 1/heure par défaut. Vous pouvez l’augmenter en [soumettant un ticket de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).|

> [!NOTE]
> Les basculements régionaux s’appliquent uniquement à des comptes d’écriture monorégions. Les comptes d’écriture multirégions ne requièrent pas ou n’ont pas de limite quant au changement de région d’écriture.

Cosmos DB sauvegarde automatiquement vos données à intervalles réguliers. Pour plus d’informations sur les fenêtres et intervalles de conservation des sauvegardes, consultez [Sauvegarde en ligne et restauration de données à la demande dans Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Limites par compte

| Ressource | Limite par défaut |
| --- | --- |
| Nombre maximal de bases de données | Illimité |
| Nombre maximal de conteneurs par base de données avec un débit partagé |25 |
| Nombre maximal de conteneurs par base de données ou compte avec un débit dédié  |illimitée |
| Nombre maximal de régions | Aucune limite (toutes les régions Azure) |

## <a name="per-container-limits"></a>Limites par conteneur

En fonction de l’API que vous utilisez, un conteneur Azure Cosmos peut représenter une collection, une table ou un graphe. Les conteneurs prennent en charge la configuration de [contraintes de clé unique](unique-keys.md), de [procédures stockées, déclencheurs et fonctions définies par l’utilisateur](stored-procedures-triggers-udfs.md) et de [stratégies d’indexation](how-to-manage-indexing-policy.md). Le tableau suivant liste les limites propres aux configurations dans un conteneur. 

| Ressource | Limite par défaut |
| --- | --- |
| Longueur maximale du nom de la base de données ou du conteneur | 255 |
| Nombre maximal de procédures stockées par conteneur | 100 <sup>*</sup>|
| Nombre maximal de fonctions définies par l’utilisateur par conteneur | 25 <sup>*</sup>|
| Nombre maximal de chemins dans la stratégie d’indexation| 100 <sup>*</sup>|
| Nombre maximal de clés uniques par conteneur|10 <sup>*</sup>|
| Nombre maximal de chemins par contrainte de clé unique|16 <sup>*</sup>|

<sup>*</sup> Vous pouvez augmenter ces limites par conteneur en contactant le Support Azure.

## <a name="per-item-limits"></a>Limites par élément

En fonction de l’API que vous utilisez, un élément Azure Cosmos peut représenter un document d’une collection, une ligne de table, un nœud de graphe ou une arête de graphe. Le tableau suivant indique les limites par élément dans Cosmos DB. 

| Ressource | Limite par défaut |
| --- | --- |
| Taille maximale d’un élément | 2 Mo (longueur UTF-8 de la représentation JSON) |
| Longueur maximale de la valeur de clé de partition | 2 048 octets |
| Longueur maximale de la valeur d’ID | 1 023 octets |
| Nombre maximal de propriétés par élément | Aucune limite pratique |
| Profondeur d’imbrication maximale | Aucune limite pratique |
| Longueur maximale du nom d’une propriété | Aucune limite pratique |
| Longueur maximale de la valeur d’une propriété | Aucune limite pratique |
| Longueur maximale d’une valeur de propriété de chaîne | Aucune limite pratique |
| Longueur maximale d’une valeur de propriété numérique | 64 bits double précision (norme IEEE754) |

Aucune restriction ne pèse sur les charges utiles d’élément comme le nombre de propriétés et la profondeur d’imbrication, sauf en ce qui concerne la longueur des valeurs d’ID et de clé de partition, et la taille globale est limitée à 2 Mo. Vous devrez peut-être configurer une stratégie d’indexation pour les conteneurs qui ont des structures comportant des éléments grands ou complexes pour réduire la consommation de RU. Consultez [Modélisation des éléments dans Cosmos DB](how-to-model-partition-example.md) pour découvrir un exemple concret et des modèles permettant de gérer de grands éléments.

## <a name="per-request-limits"></a>Limites par demande

Azure Cosmos DB prend en charge les [opérations CRUD et de requête](https://docs.microsoft.com/rest/api/cosmos-db/) sur des ressources telles que des conteneurs, des éléments et des bases de données. Il prend également en charge les [requêtes de lots transactionnels](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch) sur plusieurs éléments ayant la même clé de partition dans un conteneur.

| Ressource | Limite par défaut |
| --- | --- |
| Durée d’exécution maximale pour une seule opération (par exemple, l’exécution d’une procédure stockée ou la récupération d’une page de requête unique)| 5 secondes |
| Taille maximale de la demande (par exemple : procédure stockée, CRUD)| 2 Mo |
| Taille maximale de la réponse (par exemple, requête paginée) | 4 Mo |
| Nombre maximal d’opérations dans un lot transactionnel | 100 |

Dès qu’une opération telle qu’une requête atteint le délai d’expiration de l’exécution ou la taille limite de la réponse, elle retourne une page de résultats et un jeton de continuation au client pour reprendre l’exécution. Il n’existe aucune limite pratique quant à la durée de l’exécution d’une requête unique entre les continuations et les pages.

Cosmos DB utilise HMAC pour l’autorisation. Vous pouvez utiliser une clé principale ou un [jeton de ressource](secure-access-to-data.md) pour le contrôle précis de l’accès aux ressources telles que les conteneurs, les clés de partition ou les éléments. Le tableau suivant liste les limites pour les jetons d’autorisation dans Cosmos DB.

| Ressource | Limite par défaut |
| --- | --- |
| Délai d’expiration maximal d’un jeton principal | 15 min  |
| Délai d’expiration minimal d’un jeton de ressource | 10 min  |
| Délai d’expiration maximal d’un jeton de ressource | 24 h par défaut. Vous pouvez l’augmenter en [soumettant un ticket de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).|
| Variation d’horloge maximale pour l’autorisation de jeton| 15 min |

Cosmos DB prend en charge l’exécution de déclencheurs pendant les écritures. Le service prend en charge un maximum d’un pré-déclencheur et d’un post-déclencheur par opération d’écriture. 

## <a name="autopilot-mode-limits"></a>Limites du mode Autopilot

Reportez-vous à l’article [Autopilot](provision-throughput-autopilot.md#autopilot-limits) pour connaître les limites de débit et de stockage en mode Autopilot.

## <a name="sql-query-limits"></a>Limites de requête SQL

Cosmos DB prend en charge l’interrogation d’éléments à l’aide de [SQL](how-to-sql-query.md). Le tableau suivant décrit les restrictions dans les instructions de requête, par exemple en termes de nombre de clauses ou de longueur de requête.

| Ressource | Limite par défaut |
| --- | --- |
| Longueur maximale d’une requête SQL| 256 Ko <sup>*</sup>|
| Nombre maximal de jointures par requête| 5 <sup>*</sup>|
| Nombre maximal d’opérateurs AND par requête| 2 000 <sup>*</sup>|
| Nombre maximal d’opérateurs OR par requête| 2 000 <sup>*</sup>|
| Nombre maximal de fonctions définies par l’utilisateur par requête| 10 <sup>*</sup>|
| Nombre maximal d’arguments par expression IN| 6 000 <sup>*</sup>|
| Nombre maximal de points par polygone| 4 096 <sup>*</sup>|

<sup>*</sup> Vous pouvez augmenter ces limites de requête SQL en contactant le Support Azure.

## <a name="mongodb-api-specific-limits"></a>Limites propres à l’API MongoDB

Cosmos DB prend en charge le protocole de transmission MongoDB pour les applications écrites par rapport à MongoDB. Vous trouverez les versions de protocole et les commandes prises en charge dans l’article [Fonctionnalités et syntaxe MongoDB prises en charge](mongodb-feature-support.md).

Le tableau suivant liste les limites propres à la prise en charge des fonctionnalités MongoDB. D’autres limites de service mentionnées pour l’API SQL (Core) s’appliquent également à l’API MongoDB.

| Ressource | Limite par défaut |
| --- | --- |
| Taille de mémoire maximale pour les requêtes MongoDB | 40 Mo |
| Durée d’exécution maximale des opérations MongoDB| 30 s |

## <a name="try-cosmos-db-free-limits"></a>Limites liées à l’essai gratuit de Cosmos DB

Le tableau suivant liste les limites liées à l’[essai gratuit d’Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/).

| Ressource | Limite par défaut |
| --- | --- |
| Durée de l’essai | 30 jours (peut être renouvelé indéfiniment) |
| Nombre maximal de conteneurs par abonnement (SQL, API Table, Gremlin) | 1 |
| Nombre maximal de conteneurs par abonnement (API MongoDB) | 3 |
| Débit maximal par conteneur | 5 000 |
| Débit maximal par base de données de débit partagé | 20000 |
| Volume total de stockage maximal par compte | 10 Go |

L’essai de Cosmos DB ne prend en charge la distribution mondiale que dans les régions USA Centre, Europe Nord et Asie Sud-Est. Vous ne pouvez pas créer de tickets de support Azure pour les comptes d’essai d’Azure Cosmos DB. Toutefois, un support est fourni pour les abonnés détenant des plans de support.

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail les concepts de base de Cosmos DB de [distribution mondiale](distribute-data-globally.md), de [partitionnement](partitioning-overview.md) et de [débit provisionné](request-units.md).

Bien démarrer avec Azure Cosmos DB grâce à l’un de nos guides de démarrage rapide :

* [Prise en main de l’API SQL Azure Cosmos DB](create-sql-api-dotnet.md)
* [Bien démarrer avec l’API Azure Cosmos DB pour MongoDB](create-mongodb-nodejs.md)
* [Prise en main de l’API Cassandra Azure Cosmos DB](create-cassandra-dotnet.md)
* [Prise en main de l’API Gremlin Azure Cosmos DB](create-graph-dotnet.md)
* [Prise en main de l’API Table Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Essayez gratuitement Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
