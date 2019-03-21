---
title: Répertorier les affectations de refus relatives aux ressources Azure à l'aide de l'API REST - Azure | Microsoft Docs
description: Apprenez à dresser la liste des affectations de refus pour les utilisateurs, groupes et applications à l'aide de l'API REST et du contrôle d'accès en fonction du rôle (RBAC) pour les ressources Azure.
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
ms.date: 03/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 59bcf2b33d203ae216b4965b963a727a6b34ae72
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998409"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Répertorier les affectations de refus relatives aux ressources Azure à l'aide de l'API REST

Les [affectations de refus](deny-assignments.md) empêchent les utilisateurs d'effectuer des actions particulières sur les ressources Azure, même si une attribution de rôle leur confère un accès. Cet article décrit comment utiliser l’API REST pour la liste des affectations de refuser.

> [!NOTE]
> À ce stade, la seule façon, vous pouvez ajouter vos propres refuser affectations est à l’aide de plans d’Azure. Pour plus d’informations, consultez [protéger les nouvelles ressources des verrous de ressources Azure plans](../governance/blueprints/tutorials/protect-new-resources.md).

## <a name="prerequisites"></a>Conditions préalables

Pour obtenir des informations sur une affectation de refus, vous devez disposer de :

- `Microsoft.Authorization/denyAssignments/read` autorisation, qui est incluse dans la plupart des [rôles intégrés pour les ressources Azure](built-in-roles.md).

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

    | Étendue | Type |
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

- [Comprendre les affectations de refus relatives aux ressources Azure](deny-assignments.md)
- [Élever l’accès d’un administrateur général dans Azure Active Directory](elevate-access-global-admin.md)
- [Référence de l'API REST Azure](/rest/api/azure/)
