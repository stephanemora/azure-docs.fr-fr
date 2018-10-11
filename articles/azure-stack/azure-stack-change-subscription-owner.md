---
title: Mettre à jour le propriétaire de l’abonnement utilisateur Azure Stack | Microsoft Docs
description: Modifiez le propriétaire de facturation pour les abonnements utilisateur Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 06/12/2018
ms.author: sethm
ms.reviewer: shnatara
ms.openlocfilehash: a784f169bfdf23255b27d50f0e135384db6b2b88
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077628"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Modifier le propriétaire d’un abonnement utilisateur Azure Stack

Les opérateurs Azure Stack peuvent utiliser PowerShell pour modifier le propriétaire de facturation d’un abonnement utilisateur. Vous pouvez par exemple modifier le propriétaire pour remplacer un utilisateur qui quitte votre entreprise.   

Il existe deux types de *propriétaires* qui sont affectés à un abonnement :

- **Propriétaire de facturation** : par défaut, le propriétaire de facturation est le compte d’utilisateur qui obtient l’abonnement à partir d’une offre et qui possède donc la relation de facturation pour cet abonnement. Ce compte est également un administrateur de l’abonnement.  Seul un compte d’utilisateur peut avoir cette désignation pour un abonnement. Un propriétaire de facturation est souvent un responsable d’équipe ou d’entreprise. 

  Utilisez l’applet de commande PowerShell **Set-AzsUserSubscription** pour modifier le propriétaire de facturation.  

- **Propriétaires ajoutés via des rôles RBAC** : le rôle de propriétaire peut être affecté à d’autres utilisateurs via le système de [Contrôle d’accès en fonction du rôle](azure-stack-manage-permissions.md) (RBAC).  Vous pouvez ajouter autant de comptes d’utilisateur que vous le souhaitez en tant que propriétaires pour assister le propriétaire de facturation. Les propriétaires supplémentaires deviennent également administrateurs de l’abonnement et ont tous les privilèges pour l’abonnement, sauf l’autorisation nécessaire pour supprimer le propriétaire de facturation. 

  Vous pouvez utiliser PowerShell pour gérer les propriétaires supplémentaires (voir [Azure PowerShell to manage role-based access control]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) (Utiliser Azure PowerShell pour gérer le contrôle d’accès en fonction du rôle)).


## <a name="change-the-billing-owner"></a>Modifier le propriétaire de facturation
Exécutez le script suivant pour modifier le propriétaire de facturation d’un abonnement utilisateur.  L’ordinateur que vous utilisez pour exécuter le script doit se connecter à Azure Stack et exécuter le module Azure Stack PowerShell version 1.3.0 ou ultérieure. Pour plus d’informations, consultez l’article [Installer Azure Stack PowerShell](azure-stack-powershell-install.md). 

> [!Note]  
>  Dans une instance Azure Stack mutualisée, le nouveau propriétaire doit se trouver dans le même répertoire que le propriétaire existant. Pour pouvoir accorder la propriété de l’abonnement à un utilisateur qui se trouve dans un autre répertoire, vous devez d’abord [inviter cet utilisateur en tant qu’invité dans votre répertoire](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator). 


Remplacez les valeurs suivantes dans le script avant de l’exécuter : 
 
- **$ArmEndpoint** : spécifiez le point de terminaison Gestionnaire des ressources pour votre environnement.  
- **$TenantId** : spécifiez votre ID de locataire. 
- **$SubscriptionId** : spécifiez votre ID d’abonnement.
- **$OwnerUpn** : spécifiez un compte comme *user@example.com* à ajouter en tant que nouveau propriétaire de facturation.  


```PowerSHell   
# Setup Azure Stack Admin Environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select Admin Subscriptionr
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context the Default Provider Subscription: $providerSubscriptionId" 
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change User Subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```


## <a name="next-steps"></a>Étapes suivantes
[Gérer le contrôle d’accès en fonction du rôle](azure-stack-manage-permissions.md)
