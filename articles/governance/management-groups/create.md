---
title: Créer des groupes d'administration pour organiser les ressources Azure - Azure Governance
description: Apprenez à créer des groupes d'administration Azure pour gérer différentes ressources à l'aide du portail, d'Azure PowerShell et d'Azure CLI.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 2dd2a6e071533deef47a6482bfb9ed92953864ba
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59259800"
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

Pour PowerShell, utilisez le [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) applet de commande pour créer un nouveau groupe d’administration.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** est un identificateur unique créé. Cet ID est utilisé par d’autres commandes pour référencer ce groupe et il ne peut pas être modifié ultérieurement.

Si vous souhaitez que le groupe d’administration pour afficher un nom différent au sein du portail Azure, ajoutez le **DisplayName** paramètre. Par exemple, pour créer un groupe d’administration avec l’identificateur GroupName est Contoso et le nom complet de « Groupe Contoso », utilisez l’applet de commande suivante :

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

Dans les exemples précédents, le nouveau groupe d’administration est créé sous le groupe d’administration racine. Pour spécifier un autre groupe d’administration en tant que parent, utilisez la **ParentId** paramètre.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Créer dans Azure CLI

Pour Azure CLI, utilisez le [créer az compte du groupe d’administration](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) commande pour créer un nouveau groupe d’administration.

```azurecli-interactive
az account management-group create --name Contoso
```

Le **nom** est un identificateur unique en cours de création. Cet ID est utilisé par d’autres commandes pour référencer ce groupe et il ne peut pas être modifié ultérieurement.

Si vous souhaitez que le groupe d’administration pour afficher un nom différent au sein du portail Azure, ajoutez le **nom d’affichage** paramètre. Par exemple, pour créer un groupe d’administration avec l’identificateur GroupName est Contoso et le nom complet de « Groupe Contoso », utilisez la commande suivante :

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

Dans les exemples précédents, le nouveau groupe d’administration est créé sous le groupe d’administration racine. Pour spécifier un autre groupe d’administration en tant que parent, utilisez la **parent** paramètre et indiquez le nom du groupe parent.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les groupes d’administration, consultez :

- [Créer des groupes d’administration pour organiser les ressources Azure](create.md)
- [Guide pratique pour modifier, supprimer ou gérer vos groupes d’administration](manage.md)
- [Consulter les groupes d’administration dans le module Azure PowerShell Resources](/powershell/module/az.resources#resources)
- [Consulter les groupes d’administration dans l’API REST](/rest/api/resources/managementgroups)
- [Consulter les groupes d’administration dans Azure CLI](/cli/azure/account/management-group)