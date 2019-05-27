---
title: Limites dans Azure Cosmos DB
description: Cet article décrit les limites dans Azure Cosmos DB.
author: arramac
ms.author: arramac
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2019
ms.openlocfilehash: 0086327661df637dc0ae60208ed9424b4610ef0e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969491"
---
# <a name="limits-in-azure-cosmos-db"></a>Limites dans Azure Cosmos DB

Cet article fournit une vue d’ensemble des limites dans le service Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Stockage et débit

Après avoir créé un compte Azure Cosmos sous votre abonnement, vous pouvez gérer les données de votre compte par [création de bases de données, les conteneurs et les éléments](databases-containers-items.md). Vous pouvez configurer le débit au niveau d’un conteneur ou un niveau de base de données en termes de [unités (RU/s ou RUs) de requête](request-units.md). Le tableau suivant répertorie les limites de stockage et de débit par conteneur/base de données.

| Ressource | Limite par défaut |
| --- | --- |
| Unités de demande maximales par conteneur ([mode approvisionné un débit dédié](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 par défaut. Vous pouvez l’augmenter par [complétant un ticket de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) ou nous contactant via [poser Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| Unités de requête maximales par base de données ([mode approvisionné débit partagé](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 par défaut. Vous pouvez l’augmenter par [complétant un ticket de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) ou nous contactant via [poser Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| Unités de demande maximales par clé de partition (logique) | 10 000 |
| Stockage maximal sur tous les éléments par clé de partition (logique)| 10 Go |
| Nombre maximal de clés de partition (logiques) distinctes | Illimité |
| Stockage maximal par conteneur | Illimité |
| Stockage maximal par base de données | Illimité |

> [!NOTE]
> Pour obtenir des pratiques recommandées pour gérer des charges de travail qui ont des clés de partition nécessitant des limites plus élevées pour le stockage ou de débit, consultez [conception pour les clés de Partition à chaud](synthetic-partition-keys.md)
>

Un conteneur de Cosmos (ou de la base de données partagée de débit) doit avoir un débit minimum de 400 unités de requête. Que le conteneur augmente, le débit minimal pris en charge dépend également les facteurs suivants :

* Le stockage maximal utilisé dans le conteneur est exprimé en incréments de 40 unités de demande par Go de stockage consommé. Par exemple, si un conteneur contient 100 Go de données, le débit doit être au moins 4000 RUs
* Le débit maximal approvisionné jamais sur le conteneur. Le service prend en charge un débit réduit d’un conteneur à 10 % de la valeur maximale configurée. Par exemple, si votre débit a été augmenté à 10 000 RU, puis le débit défini pour la plus basse possible serait 1000 unités de requête
* Le nombre total de conteneurs que vous avez déjà créé dans une base de données partagée de débit, mesuré à 100 unités de requête par conteneur. Par exemple, si vous avez créé des cinq conteneurs au sein d’une base de données partagée de débit, le débit doit être au moins 500 unités de requête

Le débit actuel et minimale d’un conteneur ou d’une base de données peut être récupéré à partir du portail Azure ou les kits de développement. Pour plus d’informations, consultez [approvisionner le débit sur les conteneurs et les bases de données](set-throughput.md). En résumé, voici les limites minimales de RU configurées. 

| Ressource | Limite par défaut |
| --- | --- |
| Unités de requête minimale par conteneur ([mode approvisionné un débit dédié](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Unités de requête minimale par base de données ([mode approvisionné débit partagé](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Unités de requête minimale par conteneur au sein d’une base de données partagée de débit | 100 |
| Unités de requête minimale par Go de stockage consommé | 40 |

COSMOS DB prend en charge la mise à l’échelle élastique du débit (RU) par conteneur ou de la base de données via le portail ou les kits de développement logiciel. Chaque conteneur peut mettre à l’échelle de façon synchrone et immédiatement au sein d’une plage de l’échelle de 10 à 100 fois entre les valeurs minimales et maximales. Si la valeur de débit demandé est en dehors de la plage, mise à l’échelle est exécutée de façon asynchrone. Mise à l’échelle asynchrone peut prendre quelques minutes heures selon le débit demandé et la taille de stockage de données dans le conteneur.  

## <a name="control-plane-operations"></a>Opérations de plan de contrôle

Vous pouvez [approvisionner et gérer votre compte Azure Cosmos](how-to-manage-database-account.md) via le portail Azure, Azure PowerShell, Azure CLI et les modèles Azure Resource Manager. Le tableau suivant répertorie les limites par abonnement, compte et nombre d’opérations.

| Ressource | Limite par défaut |
| --- | --- |
| Comptes de base de données maximum par abonnement | 50 par défaut. Vous pouvez l’augmenter par [complétant un ticket de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) ou nous contactant via [poser Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| Nombre maximal de basculements régionaux | 1 heure par défaut. Vous pouvez l’augmenter par [complétant un ticket de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) ou nous contactant via [poser Cosmos DB](mailto:askcosmosdb@microsoft.com)|

> [!NOTE]
> Basculements régionaux s’appliquent uniquement aux comptes à région unique écritures. Comptes de l’écriture de plusieurs régions ne requièrent pas ou n’a aucune limite sur la modification de la région d’écriture.

COSMOS DB effectue automatiquement des sauvegardes de vos données à intervalles réguliers. Pour plus d’informations sur les intervalles de rétention de sauvegarde et de windows, consultez [sauvegarde en ligne et les données à la demande de restauration dans Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-container-limits"></a>Limites par conteneur

En fonction de l’API que vous utilisez, un conteneur Azure Cosmos peut représenter une collection, une table, ou le graphique. Conteneurs prennent en charge les configurations pour [contraintes de clé unique](unique-keys.md), [procédures stockées, déclencheurs et définies par le](stored-procedures-triggers-udfs.md), et [stratégie d’indexation](how-to-manage-indexing-policy.md). Le tableau suivant répertorie les limites spécifiques à des configurations dans un conteneur. 

| Ressource | Limite par défaut |
| --- | --- |
| Longueur maximale du nom de base de données ou un conteneur | 255 |
| Nombre maximal de procédures stockées par conteneur | 100 <sup>*</sup>|
| UDF maximale par conteneur | 25 <sup>*</sup>|
| Nombre maximal de chemins d’accès dans la stratégie d’indexation| 100 <sup>*</sup>|
| Nombre maximal de clés uniques par conteneur|10 <sup>*</sup>|
| Nombre maximal de chemins d’accès par la contrainte de clé unique|16 <sup>*</sup>|

<sup>*</sup> Vous pouvez augmenter une de ces limites par conteneur en contactant le Support Azure ou en nous contactant via [poser Cosmos DB](mailto:askcosmosdb@microsoft.com).

## <a name="per-item-limits"></a>Limites d’élément

En fonction de l’API que vous utilisez, un élément d’Azure Cosmos peut représenter soit un document dans une collection, une ligne dans une table, ou un nœud ou arêtes dans un graphique. Le tableau suivant indique les limites par élément dans Cosmos DB. 

| Ressource | Limite par défaut |
| --- | --- |
| Taille maximale d’un élément | 2 Mo (UTF-8 de longueur de la représentation JSON) |
| Longueur maximale de la valeur de clé de partition | 2 048 octets |
| Longueur maximale de la valeur d’id | 1 024 octets |
| Nombre maximal de propriétés par élément | Aucune limite pratique |
| Profondeur d’imbrication maximale | Aucune limite pratique |
| Longueur maximale du nom de propriété | Aucune limite pratique |
| Longueur maximale de la valeur de propriété | Aucune limite pratique |
| Longueur maximale de la valeur de propriété de chaîne | Aucune limite pratique |
| Longueur maximale de la valeur de la propriété numérique | IEEE754 double précision 64 bits |

Il n’existe aucune restriction sur les charges utiles d’élément comme numéro de profondeur de propriétés et l’imbrication, sauf pour les restrictions de longueur de clé de partition et de valeurs d’id et de l’ensemble de taille restriction de 2 Mo. Vous devrez peut-être configurer la stratégie d’indexation pour les conteneurs avec des structures complexes ou volumineux article pour réduire la consommation de RU. Consultez [modélisation des éléments dans Cosmos DB](how-to-model-partition-example.md) pour un exemple concret et les modèles pour gérer les éléments de grande taille.

## <a name="per-request-limits"></a>Limites par demande

COSMOS DB prend en charge [opérations CRUD et de requête](https://docs.microsoft.com/rest/api/cosmos-db/) par rapport à des ressources telles que des conteneurs, des éléments et des bases de données.  

| Ressource | Limite par défaut |
| --- | --- |
| Durée d’exécution maximale pour une seule opération (par exemple, une exécution de procédure stockée ou une récupération de la page requête unique)| 5 secondes |
| Taille maximale de la demande (procédure stockée, CRUD)| 2 Mo |
| Taille de réponse maximal (par exemple, une requête paginée) | 4 Mo |

Une fois une opération comme requête atteint la limite de taille de délai d’attente ou de réponse de l’exécution, elle retourne une page de résultats et un jeton de liaison pour le client pour reprendre l’exécution. Il n’existe aucune limite pratique sur la durée de qu'exécution entre les continuations de pages/une requête unique.

COSMOS DB utilise HMAC pour l’autorisation. Vous pouvez utiliser soit une clé principale, ou un [les jetons de ressource](secure-access-to-data.md) pour le contrôle d’accès précis aux ressources telles que des conteneurs, de clés ou des éléments de partition. Le tableau suivant répertorie les limites pour les jetons d’autorisation dans Cosmos DB.

| Ressource | Limite par défaut |
| --- | --- |
| Délai d’expiration maximal de jeton principal | 15 min  |
| Heure d’expiration du jeton de ressource minimale | 10 minutes  |
| Heure d’expiration du jeton de ressources maximum | 24 heures par défaut. Vous pouvez l’augmenter par [complétant un ticket de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) ou nous contactant via [poser Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| Variation d’horloge maximale pour l’autorisation de jeton| 15 min |

COSMOS DB prend en charge l’exécution de déclencheurs pendant les écritures. Le service prend en charge un maximum d’un pré-déclencheur et un post-déclencheur par opération d’écriture. 

## <a name="sql-query-limits"></a>Limites de requête SQL

COSMOS DB prend en charge l’interrogation des éléments à l’aide de [SQL](how-to-sql-query.md). Le tableau suivant décrit les restrictions dans les instructions de requête, par exemple en termes de nombre de clauses ou de longueur de la requête.

| Ressource | Limite par défaut |
| --- | --- |
| Longueur maximale de la requête SQL| 256 KO <sup>*</sup>|
| Jointures maximales par requête| 5 <sup>*</sup>|
| ANDs maximales par requête| 2000 <sup>*</sup>|
| ORs maximales par requête| 2000 <sup>*</sup>|
| UDF maximale par requête| 10 <sup>*</sup>|
| Arguments maximales par dans l’expression| 6000 <sup>*</sup>|
| Nombre maximal de points par polygone| 4096 <sup>*</sup>|

<sup>*</sup> Vous pouvez augmenter une de ces limites de requête SQL en contactant le Support Azure ou en nous contactant via [poser Cosmos DB](mailto:askcosmosdb@microsoft.com).

## <a name="mongodb-api-specific-limits"></a>Limites spécifiques des API MongoDB

COSMOS DB prend en charge le protocole filaire MongoDB pour les applications écrites par rapport à MongoDB. Vous pouvez trouver les commandes prises en charge et sur les versions de protocole [syntaxe et les fonctionnalités de prise en charge de MongoDB](mongodb-feature-support.md).

Le tableau suivant répertorie les limites spécifiques à la prise en charge des fonctionnalités de MongoDB. Autres limites de service mentionnées pour le SQL API (principale) s’appliquent également à l’API MongoDB.

| Ressource | Limite par défaut |
| --- | --- |
| Taille de la mémoire maximale pour la requête MongoDB | 40 MO |
| Durée d’exécution maximale pour les opérations de MongoDB| 30 s |

## <a name="try-cosmos-db-free-limits"></a>Essayez les limites de Cosmos DB gratuit

Le tableau suivant répertorie les limites pour le [essayez gratuitement Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) d’évaluation.

| Ressource | Limite par défaut |
| --- | --- |
| Durée de la version d’évaluation | 30 jours (peut être renouvelé autant de fois) |
| Nombre maximal de conteneurs par abonnement (API de Table SQL, Gremlin) | 1 |
| Nombre maximal de conteneurs par abonnement (API de MongoDB) | 3 |
| Débit maximal par conteneur | 5 000 |
| Débit maximal par base de données partagée et de débit | 20000 |
| Stockage total maximal par compte | 10 Go |

Essayez la distribution mondiale de Cosmos DB prend en charge dans uniquement les régions centre des États-Unis, Europe du Nord et Asie du Sud-est. Impossible de créer des tickets de support Azure pour les comptes d’essayer Azure Cosmos DB. Toutefois, le support est fourni pour les abonnés avec des plans de support existants.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur Cosmos DB principaux concepts [distribution mondiale](distribute-data-globally.md) et [partitionnement](partitioning-overview.md) et [débit approvisionné](request-units.md).

Bien démarrer avec Azure Cosmos DB grâce à l’un de nos guides de démarrage rapide :

* [Prise en main de l’API SQL Azure Cosmos DB](create-sql-api-dotnet.md)
* [Bien démarrer avec l’API Azure Cosmos DB pour MongoDB](create-mongodb-nodejs.md)
* [Prise en main de l’API Cassandra Azure Cosmos DB](create-cassandra-dotnet.md)
* [Prise en main de l’API Gremlin Azure Cosmos DB](create-graph-dotnet.md)
* [Prise en main de l’API Table Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Essayez gratuitement Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
