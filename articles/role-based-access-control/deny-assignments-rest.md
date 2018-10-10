---
title: Lister les affectations de refus à l’aide de RBAC et de l’API REST - Azure | Microsoft Docs
description: Découvrez comment lister les affectations de refus pour les utilisateurs, groupes et applications à l’aide du contrôle d’accès en fonction du rôle (RBAC) et de l’API REST.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 44c1d3b18bb9bdc63247379fe3f277cb6542f2da
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975580"
---
# <a name="list-deny-assignments-using-rbac-and-the-rest-api"></a>Lister les affectations de refus à l’aide de RBAC et de l’API REST

Actuellement, les affectations de refus sont **en lecture seule** et peuvent être définies uniquement par Azure. Même si vous ne pouvez pas créer vos propres affectations de refus, il est utile de lister celles qui sont définies, car elles peuvent impacter vos autorisations actuelles. Cet article décrit comment lister les affectations de refus existantes à l’aide de RBAC et de l’API REST.

## <a name="list-a-single-deny-assignment"></a>Lister une seule affectation de refus

1. Commencez par la requête suivante :

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. Dans l’URI, remplacez *{scope}* par l’étendue dont vous souhaitez lister les affectations de refus.

    | Étendue | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Groupe de ressources |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ressource |

1. Remplacez *{deny-assignment-id}* par l’identificateur de l’affectation de refus à récupérer.

## <a name="list-multiple-deny-assignments"></a>Lister plusieurs affectations de refus

1. Commencez par l’une des requêtes suivantes :

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Avec des paramètres facultatifs :

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Dans l’URI, remplacez *{scope}* par l’étendue dont vous souhaitez lister les affectations de refus.

    | Étendue | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Groupe de ressources |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ressource |

1. Remplacez *{filter}* par la condition que vous voulez appliquer pour filtrer la liste des affectations de refus.

    | Filtrer | Description |
    | --- | --- |
    | (aucun filtre) | Liste toutes les affectations de refus dans l’étendue spécifiée, mais aussi dans les étendues au-dessus et en dessous. |
    | `$filter=atScope()` | Liste les affectations de refus uniquement dans la portée spécifiée et dans les étendues au-dessus. N’inclut pas les affectations de refus dans les étendues en dessous. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Liste les affectations de refus avec le nom spécifié. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Lister les affectations de refus dans l’étendue racine (/)

1. Élevez votre accès comme décrit dans [Élever l’accès d’un administrateur général dans Azure Active Directory](elevate-access-global-admin.md).

1. Utilisez la requête suivante :

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Remplacez *{filter}* par la condition que vous voulez appliquer pour filtrer la liste des affectations de refus. Un filtre est obligatoire.

    | Filtrer | Description |
    | --- | --- |
    | `$filter=atScope()` | Liste les affectations de refus dans l’étendue racine uniquement. N’inclut pas les affectations de refus dans les étendues en dessous. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Liste les affectations de refus avec le nom spécifié. |

1. Supprimez l’accès avec élévation des privilèges.

## <a name="next-steps"></a>Étapes suivantes

- [Comprendre les affectations de refus](deny-assignments.md)
- [Élever l’accès d’un administrateur général dans Azure Active Directory](elevate-access-global-admin.md)
- [Référence de l'API REST Azure](/rest/api/azure/)
