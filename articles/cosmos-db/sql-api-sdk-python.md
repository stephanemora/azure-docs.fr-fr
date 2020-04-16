---
title: 'Azure Cosmos DB : API, SDK et ressources SQL Python'
description: Découvrez l’API et le Kit SDK Python SQL, y compris les dates de publication, les dates de suppression et les modifications apportées entre chaque version du Kit SDK Python Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: b81a3921ec11d589dadbdebd698ab9ad67d7649c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982903"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Kit de développement logiciel Python Azure Cosmos DB pour l’API SQL : Notes de publication et ressources
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Flux de modification .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Java asynchrone](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [API REST Resource Provider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Exécuteur en bloc – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Exécuteur en bloc – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Téléchargement du Kit de développement logiciel (SDK)**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Documentation de l’API**|[Documentation de référence sur l’API Python](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**Instructions d’installation du Kit de développement logiciel (SDK)**|[Instructions d’installation du Kit de développement logiciel (SDK) Python](https://github.com/Azure/azure-cosmos-python)|
|**Contribution au Kit de développement logiciel (SDK)**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Prise en main**|[Bien démarrer avec le Kit de développement logiciel (SDK) Python](sql-api-python-application.md)|
|**Plateforme actuellement prise en charge**|[Python 2.7](https://www.python.org/downloads/) et [Python 3.5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Notes de publication

### <a name="302"></a><a name="3.0.2"/>3.0.2
* Ajout de la prise en charge du type de données multipolygones
* Correctif de bogue dans la stratégie de nouvelle tentative de lecture de session
* Correctif de bogue pour les problèmes de remplissage incorrect lors du décodage de chaînes base 64

### <a name="301"></a><a name="3.0.1"/>3.0.1
* Correction de bogue dans LocationCache
* Correction de bogue au niveau de la logique de nouvelle tentative de point de terminaison
* Documentation mise à jour

### <a name="300"></a><a name="3.0.0"/>3.0.0
* Prise en charge des écritures multirégions.
* Espace de noms remplacé par azure.cosmos.
* Concepts Collection et Document renommés en Conteneur et Élément ; « document_client » renommé en « cosmos_client ». 

### <a name="233"></a><a name="2.3.3"/>2.3.3
* Ajout de la prise en charge de proxy
* Ajout de la prise en charge de la lecture du flux de modification
* Ajout de la prise en charge des en-têtes de quota de collection
* Correction du problème de jetons de session volumineuse
* Correction de bogue au niveau de l’API ReadMedia
* Correction de bogue dans le cache de plage de clés de partition

### <a name="232"></a><a name="2.3.2"/>2.3.2
* Support supplémentaire pour les nouvelles tentatives par défaut concernant les problèmes de connexion.

### <a name="231"></a><a name="2.3.1"/>2.3.1
* Documentation mise à jour pour référencer Azure Cosmos DB au lieu d’Azure DocumentDB.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Cette version du SDK nécessite la dernière version de l’[émulateur Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Résolution de bogue pour le dictionnaire d’agrégation.
* Résolution de bogue pour la suppression des barres obliques dans le lien de ressource.
* Ajout de tests pour l’encodage Unicode.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Prise en charge ajoutée pour un nouveau niveau de cohérence nommé ConsistentPrefix.


### <a name="210"></a><a name="2.1.0"/>2.1.0
* Ajout de la prise en charge des requêtes d’agrégation (COUNT, MIN, MAX, SUM et AVG).
* Ajout d’une option permettant de désactiver la vérification TLS en cas d’exécution sur l’émulateur Cosmos DB.
* Suppression de la restriction du module de demandes dépendantes qui devait correspondre exactement à la version 2.10.0.
* Débit minimal réduit sur les collections partitionnées de 10 100 unités de demande/s à 2 500 unités de demande/s.
* Ajout de la prise en charge de l’activation de la journalisation de script pendant l’exécution de la procédure stockée.
* Version de l’API REST passée à « 2017-01-19 » à l’occasion de cette publication.

### <a name="201"></a><a name="2.0.1"/>2.0.1
* Modifications éditoriales apportées aux commentaires de documentation.

### <a name="200"></a><a name="2.0.0"/>2.0.0
* Ajout de la prise en charge de Python 3.5.
* Ajout de la prise en charge du regroupement de connexions à l’aide d’un module de demandes.
* Ajout de la prise en charge de la cohérence de session.
* Ajout de la prise en charge des requêtes TOP/ORDERBY pour les collections partitionnées.

### <a name="190"></a><a name="1.9.0"/>1.9.0
* Ajout de la prise en charge d’une stratégie de nouvelle tentative pour les requêtes limitées. (Les requêtes limitées reçoivent une exception de taux de requête excessif, code d’erreur 429.) Par défaut, Azure Cosmos DB accepte neuf nouvelles tentatives pour chaque requête lorsque le code d’erreur 429 est renvoyé, conformément à l’heure de retryAfter spécifiée dans l’en-tête de réponse. Il est désormais possible de définir un intervalle fixe de nouvelle tentative dans la propriété RetryOptions sur l’objet ConnectionPolicy, si vous souhaitez ignorer le temps retryAfter retourné par le serveur entre chaque nouvelle tentative. Azure Cosmos DB attend maintenant au maximum 30 secondes pour chaque requête limitée (quel que soit le nombre de nouvelles tentatives) et renvoie la réponse avec un code d’erreur 429. Cette durée peut également être remplacée dans la propriété RetryOptions sur l’objet ConnectionPolicy.
* Cosmos DB renvoie maintenant x-ms-throttle-retry-count et x-ms-throttle-retry-wait-time-ms comme en-têtes de réponse dans chaque requête pour signaler le nombre limite de nouvelles tentatives et le cumul de temps d’attente observé par la requête entre les nouvelles tentatives.
* Suppression de la classe RetryPolicy et de la propriété correspondante (retry_policy) exposées sur la classe document_client, et introduction d’une classe RetryOptions qui expose la propriété RetryOptions sur la classe ConnectionPolicy pouvant être utilisée pour substituer certaines des options de nouvelle tentative par défaut.

### <a name="180"></a><a name="1.8.0"/>1.8.0
* Ajout de la prise en charge des comptes de base de données de plusieurs régions.

### <a name="170"></a><a name="1.7.0"/>1.7.0
* Ajout de la fonctionnalité de durée de vie (TTL) pour les documents.

### <a name="161"></a><a name="1.6.1"/>1.6.1
* Résolution des bogues liés au partitionnement côté serveur pour autoriser des caractères spéciaux dans le chemin d’accès à la clé de partition.

### <a name="160"></a><a name="1.6.0"/>1.6.0
* Implémentation des [collections partitionnées](partition-data.md) et des [niveaux de performances définis par l’utilisateur](performance-levels.md). 

### <a name="150"></a><a name="1.5.0"/>1.5.0
* Ajoutez des programmes de résolution de partitions par hachage et par spécification de plages de valeurs pour vous aider lors du partitionnement des applications sur plusieurs partitions.

### <a name="142"></a><a name="1.4.2"/>1.4.2
* Implémentation de l’opération Upsert. Nouvelles méthodes UpsertXXX ajoutées pour prendre en charge la fonctionnalité Upsert.
* Implémenter l'ID en fonction du routage. Aucune modification d'API publique, toutes les modifications en interne.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Prise en charge de l'index géospatial.
* Validation de la propriété ID pour toutes les ressources. Les ID des ressources ne peuvent pas contenir les caractères ?, /, #, \, ou se terminer par un espace.
* Ajoute le nouvel en-tête « progression de la transformation de l'index » à ResourceResponse.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Implémente la stratégie d’indexation V2.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Prend en charge la connexion proxy.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* Kit de développement logiciel (SDK) GA

## <a name="release--retirement-dates"></a>Dates de lancement et de suppression
Microsoft envoie une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et fonctions, ainsi que les optimisations sont uniquement ajoutées au Kit SDK actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du Kit SDK dès que possible. 

Le service rejette toute requête envoyée à Cosmos DB à l’aide d’un Kit SDK supprimé.

> [!WARNING]
> Toutes les versions du Kit SDK Python pour l’API SQL antérieures à la version **1.0.0** ont été supprimées le **29 février 2016**. 
> 
> 

> [!WARNING]
> Toutes les versions 1.x et 2.x du Kit SDK Python pour l’API SQL seront supprimées le **30 août 2020**. 
> 
> 

<br/>

| Version | Date de sortie | Date de suppression |
| --- | --- | --- |
| [3.0.2](#3.0.2) |15 novembre 2018 |--- |
| [3.0.1](#3.0.1) |04 octobre 2018 |--- |
| [2.3.3](#2.3.3) |8 septembre 2018 |30 août 2020 |
| [2.3.2](#2.3.2) |8 mai 2018 |30 août 2020 |
| [2.3.1](#2.3.1) |21 décembre 2017 |30 août 2020 |
| [2.3.0](#2.3.0) |10 novembre 2017 |30 août 2020 |
| [2.2.1](#2.2.1) |29 septembre 2017 |30 août 2020 |
| [2.2.0](#2.2.0) |10 mai 2017 |30 août 2020 |
| [2.1.0](#2.1.0) |1er mai 2017 |30 août 2020 |
| [2.0.1](#2.0.1) |30 octobre 2016 |30 août 2020 |
| [2.0.0](#2.0.0) |29 septembre 2016 |30 août 2020 |
| [1.9.0](#1.9.0) |7 juillet 2016 |30 août 2020 |
| [1.8.0](#1.8.0) |14 juin 2016 |30 août 2020 |
| [1.7.0](#1.7.0) |26 avril 2016 |30 août 2020 |
| [1.6.1](#1.6.1) |8 avril 2016 |30 août 2020 |
| [1.6.0](#1.6.0) |29 mars 2016 |30 août 2020 |
| [1.5.0](#1.5.0) |3 janvier 2016 |30 août 2020 |
| [1.4.2](#1.4.2) |6 octobre 2015 |30 août 2020 |
| 1.4.1 |6 octobre 2015 |30 août 2020 |
| [1.2.0](#1.2.0) |6 août 2015 |30 août 2020 |
| [1.1.0](#1.1.0) |9 juillet 2015 |30 août 2020 |
| [1.0.1](#1.0.1) |25 mai 2015 |30 août 2020 |
| [1.0.0](#1.0.0) |7 avril 2015 |30 août 2020 |
| 0.9.4-prelease |14 janvier 2015 |29 février 2016 |
| 0.9.3-prelease |9 décembre 2014 |29 février 2016 |
| 0.9.2-prelease |25 novembre 2014 |29 février 2016 |
| 0.9.1-prelease |23 septembre 2014 |29 février 2016 |
| 0.9.0-prelease |21.08.14 |29 février 2016 |

## <a name="faq"></a>Questions fréquentes (FAQ)
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur Cosmos DB, consultez la page du service [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

