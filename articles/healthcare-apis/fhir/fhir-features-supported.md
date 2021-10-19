---
title: Fonctionnalités FHIR prises en charge dans le service FHIR
description: Cet article explique les fonctionnalités de la spécification FHIR qui sont implémentées dans les API de santé.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.openlocfilehash: c1f40d6129f08b8369885631232b394d66f1d260
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045840"
---
# <a name="supported-fhir-features"></a>Fonctionnalités FHIR prises en charge

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Le &reg; service FHIR dans les API de santé Azure (par défaut le service FHIR) fournit un déploiement entièrement managé du [serveur FHIR Open source](https://github.com/microsoft/fhir-server) et est une implémentation de la norme [FHIR](https://hl7.org/fhir) . Ce document répertorie les principales fonctionnalités du service FHIR.

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
| recherche chaînée                 | Oui       | Oui       | |
| recherche chaînée inversée         | Oui       | Oui       | |
| lot                          | Oui       | Oui       |
| transaction                    | Non        | Oui       |
| paging                         | Partiel   | Partiel   | `self` et `next` sont pris en charge                     |
| intermediaries                 | Non        | Non        |

### <a name="delete-and-conditional-delete"></a>Delete et suppression conditionnelle

Après la suppression, telle qu’elle est définie par la spécification FHIR, les lectures suivantes d’une ressource qui ne sont pas propres à la version renvoient un code de statut HTTP 410, et la ressource ne figure plus dans les résultats de recherche. L’API Azure pour FHIR et le service FHIR vous permettent également de supprimer entièrement (y compris l’historique) la ressource. Pour supprimer entièrement la ressource, vous pouvez passer une valeur de paramètre `hardDelete` définie sur true (`DELETE {server}/{resource}/{id}?hardDelete=true`). Si vous ne transmettez pas ce paramètre ou que vous lui donnez la valeur false, les versions historiques de la ressource restent disponibles.

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

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

le service FHIR utilise [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) pour le contrôle d’accès. 

## <a name="service-limits"></a>Limites du service

* **Taille de bundle** : chaque bundle est limité à 500 éléments.

* **Limite d’abonnement** : par défaut, chaque abonnement est limité à un maximum de 10 services FHIR. La limite peut être utilisée dans un ou plusieurs espaces de travail. 

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez lu les fonctionnalités FHIR prises en charge dans le service FHIR. Ensuite, déployez le service FHIR.
 
>[!div class="nextstepaction"]
>[Déployer le service FHIR](fhir-portal-quickstart.md)
