---
title: Fonctionnalités de FHIR prises en charge dans Azure - API Azure pour FHIR
description: Cet article détaille les fonctionnalités de la spécification de FHIR qui sont implémentées dans l’API Azure pour FHIR
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 74a17c4a433ebe1c1107230739086375fe165ed9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67033773"
---
# <a name="features"></a>Caractéristiques

L’API Azure pour FHIR fournit un déploiement complètement managé du serveur FHIR Microsoft pour Azure. Le serveur est une implémentation de la [FHIR](https://hl7.org/fhir) standard. Ce document répertorie les principales fonctionnalités du serveur FHIR.

## <a name="fhir-version"></a>Version FHIR

La dernière version prise en charge (disponible uniquement sur l’implémentation OSS) : `4.0.0`

Les versions antérieures suivantes sont également prises en charge (disponible sur les implémentations PaaS et OSS) : `3.0.1`

## <a name="rest-api"></a>API REST

| API                            | Pris en charge | Commentaire |
|--------------------------------|-----------|---------|
| read                           | OUI       |         |
| vread                          | OUI       |         |
| update                         | OUI       |         |
| update with optimistic locking | OUI       |         |
| update (conditional)           | Non        |         |
| patch                          | Non        |         |
| delete                         | OUI       |         |
| delete (conditional)           | Non        |         |
| create                         | OUI       | Prend en charge POST et PUT |
| create (conditional)           | Non        |         |
| recherche                         | Partiel   | Voir ci-dessous |
| capabilities                   | OUI       |         |
| lot                          | Non        |         |
| transaction                    | Non        |         |
| history                        | OUI       |         |
| paging                         | Partiel   | `self` et `next` sont pris en charge |
| intermediaries                 | Non        |         |

## <a name="search"></a>Recherche

Tous les types de paramètre de recherche sont pris en charge. Les paramètres en chaîne et le chaînage inverse ne sont *pas* pris en charge.

| Type de paramètre de recherche | Pris en charge | Commentaire |
|-----------------------|-----------|---------|
| Number                | OUI       |         |
| Date/DateHeure         | OUI       |         |
| Chaîne                | OUI       |         |
| par jeton                 | OUI       |         |
| Informations de référence             | OUI       |         |
| Composite             | OUI       |         |
| Quantité              | OUI       | Problème [n° 103](https://github.com/Microsoft/fhir-server/issues/103) |
| URI                   | OUI       |         |


| Modificateurs             | Pris en charge | Commentaire |
|-----------------------|-----------|---------|
|`:missing`             | OUI       |         |
|`:exact`               | OUI       |         |
|`:contains`            | OUI       |         |
|`:text`                | OUI       |         |
|`:in` (jeton)          | Non        |         |
|`:below` (jeton)       | Non        |         |
|`:above` (jeton)       | Non        |         |
|`:not-in` (jeton)      | Non        |         |
|`:[type]` (référence)  | Non        |         |
|`:below` (URI)         | OUI       |         |
|`:above` (URI)         | Non        | Problème [n° 158](https://github.com/Microsoft/fhir-server/issues/158) |

| Paramètre de recherche courant | Pris en charge | Commentaire |
|-------------------------| ----------|---------|
| `_id`                   | OUI       |         |
| `_lastUpdated`          | OUI       |         |
| `_tag`                  | OUI       |         |
| `_profile`              | OUI       |         |
| `_security`             | OUI       |         |
| `_text`                 | Non        |         |
| `_content`              | Non        |         |
| `_list`                 | Non        |         |
| `_has`                  | Non        |         |
| `_type`                 | OUI       |         |
| `_query`                | Non        |         |

| Opérations de recherche       | Pris en charge | Commentaire |
|-------------------------|-----------|---------|
| `_filter`               | Non        |         |
| `_sort`                 | Non        |         |
| `_score`                | Non        |         |
| `_count`                | OUI       |         |
| `_summary`              | Partiel   | `_summary=count` est pris en charge |
| `_include`              | Non        |         |
| `_revinclude`           | Non        |         |
| `_contained`            | Non        |         |
| `_elements`             | Non        |         |

## <a name="persistence"></a>Persistance

Le serveur FHIR Microsoft dispose d’un module de persistance enfichable (voir [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence)).

Le code open source du serveur FHIR comprend actuellement une implémentation pour [Azure Cosmos DB](../cosmos-db/index-overview.md).

Cosmos DB est une base de données multimodèle (SQL API, MongoDB API, etc.) distribuée à l’échelle mondiale. Il prend en charge différents [niveaux de cohérence](../cosmos-db/consistency-levels.md). Le modèle de déploiement par défaut configure le serveur FHIR avec une cohérence `Strong`, mais la stratégie de cohérence peut être modifiée (généralement assouplie) en envoyant une demande individuelle à l’aide de l’en-tête de demande `x-ms-consistency-level`.

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Le serveur FHIR utilise [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) pour contrôler l’accès. Plus précisément, le contrôle RBAC (contrôle d’accès en fonction du rôle) est appliqué, si le paramètre de configuration `FhirServer:Security:Enabled` est défini sur `true`. De plus, l’en-tête de demande `Authorization` de toutes les demandes (à l’exception de `/metadata`) envoyées au serveur FHIR doit être défini sur `Bearer <TOKEN>`. Le jeton doit contenir au moins un rôle tel que défini dans la revendication `roles`. Une demande sera autorisée si le jeton contient un rôle qui permet l’application de l’action spécifiée sur la ressource spécifiée.

Actuellement, les actions autorisées pour un rôle donné sont appliquées *à l’ensemble* de l’API.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez obtenu des informations sur les fonctionnalités FHIR prises en charge dans l’API Azure pour FHIR. Ensuite, déployez une API FHIR dans Azure.
 
>[!div class="nextstepaction"]
>[Déployer le serveur FHIR open source](fhir-oss-powershell-quickstart.md)