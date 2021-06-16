---
title: Fichier include pour PowerShell pour Azure DNS
description: Fichier include pour PowerShell pour Azure DNS
services: dns
author: subsarma
ms.service: dns
ms.topic: include file for PowerShell for Azure DNS
ms.date: 04/28//2021
ms.author: subsarma
ms.custom: include file for PowerShell for Azure DNS, devx-track-azurepowershell
ms.openlocfilehash: 2454ab0a95e8f69fb72e85b8fad083793e00735e
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110721848"
---
## <a name="set-up-azure-powershell-for-azure-dns"></a>Configurer Azure PowerShell pour Azure DNS

### <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [requires-azurerm](requires-azurerm.md)]

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration.

* Un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Vous devez installer la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations, consultez [Installer et configurer Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="sign-in-to-your-azure-account"></a>Connexion à votre compte Azure

Ouvrez la console PowerShell et connectez-vous à votre compte. Pour plus d’informations, consultez [Se connecter avec Azure PowerShell](/powershell/azure/azurerm/authenticate-azureps).

```azurepowershell-interactive
Connect-AzAccount
```

### <a name="select-the-subscription"></a>Sélection de l’abonnement
 
Vérifiez les abonnements associés au compte.

```azurepowershell-interactive
Get-AzSubscription
```

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Celui-ci est utilisé comme emplacement par défaut des ressources de ce groupe. Toutefois, étant donné que toutes les ressources DNS sont globales et non régionales, le choix de l’emplacement du groupe de ressources n’a aucun impact sur Azure DNS.

Ignorez cette étape si vous utilisez un groupe de ressources existant.

```azurepowershell-interactive
New-AzResourceGroup -Name MyDNSResourceGroup -location "West US"
```
