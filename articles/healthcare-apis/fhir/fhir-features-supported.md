---
title: Fonctionnalités de FHIR prises en charge dans Azure - API Azure pour FHIR
description: Cet article détaille les fonctionnalités de la spécification de FHIR qui sont implémentées dans l’API Azure pour FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/15/2021
ms.author: cavoeg
ms.openlocfilehash: de99d6b5480a8e8262aba9d40826f69862630961
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2021
ms.locfileid: "111592989"
---
# <a name="features"></a>Fonctionnalités

L’API Azure pour FHIR fournit un déploiement complètement managé du serveur FHIR Microsoft pour Azure. Le serveur est une implémentation de la [FHIR](https://hl7.org/fhir) standard. Ce document répertorie les principales fonctionnalités du serveur FHIR.

## <a name="fhir-version"></a>Version FHIR

Dernière version prise en charge : `4.0.1`

Versions antérieures également prises en charge : `3.0.2`

## <a name="rest-api"></a>API REST

| API                            | Prise en charge - PaaS | Prise en charge - OSS (SQL) | Prise en charge - OSS (Cosmos DB) | Commentaire                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| lire                           | Oui       | Oui       | Oui       |                                                     |
| vread                          | Oui       | Oui       | Oui       |                                                     |
| update                         | Oui       | Oui       | Oui       |                                                     |
| update with optimistic locking | Oui       | Oui       | Oui       |                                                     |
| update (conditional)           | Oui       | Oui       | Oui       |                                                     |
| patch                          | Non        | Non        | Non        |                                                     |
| supprimer                         | Oui       | Oui       | Oui       |  Voir la remarque ci-dessous.                                   |
| delete (conditional)           | Oui       | Oui        | Oui        |                                                     |
| history                        | Oui       | Oui       | Oui       |                                                     |
| create                         | Oui       | Oui       | Oui       | Prend en charge POST et PUT                               |
| create (conditional)           | Oui       | Oui       | Oui       | Problème [no 1382](https://github.com/microsoft/fhir-server/issues/1382) |
| recherche                         | Partiel   | Partiel   | Partiel   | Consultez [vue d’ensemble de la recherche de FHIR](overview-of-search.md).                           |
| recherche chaînée                 | Partiel       | Oui       | Partiel   | Voir la remarque 2 ci-dessous.                                   |
| recherche chaînée inversée         | Partiel       | Oui       | Partiel   | Voir la remarque 2 ci-dessous.                                   |
| capabilities                   | Oui       | Oui       | Oui       |                                                     |
| lot                          | Oui       | Oui       | Oui       |                                                     |
| transaction                    | Non        | Oui       | Non        |                                                     |
| paging                         | Partiel   | Partiel   | Partiel   | `self` et `next` sont pris en charge                     |
| intermediaries                 | Non        | Non        | Non        |                                                     |

> [!Note]
> Après la suppression, telle qu’elle est définie par la spécification FHIR, les lectures suivantes d’une ressource qui ne sont pas propres à la version renvoient un code de statut HTTP 410, et la ressource ne figure plus dans les résultats de recherche. L’API Azure pour FHIR permet également de supprimer entièrement la ressource (y compris l’historique). Pour supprimer entièrement la ressource, vous pouvez passer une valeur de paramètre `hardDelete` définie sur true (`DELETE {server}/{resource}/{id}?hardDelete=true`). Si vous ne transmettez pas ce paramètre ou que vous lui donnez la valeur false, les versions historiques de la ressource restent disponibles.


 **Remarque 2**
* Ajoute la prise en charge MVP pour la recherche de FHIR chaînée et inversée dans CosmosDB. 

  Dans l’API Azure pour FHIR et le serveur FHIR Open source avec Cosmos, la recherche chaînée et la recherche chaînée par chaîne est une implémentation MVP. Pour effectuer une recherche chaînée sur Cosmos DB, l’implémentation parcourt l’expression de recherche et émet des sous-requêtes pour résoudre les ressources correspondantes. Cette opération est effectuée pour chaque niveau de l’expression. Si une requête retourne plus de 100 résultats, une erreur est générée. Par défaut, la recherche chaînée se trouve derrière un indicateur de fonctionnalité. Pour utiliser la recherche chaînée sur Cosmos DB, utilisez l’en-tête `x-ms-enable-chained-search: true` . Pour plus d’informations, consultez la page [PR 1695](https://github.com/microsoft/fhir-server/pull/1695).

## <a name="extended-operations"></a>Opérations étendues

Toutes les opérations prises en charge qui étendent l’API RESTful.

| Type de paramètre de recherche | Prise en charge - PaaS | Prise en charge - OSS (SQL) | Prise en charge - OSS (Cosmos DB) | Commentaire |
|------------------------|-----------|-----------|-----------|---------|
| $export (système entier) | Oui       | Oui       | Oui       |         |
| Patient/$export        | Oui       | Oui       | Oui       |         |
| Group/$export          | Oui       | Oui       | Oui       |         |
| $convert-data          | Oui       | Oui       | Oui       |         |
| $validate              | Oui       | Oui       | Oui       |         |
| $member-correspondance          | Oui       | Oui       | Oui       |         |
| $patient-tout    | Non        | Non        | Oui       |         |

## <a name="persistence"></a>Persistance

Le serveur FHIR Microsoft dispose d’un module de persistance enfichable (voir [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence)).

Le code open source de FHIR Server inclut une implémentation pour [Azure Cosmos DB](../../cosmos-db/index-overview.md) et [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB est une base de données multimodèle (SQL API, MongoDB API, etc.) distribuée à l’échelle mondiale. Il prend en charge différents [niveaux de cohérence](../../cosmos-db/consistency-levels.md). Le modèle de déploiement par défaut configure le serveur FHIR avec une cohérence `Strong`, mais la stratégie de cohérence peut être modifiée (généralement assouplie) en envoyant une demande individuelle à l’aide de l’en-tête de demande `x-ms-consistency-level`.

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Le serveur FHIR utilise [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) pour contrôler l’accès. Plus précisément, le contrôle d’accès en fonction du rôle (RBAC) est appliqué, si le paramètre de configuration `FhirServer:Security:Enabled` a la valeur `true`. De plus, l’en-tête de requête `Authorization` de toutes les requêtes (à l’exception de `/metadata`) envoyées au serveur FHIR Server doit avoir la valeur `Bearer <TOKEN>`. Le jeton doit contenir au moins un rôle tel que défini dans la revendication `roles`. Une demande sera autorisée si le jeton contient un rôle qui permet l’application de l’action spécifiée sur la ressource spécifiée.

Actuellement, les actions autorisées pour un rôle donné sont appliquées *à l’ensemble* de l’API.

## <a name="service-limits"></a>Limites du service

* [**Unités de requête (RU)**](../../cosmos-db/concepts-limits.md) : vous pouvez configurer jusqu’à 10 000 unités de requête dans le portail pour l’API Azure pour FHIR. Vous aurez besoin d’un minimum de 400 ou de 40 unités de capacité/Go, selon la valeur la plus grande. Si vous avez besoin de plus de 10 000 unités de requête, vous pouvez envoyer un ticket de support pour augmenter ce quota. Le maximum disponible est 1 000 000.

* **Taille de bundle** : chaque bundle est limité à 500 éléments.

* **Taille des données** : les éléments Données/Documents doivent chacun être un peu inférieurs à 2 Mo.

* **Limite d’abonnement** : par défaut, chaque abonnement est limité à un maximum de 10 instances de serveur FHIR. Si vous avez besoin d’un plus grand nombre d’instances par abonnement, ouvrez un ticket de support et fournissez des informations détaillées sur vos besoins.

* **Connexions et instances simultanées** : par défaut, vous disposez de 15 connexions simultanées sur deux instances du cluster (pour un total de 30 demandes simultanées). Si vous avez besoin de plus de demandes simultanées, ouvrez un ticket de support et fournissez des informations détaillées sur vos besoins.

## <a name="performance-expectations"></a>Attentes en matière de niveau de performance

Les performances du système dépendent du nombre d’unités de requête, des connexions simultanées et du type d’opérations que vous effectuez (put, poster, etc.). Vous trouverez ci-dessous quelques plages générales de ce que vous pouvez attendre en fonction des unités de requête configurées. En règle générale, les performances évoluent de façon linéaire avec l’augmentation des unités de requête :

| Nombre d’unités de requête | Ressources/s |    Stockage max. (Go)*    |
|----------|---------------|--------|                 
| 400      | 5-10          |     10   |
| 1 000    | 100-150       |      25  |
| 10 000   | 225-400       |      250  |
| 100 000  | 2 500-4 000   |      2 500  |

Remarque : par Cosmos DB exigence, un débit minimal de 40 RU/s par Go de stockage est requis. 

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez obtenu des informations sur les fonctionnalités FHIR prises en charge dans l’API Azure pour FHIR. Déployez ensuite l’API Azure pour FHIR.
 
>[!div class="nextstepaction"]
>[Déployer l’API Azure pour FHIR](fhir-paas-portal-quickstart.md)
