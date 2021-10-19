---
title: Fonctionnalités de FHIR prises en charge dans Azure - API Azure pour FHIR
description: Cet article détaille les fonctionnalités de la spécification de FHIR qui sont implémentées dans l’API Azure pour FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 6/16/2021
ms.author: cavoeg
ms.openlocfilehash: 5db569349134bd63b0341cc7afb024cfad83b884
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046142"
---
# <a name="features"></a>Fonctionnalités

L’API Azure pour FHIR fournit un déploiement complètement managé du serveur FHIR Microsoft pour Azure. Le serveur est une implémentation de la [FHIR](https://hl7.org/fhir) standard. Ce document répertorie les principales fonctionnalités du serveur FHIR.

## <a name="fhir-version"></a>Version FHIR

Dernière version prise en charge : `4.0.1`

Versions antérieures également prises en charge : `3.0.2`

## <a name="rest-api"></a>API REST

| API    | API Azure pour FHIR | Service FHIR dans les API de santé | Commentaire |
|--------|--------------------|---------------------------------|---------|
| lire   | Oui                | Oui                             |         |
| vread  | Oui                | Oui                             |         |
| update | Oui                | Oui                             |         | 
| update with optimistic locking | Oui       | Oui       |
| update (conditional)           | Oui       | Oui       |
| patch                          | Oui       | Oui       | Prise en charge du [correctif JSON](https://www.hl7.org/fhir/http.html#patch) uniquement. Nous avons inclus une solution de contournement pour utiliser le correctif JSON dans une offre groupée dans [ce PR](https://github.com/microsoft/fhir-server/pull/2143).|
| correctif (conditionnel)            | Oui       | Oui       |
| supprimer                         | Oui       | Oui       | Pour plus d’informations, consultez la section supprimer ci-dessous. |
| delete (conditional)           | Oui       | Oui       | Pour plus d’informations, consultez la section supprimer ci-dessous. |
| history                        | Oui       | Oui       |
| create                         | Oui       | Oui       | Prend en charge POST et PUT |
| create (conditional)           | Oui       | Oui       | Problème [no 1382](https://github.com/microsoft/fhir-server/issues/1382) |
| recherche                         | Partiel   | Partiel   | Consultez [vue d’ensemble de la recherche de FHIR](overview-of-search.md). |
| recherche chaînée                 | Oui       | Oui       | Voir la remarque ci-dessous. |
| recherche chaînée inversée         | Oui       | Oui       | Voir la remarque ci-dessous. |
| lot                          | Oui       | Oui       |
| transaction                    | Non        | Oui       |
| paging                         | Partiel   | Partiel   | `self` et `next` sont pris en charge                     |
| intermediaries                 | Non        | Non        |

> [!Note] 
> dans l’API Azure pour FHIR et le serveur FHIR open source sauvegardé par Cosmos, la recherche chaînée et la recherche chaînée par chaîne est une implémentation MVP. pour effectuer une recherche chaînée sur Cosmos DB, l’implémentation parcourt l’expression de recherche et émet des sous-requêtes pour résoudre les ressources correspondantes. Cette opération est effectuée pour chaque niveau de l’expression. Si une requête retourne plus de 1000 résultats, une erreur est générée.

### <a name="delete-and-conditional-delete"></a>Delete et suppression conditionnelle

La suppression définie par la spécification FHIR nécessite qu’après la suppression, les lectures suivantes non spécifiques à la version d’une ressource retournent un code d’état HTTP 410 et que la ressource ne soit plus trouvée via la recherche. L’API Azure pour FHIR et le service FHIR vous permettent également de supprimer entièrement (y compris l’historique) la ressource. Pour supprimer entièrement la ressource, vous pouvez passer une valeur de paramètre `hardDelete` définie sur true (`DELETE {server}/{resource}/{id}?hardDelete=true`). Si vous ne transmettez pas ce paramètre ou que vous lui donnez la valeur false, les versions historiques de la ressource restent disponibles.

En plus de supprimer, l’API Azure pour FHIR et le service FHIR prennent en charge la suppression conditionnelle, qui vous permet de passer un critère de recherche pour supprimer une ressource. Par défaut, la suppression conditionnelle vous permet de supprimer un élément à la fois. Vous pouvez également spécifier le `_count` paramètre pour supprimer jusqu’à 100 éléments à la fois. Voici quelques exemples d’utilisation de la suppression conditionnelle.

Pour supprimer un seul élément à l’aide de la suppression conditionnelle, vous devez spécifier des critères de recherche qui renvoient un seul élément.
``` JSON
DELETE https://{{hostname}}/Patient?identifier=1032704
```

Vous pouvez effectuer la même recherche, mais inclure hardDelete = true pour supprimer également tout l’historique.
```JSON 
DELETE https://{{hostname}}/Patient?identifier=1032704&hardDelete=true
```

Si vous souhaitez supprimer plusieurs ressources, vous pouvez inclure `_count=100` , qui supprimera jusqu’à 100 ressources correspondant aux critères de recherche. 
``` JSON
DELETE https://{{hostname}}/Patient?identifier=1032704&_count=100
```

## <a name="extended-operations"></a>Opérations étendues

Toutes les opérations prises en charge qui étendent l’API REST.

| Type de paramètre de recherche | API Azure pour FHIR | Service FHIR dans les API de santé| Commentaire |
|------------------------|-----------|-----------|---------|
| $export (système entier) | Oui       | Oui       |         |
| Patient/$export        | Oui       | Oui       |         |
| Group/$export          | Oui       | Oui       |         |
| $convert-data          | Oui       | Oui       |         |
| $validate              | Oui       | Oui       |         |
| $member-correspondance          | Oui       | Oui       |         |
| $patient-tout    | Oui       | Oui       |         |
| historique des $purge         | Oui       | Oui       |         |

## <a name="persistence"></a>Persistance

Le serveur FHIR Microsoft dispose d’un module de persistance enfichable (voir [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence)).

Le code open source de FHIR Server inclut une implémentation pour [Azure Cosmos DB](../../cosmos-db/index-overview.md) et [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB est une base de données multimodèle (SQL API, MongoDB API, etc.) distribuée à l’échelle mondiale. Il prend en charge différents [niveaux de cohérence](../../cosmos-db/consistency-levels.md). Le modèle de déploiement par défaut configure le serveur FHIR avec une cohérence `Strong`, mais la stratégie de cohérence peut être modifiée (généralement assouplie) en envoyant une demande individuelle à l’aide de l’en-tête de demande `x-ms-consistency-level`.

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Le serveur FHIR utilise [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) pour contrôler l’accès. Plus précisément, le contrôle d’accès en fonction du rôle (RBAC) est appliqué, si le paramètre de configuration `FhirServer:Security:Enabled` a la valeur `true`. De plus, l’en-tête de requête `Authorization` de toutes les requêtes (à l’exception de `/metadata`) envoyées au serveur FHIR Server doit avoir la valeur `Bearer <TOKEN>`. Le jeton doit contenir au moins un rôle tel que défini dans la revendication `roles`. Une demande sera autorisée si le jeton contient un rôle qui permet l’application de l’action spécifiée sur la ressource spécifiée.

Actuellement, les actions autorisées pour un rôle donné sont appliquées *à l’ensemble* de l’API.

## <a name="service-limits"></a>Limites du service

* [**Unités de requête (RU)**](../../cosmos-db/concepts-limits.md) : vous pouvez configurer jusqu’à 10 000 unités de requête dans le portail pour l’API Azure pour FHIR. Vous aurez besoin d’un minimum de 400 ou de 40 unités de capacité/Go, selon la valeur la plus grande. Si vous avez besoin de plus de 10 000 unités de requête, vous pouvez mettre en place un ticket de support pour que le RUs soit augmenté. Le maximum disponible est 1 000 000.

* **Taille de bundle** : chaque bundle est limité à 500 éléments.

* **Taille des données** : les éléments Données/Documents doivent chacun être un peu inférieurs à 2 Mo.

* **Limite d’abonnement** : par défaut, chaque abonnement est limité à un maximum de 10 instances de serveur FHIR. Si vous avez besoin d’un plus grand nombre d’instances par abonnement, ouvrez un ticket de support et fournissez des informations détaillées sur vos besoins.

* **Connexions et instances simultanées** : par défaut, vous disposez de 15 connexions simultanées sur deux instances du cluster (pour un total de 30 demandes simultanées). Si vous avez besoin de plus de demandes simultanées, ouvrez un ticket de support et fournissez des informations détaillées sur vos besoins.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez obtenu des informations sur les fonctionnalités FHIR prises en charge dans l’API Azure pour FHIR. Déployez ensuite l’API Azure pour FHIR.
 
>[!div class="nextstepaction"]
>[Déployer l’API Azure pour FHIR](fhir-paas-portal-quickstart.md)
