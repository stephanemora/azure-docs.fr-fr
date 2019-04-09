---
title: Créer des groupes d'administration pour organiser les ressources Azure - Azure Governance
description: Apprenez à créer des groupes d'administration Azure pour gérer différentes ressources à l'aide du portail, d'Azure PowerShell et d'Azure CLI.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 928cb790bd97270870618534a73316bba5eeb070
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057436"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Créer des groupes d’administration pour la gestion et l’organisation des ressources

Les groupes d’administration sont des conteneurs qui vous aident à gérer l’accès, la stratégie et la conformité dans plusieurs abonnements. Créez ces conteneurs pour construire une hiérarchie efficace utilisable avec [Azure Policy](../policy/overview.md) et les [contrôles d’accès en fonction du rôle Azure](../../role-based-access-control/overview.md). Pour plus d’informations sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](overview.md).

La création du premier groupe d’administration dans l’annuaire peut nécessiter jusqu’à 15 minutes. En effet, des processus s’exécutent la première fois pour configurer le service des groupes d’administration dans Azure pour votre annuaire. Vous recevez une notification quand le processus est terminé.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="create-a-management-group"></a>Créer un groupe d’administration

Vous pouvez créer un groupe d’administration en utilisant le portail, PowerShell ou Azure CLI. Actuellement, vous ne pouvez pas utiliser les modèles Resource Manager pour créer des groupes d’administration.

### <a name="create-in-portal"></a>Créer dans le portail

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services** > **Groupes d’administration**.

1. Dans la page principale, sélectionnez **Nouveau groupe d’administration**.

   ![Page pour l’utilisation des groupes d’administration](./media/main.png)

1. Renseignez le champ ID du groupe d’administration.

   - L’**ID du groupe d’administration** est l’identificateur unique de l’annuaire utilisé pour envoyer des commandes sur ce groupe d’administration. Cet identificateur n’est pas modifiable après sa création car il est utilisé dans tout le système Azure pour identifier ce groupe. Le [groupe d’administration racine](index.md#root-management-group-for-each-directory) est automatiquement créé avec un ID qui est l’ID d’Azure Active Directory. Pour tous les autres groupes de gestion, affecter un ID unique.
   - Le champ du nom d’affichage correspond au nom qui s’affiche dans le portail Azure. Un nom d’affichage distinct est un champ facultatif lors de la création du groupe d’administration. Il peut être modifié à tout moment.  

   ![Volet d’options pour la création d’un nouveau groupe d’administration](./media/create_context_menu.png)  

1. Sélectionnez **Enregistrer**.

### <a name="create-in-powershell"></a>Créer dans PowerShell

Dans PowerShell, vous utilisez l’applet de commande New-AzManagementGroup :

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** est un identificateur unique créé. Cet ID est utilisé par d’autres commandes pour référencer ce groupe et il ne peut pas être modifié ultérieurement.

Si vous voulez afficher un autre nom pour le groupe d’administration dans le portail Azure, ajoutez le paramètre **DisplayName** avec la chaîne. Par exemple, pour créer un groupe d’administration dont l’identificateur GroupName est Contoso et le nom d’affichage « Contoso Group », utilisez l’applet de commande suivante :

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group' -ParentId '/providers/Microsoft.Management/managementGroups/ContosoTenant'
```

Utilisez le paramètre **ParentId** pour que ce groupe d’administration soit créé sous un autre groupe d’administration.

### <a name="create-in-azure-cli"></a>Créer dans Azure CLI

Dans Azure CLI, utilisez la commande az account management-group create.

```azurecli-interactive
az account management-group create --name 'Contoso'
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les groupes d’administration, consultez :

- [Créer des groupes d’administration pour organiser les ressources Azure](create.md)
- [Comment modifier, supprimer ou gérer vos groupes d’administration](manage.md)
- [Passez en revue les groupes d’administration dans le Module de ressources Azure PowerShell](/powershell/module/az.resources#resources)
- [Passez en revue les groupes d’administration dans l’API REST](/rest/api/resources/managementgroups)
- [Passez en revue les groupes d’administration dans Azure CLI](/cli/azure/account/management-group)