---
title: Lister les attributions de rôles à l’aide du RBAC Azure et de l’API REST
description: Découvrez comment déterminer les ressources, utilisateurs, groupes, principaux de service ou identités managées qui ont accès au contrôle d’accès en fonction du rôle (RBAC) Azure et à l’API REST.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e20edcb5e2406c216711a2e0f696ef06e19fe21e
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74709881"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Lister les attributions de rôles à l’aide du RBAC Azure et de l’API REST

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] Cet article explique comment lister les attributions de rôles à l’aide de l’API REST.

## <a name="list-role-assignments"></a>Répertorier les attributions de rôles

Dans le contrôle d’accès en fonction du rôle, vous répertoriez les attributions de rôles pour énumérer les accès. Pour répertorier les attributions de rôles, utilisez l’une des API REST de la [liste d’attributions de rôles](/rest/api/authorization/roleassignments/list). Pour affiner vos résultats, vous spécifiez une étendue et un filtre facultatif.

1. Commencez par la requête suivante :

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Dans l’URI, remplacez *{scope}* par l’étendue dont vous souhaitez lister les attributions de rôle.

    | Étendue | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Groupe d’administration |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ressource |

    Dans l’exemple précédent, microsoft.web est un fournisseur de ressources qui fait référence à une instance App Service. De la même façon, vous pouvez utiliser tout autre fournisseur de ressources et spécifier l’étendue. Pour plus d’informations, consultez [Fournisseurs et types de ressources Azure](../azure-resource-manager/resource-manager-supported-services.md) et [Opérations du fournisseur de ressources Azure Resource Manager](resource-provider-operations.md) prises en charge.  
     
1. Remplacez *{filter}* par la condition que vous voulez appliquer pour filtrer la liste des attributions de rôle.

    | Filtrer | Description |
    | --- | --- |
    | `$filter=atScope()` | Répertorie les attributions de rôles pour l’étendue spécifiée seulement, sans y inclure les attributions de rôles à des étendues secondaires. |
    | `$filter=principalId%20eq%20'{objectId}'` | Répertorie les attributions de rôles pour un utilisateur, un groupe ou un principal de service spécifié. |
    | `$filter=assignedTo('{objectId}')` | Répertorie les attributions de rôles pour un utilisateur ou un principal de service spécifié. Si l’utilisateur est membre d’un groupe auquel un rôle a été attribué, cette attribution de rôle est également répertoriée. Ce filtre est transitif pour les groupes, ce qui signifie que si l’utilisateur est membre d’un groupe et que ce groupe est membre d’un autre groupe auquel un rôle a été attribué, cette attribution de rôle est également répertoriée. Ce filtre accepte uniquement un ID d’objet pour un utilisateur ou principal de service. Vous ne pouvez pas transmettre un ID d’objet pour un groupe. |

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter ou supprimer des attributions de rôles à l’aide du RBAC Azure et de l’API REST](role-assignments-rest.md)
- [Référence de l'API REST Azure](/rest/api/azure/)
