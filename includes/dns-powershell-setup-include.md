---
title: Fichier include pour PowerShell pour Azure DNS
description: Fichier include pour PowerShell pour Azure DNS
services: dns
author: subsarma
ms.service: dns
ms.topic: include file for PowerShell for Azure DNS
ms.date: 03/21/2018
ms.author: subsarma
ms.custom: include file for PowerShell for Azure DNS
ms.openlocfilehash: 682b3d3fee4ff1cdbed6e05a51fd8828b6eb155f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293612"
---
## <a name="set-up-azure-powershell-for-azure-dns"></a>Configurer Azure PowerShell pour Azure DNS

### <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [requires-azurerm](requires-azurerm.md)]

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration.

* Un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Vous devez installer la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations, consultez [Installer et configurer Azure PowerShell](/powershell/azureps-cmdlets-docs).

Pour utiliser des zones privées (préversion publique), vous devez également vérifier que vous disposez des modules et versions PowerShell ci-dessous. 
* AzureRM.Dns - [version 4.1.0](https://www.powershellgallery.com/packages/AzureRM.Dns/4.1.0) ou supérieure
* AzureRM.Network - [version 5.4.0](https://www.powershellgallery.com/packages/AzureRM.Network/5.4.0) ou supérieure

```powershell 
Find-Module -Name AzureRM.Dns 
``` 
 
```powershell 
Find-Module -Name AzureRM.Network 
``` 
 
Le résultat des commandes ci-dessus doit indiquer que la version d’AzureRM.Dns est 4.1.0 ou supérieure et que la version d’AzureRM.Network est 5.4.0 ou supérieure.  

Si votre système contient des versions antérieures, vous pouvez installer la dernière version d’Azure PowerShell ou télécharger et installer les modules ci-dessus à partir de PowerShell Gallery à l’aide des liens ci-dessus en regard des versions des modules. Vous pouvez les installer ensuite à l’aide des commandes ci-dessous. Les deux modules sont nécessaires et sont entièrement rétrocompatibles. 

```powershell
Install-Module -Name AzureRM.Dns -Force
```

```powershell
Install-Module -Name AzureRM.Network -Force
```

### <a name="sign-in-to-your-azure-account"></a>Connexion à votre compte Azure

Ouvrez la console PowerShell et connectez-vous à votre compte. Pour plus d’informations, consultez [connectez-vous](/powershell/azure/install-az-ps?view=azps-1.4.0#sign-in).

```powershell
Connect-AzureRmAccount
```

### <a name="select-the-subscription"></a>Sélection de l’abonnement
 
Vérifiez les abonnements associés au compte.

```powershell
Get-AzureRmSubscription
```

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Celui-ci est utilisé comme emplacement par défaut des ressources de ce groupe. Toutefois, étant donné que toutes les ressources DNS sont globales et non régionales, le choix de l’emplacement du groupe de ressources n’a aucun impact sur Azure DNS.

Ignorez cette étape si vous utilisez un groupe de ressources existant.

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>S’inscrire auprès du fournisseur de ressources

Le service Azure DNS est géré par le fournisseur de ressources Microsoft.Network. Votre abonnement Azure doit être enregistré auprès de ce fournisseur de ressources pour que vous puissiez utiliser Azure DNS. Cette opération n’est à effectuer qu’une fois par abonnement.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```
