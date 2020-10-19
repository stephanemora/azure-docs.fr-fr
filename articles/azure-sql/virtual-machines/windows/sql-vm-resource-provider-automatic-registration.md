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
ms.openlocfilehash: b986832e5febbb2a0f88b65213f9acf0dd4c5ab5
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996894"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>Inscription automatique avec le fournisseur de ressources de machine virtuelle SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Activez la fonctionnalité d’inscription automatique dans le Portail Azure pour inscrire automatiquement tous les SQL Server actuels et futurs sur les machines virtuelles Azure avec le fournisseur de ressources de machine virtuelle SQL en mode allégé.

Cet article vous apprend à activer la fonctionnalité d’inscription automatique. Vous pouvez également [inscrire une seule machine virtuelle](sql-vm-resource-provider-register.md) ou [inscrire vos machines virtuelles en bloc](sql-vm-resource-provider-bulk-register.md) à l’aide du fournisseur de ressources de machine virtuelle SQL. 

## <a name="overview"></a>Vue d’ensemble

Le [fournisseur de ressources de machine virtuelle SQL](sql-vm-resource-provider-register.md#overview) vous permet de gérer votre machine virtuelle SQL Server à partir du Portail Azure. En outre, le fournisseur de ressources active un ensemble de fonctionnalités robustes, notamment [la mise à jour corrective automatisée](automated-patching.md), [la sauvegarde automatisée](automated-backup.md), ainsi que des fonctionnalités de surveillance et de gestion. De même, cela facilite la gestion des [licences](licensing-model-azure-hybrid-benefit-ahb-change.md) et des [éditions](change-sql-server-edition.md). Auparavant, ces fonctionnalités étaient uniquement accessibles aux images de machine virtuelle SQL Server déployées à partir de la Place de marché Azure. 

La fonctionnalité d’inscription automatique permet aux clients d’inscrire automatiquement toutes les machines virtuelles SQL Server actuelles et futures dans leur abonnement Azure avec le fournisseur de ressources de machine virtuelle SQL. Cela diffère de l’inscription manuelle, qui se concentre uniquement sur les machines virtuelles SQL Server actuelles. 

L’inscription automatique inscrit vos machines virtuelles SQL Server en mode allégé. Vous devez toujours [mettre à niveau manuellement vers le mode de gestion complet](sql-vm-resource-provider-register.md#upgrade-to-full) pour tirer parti de l’ensemble complet de fonctionnalités. 

## <a name="prerequisites"></a>Prérequis

Pour inscrire votre machine virtuelle SQL Server auprès du fournisseur de ressources, voici ce dont vous avez besoin : 

- Un [abonnement Azure](https://azure.microsoft.com/free/).
- Un modèle de ressource Azure de [machine virtuelle Windows](../../../virtual-machines/windows/quick-create-portal.md) avec [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) déployée dans le Cloud public ou Azure Government. 


## <a name="enable"></a>Activer

Pour activer l’inscription automatique de vos machines virtuelles SQL Server dans le Portail Azure, suivez ces étapes :

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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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
