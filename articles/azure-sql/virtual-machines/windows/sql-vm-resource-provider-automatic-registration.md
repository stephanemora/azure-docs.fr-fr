---
title: Inscription automatique avec le fournisseur de ressources de machine virtuelle SQL
description: Découvrez comment activer la fonctionnalité d’inscription automatique pour inscrire automatiquement toutes les machines virtuelles SQL Server passées et futures auprès du fournisseur de ressources de machine virtuelle SQL à l’aide du Portail Azure.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.openlocfilehash: d4a2d9e43dadc53008c04b44ea1dda9cb337da99
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308353"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>Inscription automatique avec le fournisseur de ressources de machine virtuelle SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Activez la fonctionnalité d’inscription automatique dans le portail Azure pour inscrire automatiquement tous les serveurs SQL actuels et futurs sur les machines virtuelles Azure avec le fournisseur de ressources de machine virtuelle SQL en mode allégé. L’inscription auprès du fournisseur de ressources de machine virtuelle SQL installe l’extension [SQL IaaS Agent](sql-server-iaas-agent-extension-automate-management.md).

Cet article vous apprend à activer la fonctionnalité d’inscription automatique. Vous pouvez également [inscrire une seule machine virtuelle](sql-vm-resource-provider-register.md) ou [inscrire vos machines virtuelles en bloc](sql-vm-resource-provider-bulk-register.md) à l’aide du fournisseur de ressources de machine virtuelle SQL. 

## <a name="overview"></a>Vue d’ensemble

Le fait d’inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources de machine virtuelle SQL installe l’[extension SQL IaaS Agent](sql-server-iaas-agent-extension-automate-management.md). 

Lorsque l’inscription automatique est activée, un travail est exécuté quotidiennement pour détecter si SQL Server est installé ou non sur toutes les machines virtuelles non inscrites de l’abonnement. Pour ce faire, copiez les fichiers binaires de l’extension SQL IaaS Agent sur la machine virtuelle, puis exécutez un utilitaire ponctuel qui recherche la ruche du registre SQL Server. Si la ruche SQL Server est détectée, la machine virtuelle est inscrite auprès du [fournisseur de ressources de machine virtuelle SQL](sql-vm-resource-provider-register.md) en mode allégé. Si aucune ruche SQL Server n’existe dans le registre, les fichiers binaires sont supprimés.

Une fois l’inscription automatique activée pour un abonnement, toutes les machines virtuelles actuelles et futures sur lesquelles SQL Server est installé sont inscrites auprès du fournisseur de ressources de machine virtuelle SQL en mode allégé. Vous devez toujours [mettre à niveau manuellement vers le mode de gestion complet](sql-vm-resource-provider-register.md#upgrade-to-full) pour tirer parti de l’ensemble complet de fonctionnalités. 

> [!IMPORTANT]
> L’extension SQL IaaS Agent collecte des données dans le seul but de fournir d’autres avantages aux clients lors de l’utilisation de SQL Server dans Machines virtuelles Azure. Microsoft n’utilisera pas ces données pour les audits de gestion des licences sans le consentement préalable du client. Pour plus d’informations, consultez l’[Avenant à la déclaration de confidentialité de SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).

## <a name="prerequisites"></a>Prérequis

Pour inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources, voici ce dont vous avez besoin : 

- Un [abonnement Azure](https://azure.microsoft.com/free/) et, au minimum, les autorisations du [rôle Contributeur](../../../role-based-access-control/built-in-roles.md#all).
- Une [machine virtuelle Windows Server 2008 R2 (ou ultérieure)](../../../virtual-machines/windows/quick-create-portal.md) de modèle de ressource Azure avec [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) déployé sur le cloud public ou sur le cloud Azure Government. Windows Server 2008 n’est pas pris en charge. 


## <a name="enable"></a>Activer

Pour activer l’inscription automatique de vos machines virtuelles SQL Server dans le portail Azure, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez à la page de ressource [**machines virtuelles SQL**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines). 
1. Sélectionnez **l’inscription automatique de la machine virtuelle SQL Server** pour ouvrir la page **Inscription automatique**. 

   Sélectionner :::image type="content" source="media/sql-vm-resource-provider-automatic-registration/automatic-registration.png" alt-text="l’inscription automatique de la machine virtuelle SQL Server pour ouvrir la page Inscription automatique":::

1. Choisissez votre abonnement dans le menu déroulant. 
1. Lisez les conditions générales et, si vous acceptez, sélectionnez **J’accepte**. 
1. Sélectionnez **Inscrire** pour activer la fonctionnalité et inscrire automatiquement toutes les machines virtuelles SQL Server actuelles et futures auprès du fournisseur de ressources de machine virtuelle SQL. Cela ne redémarre pas le service SQL Server sur les machines virtuelles. 

## <a name="disable"></a>Désactiver

Pour désactiver la fonctionnalité d’inscription automatique, utilisez [Azure CLI](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/install-az-ps) . Lorsque la fonctionnalité d’inscription automatique est désactivée, les machines virtuelles SQL Server ajoutées à l’abonnement doivent être inscrites manuellement auprès du fournisseur de ressources de la machine virtuelle SQL. Cela n’annule pas l’inscription des machines virtuelles SQL Server existantes qui ont déjà été inscrites.



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour désactiver l’inscription automatique à l’aide d’Azure CLI, exécutez la commande suivante : 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Pour désactiver l’inscription automatique à l’aide d’Azure PowerShell, exécutez la commande suivante : 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>Activer quatre abonnements multiples

Vous pouvez activer la fonctionnalité d’inscription automatique pour plusieurs abonnements Azure à l’aide de PowerShell. 

Pour ce faire, procédez comme suit :

1. Enregistrez [ce script](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1) dans un fichier `.ps1`, par exemple `EnableBySubscription.ps1`. 
1. Accédez à l’emplacement où vous avez enregistré le script à l’aide d’une invite de commandes d’administration ou d’une fenêtre PowerShell. 
1. Connectez-vous à Azur (`az login`).
1. Exécutez le script en passant SubscriptionIds comme paramètres, comme   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   Par exemple : 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

Les erreurs d’inscription sont stockées dans `RegistrationErrors.csv` situé dans le même répertoire que celui où vous avez enregistré et exécuté le script `.ps1`. 

## <a name="next-steps"></a>Étapes suivantes

Mettez à niveau votre mode de gestion vers [complet](sql-vm-resource-provider-register.md#upgrade-to-full) afin de tirer parti de toutes les fonctionnalités du fournisseur de ressources de machine virtuelle SQL. 
