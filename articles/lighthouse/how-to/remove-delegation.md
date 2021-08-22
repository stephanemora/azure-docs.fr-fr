---
title: Supprimer l’accès à une délégation
description: Découvrez comment supprimer l’accès aux ressources qui ont été déléguées à un fournisseur de services pour Azure Lighthouse.
ms.date: 05/11/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bae8407e09ab07a13c12abe3ee6371c04155cced
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114389839"
---
# <a name="remove-access-to-a-delegation"></a>Supprimer l’accès à une délégation

Une fois que le groupe de ressources ou l’abonnement d’un client a été délégué à un fournisseur de services pour [Azure Lighthouse](../overview.md), la délégation peut être supprimée si nécessaire. Une fois qu’une délégation est supprimée, l’accès à [la gestion des ressources déléguées Azure Lighthouse](../concepts/architecture.md) précédemment accordé aux utilisateurs du locataire du fournisseur de services ne s’appliquera plus.

La suppression d’une délégation peut être effectuée par un utilisateur dans le locataire du client ou dans le locataire du fournisseur de services, tant que l’utilisateur dispose des autorisations appropriées.

> [!TIP]
> Même si nous faisons référence aux fournisseurs de services et aux clients dans cette rubrique, les [entreprises gérant plusieurs locataires](../concepts/enterprise.md) peuvent utiliser les mêmes processus.

## <a name="customers"></a>Clients

Les utilisateurs du locataire du client qui disposent d’un rôle ayant l’autorisation `Microsoft.Authorization/roleAssignments/write`, comme [Propriétaire](../../role-based-access-control/built-in-roles.md#owner), peuvent supprimer l’accès du fournisseur de services à cet abonnement (ou aux groupes de ressources de cet abonnement). Pour ce faire, les utilisateurs peuvent accéder à la [page Fournisseurs de services](view-manage-service-providers.md#remove-service-provider-offers) du portail Azure, rechercher l’offre sur l’écran **Offres du fournisseur de services**, puis sélectionner l’icône de la corbeille sur la ligne de cette offre.

Une fois la suppression confirmée, aucun utilisateur du locataire du fournisseur de services ne pourra accéder aux ressources précédemment déléguées.

## <a name="service-providers"></a>Fournisseurs de services

Les utilisateurs d’un locataire gérant peuvent supprimer l’accès aux ressources déléguées s’ils ont reçu le [rôle Supprimer l’attribution de l’inscription des services gérés](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) pour les ressources du client. Si ce rôle n’a été attribué à aucun utilisateur du fournisseur de services, la délégation peut être supprimée uniquement par un utilisateur du locataire du client.

L’exemple ci-dessous montre une affectation octroyant le **rôle Supprimer l’attribution de l’inscription des services gérés** pouvant être inclus dans un fichier de paramètres pendant le [processus d’intégration](onboard-customer.md) :

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Ce rôle peut également être sélectionné dans une **autorisation** lors de la [création d’une offre de service géré](../../marketplace/plan-managed-service-offer.md) à publier sur la Place de marché Azure.

Un utilisateur disposant de cette autorisation peut supprimer une délégation de l’une des manières suivantes.

### <a name="azure-portal"></a>Portail Azure

1. Accédez à la [page Mes clients](view-manage-customers.md).
2. Sélectionnez **Délégations**.
3. Recherchez la délégation que vous souhaitez supprimer, puis sélectionnez l’icône de corbeille qui apparaît dans sa ligne.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez l’[architecture d’Azure Lighthouse](../concepts/architecture.md).
- [Affichez et gérez les clients](view-manage-customers.md) en accédant à **Mes clients** sur le portail Azure.
- Apprenez à [mettre à jour une délégation précédente](update-delegation.md).
