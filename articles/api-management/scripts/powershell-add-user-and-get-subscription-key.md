---
title: Exemple de script PowerShell - Ajouter un utilisateur | Microsoft Docs
description: Exemple de script Azure PowerShell - Ajouter un utilisateur
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.devlang: na
ms.topic: sample
ms.date: 11/16/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 23395ed545042c2528f782d98b5182132d25980f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438539"
---
# <a name="add-a-user"></a>Ajouter un utilisateur

Cet exemple de script crée un utilisateur dans Gestion des API et permet d’obtenir une clé d’abonnement.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 3.6 ou version ultérieure pour les besoins de ce didacticiel. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/api-management/add-user-and-get-subscription-key/add_a_user_and_get_a_subscriptionKey.ps1 "Add a user")]

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour la Gestion des API Azure sur la page [Exemples PowerShell](../powershell-samples.md).
