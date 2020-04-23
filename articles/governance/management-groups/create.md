---
title: Créer des groupes d'administration pour organiser les ressources - Azure Governance
description: Apprenez à créer des groupes d'administration Azure pour gérer différentes ressources à l'aide du portail, d'Azure PowerShell et d'Azure CLI.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: 34815089367512c4aa54f148c118a669625d0ea3
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381584"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Créer des groupes d’administration pour la gestion et l’organisation des ressources

Les groupes d’administration sont des conteneurs qui vous aident à gérer l’accès, la stratégie et la conformité dans plusieurs abonnements. Créez ces conteneurs pour construire une hiérarchie efficace utilisable avec [Azure Policy](../policy/overview.md) et les [contrôles d’accès en fonction du rôle Azure](../../role-based-access-control/overview.md). Pour plus d’informations sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](overview.md).

La création du premier groupe d’administration dans l’annuaire peut nécessiter jusqu’à 15 minutes. En effet, des processus s’exécutent la première fois pour configurer le service des groupes d’administration dans Azure pour votre annuaire. Vous recevez une notification quand le processus est terminé. Pour plus d’informations, consultez la [configuration initiale des groupes d’administration](./overview.md#initial-setup-of-management-groups).

## <a name="create-a-management-group"></a>Créer un groupe d’administration

Tout utilisateur Azure AD du locataire peut créer un groupe d’administration sans l’autorisation d’écriture du groupe d’administration attribuée à cet utilisateur. Ce nouveau groupe d’administration sera un enfant du groupe d’administration racine et le rôle de « Propriétaire » sera attribué au créateur. Le service de groupe d’administration offre cette possibilité afin que les attributions de rôles ne soient pas nécessaires au niveau de la racine. Aucun utilisateur n’a accès au groupe d’administration racine lors de sa création. Pour éviter l’obstacle que constitue la recherche des administrateurs généraux Azure AD pour commencer à utiliser des groupes d’administration, nous autorisons la création des groupes d’administration initiaux au niveau de la  
racine.

Vous pouvez créer le groupe d’administration en utilisant le portail, un [modèle Resource Manager](../../azure-resource-manager/templates/deploy-to-tenant.md#create-management-group), PowerShell ou Azure CLI.

### <a name="create-in-portal"></a>Créer dans le portail

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services** > **Gestion + gouvernance**.

1. Sélectionnez **Gestion des coûts + facturation**.

1. Dans la page Gestion des coûts + facturation - Groupes d’administration, sélectionnez **Groupes d’administration**.

1. Sélectionnez **+ Ajouter un groupe d’administration**.

   :::image type="content" source="./media/main.png" alt-text="Page dédiée à l’utilisation des groupes d’administration" border="false":::

1. Renseignez le champ ID du groupe d’administration.

   - L’**ID du groupe d’administration** est l’identificateur unique de l’annuaire utilisé pour envoyer des commandes sur ce groupe d’administration. Cet identificateur n’est pas modifiable après sa création car il est utilisé dans tout le système Azure pour identifier ce groupe. Le [groupe d’administration racine](overview.md#root-management-group-for-each-directory) est automatiquement créé avec un ID qui correspond à l’ID Azure Active Directory. Pour tous les autres groupes d’administration, affectez un ID unique.
   - Le champ du nom d’affichage correspond au nom qui s’affiche dans le portail Azure. Un nom d’affichage distinct est un champ facultatif lors de la création du groupe d’administration. Il peut être modifié à tout  
     moment.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Volet Options pour la création d’un groupe d’administration" border="false":::

1. Sélectionnez **Enregistrer**.

### <a name="create-in-powershell"></a>Créer dans PowerShell

Pour PowerShell, utilisez le cmdlet [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) afin de créer un groupe d’administration.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** est un identificateur unique créé. Cet ID est utilisé par d’autres commandes pour faire référence à ce groupe et il ne peut pas être modifié ultérieurement.

Si vous voulez afficher un autre nom pour le groupe d’administration dans le portail Azure, ajoutez le paramètre **DisplayName**. Par exemple, pour créer un groupe d’administration dont l’identificateur GroupName est Contoso et le nom d’affichage « Contoso Group », utilisez le cmdlet suivant :

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

Dans les exemples précédents, le groupe d’administration est créé sous le groupe d’administration racine. Pour spécifier un autre groupe d’administration en tant que parent, utilisez le paramètre **ParentId**.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Créer dans Azure CLI

Dans Azure CLI, utilisez la commande [az account management-group create](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) pour créer un groupe d’administration.

```azurecli-interactive
az account management-group create --name Contoso
```

**name** est un identificateur unique créé. Cet ID est utilisé par d’autres commandes pour faire référence à ce groupe et il ne peut pas être modifié ultérieurement.

Si vous voulez afficher un autre nom pour le groupe d’administration dans le Portail Azure, ajoutez le paramètre **display-name**. Par exemple, pour créer un groupe d’administration dont l’identificateur GroupName est Contoso et le nom d’affichage « Contoso Group », utilisez la commande suivante :

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

Dans les exemples précédents, le groupe d’administration est créé sous le groupe d’administration racine. Pour spécifier un autre groupe d’administration en tant que parent, utilisez le paramètre **parent** et fournissez le nom du groupe parent.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les groupes d’administration, consultez :

- [Créer des groupes d’administration pour organiser les ressources Azure](./create.md)
- [Guide pratique pour modifier, supprimer ou gérer vos groupes d’administration](./manage.md)
- [Consulter les groupes d’administration dans le module Azure PowerShell Resources](/powershell/module/az.resources#resources)
- [Consulter les groupes d’administration dans l’API REST](/rest/api/resources/managementgroups)
- [Consulter les groupes d’administration dans Azure CLI](/cli/azure/account/management-group)
