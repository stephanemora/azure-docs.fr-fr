---
title: Exemple de script Azure PowerShell - Sécuriser le serveur principal | Microsoft Docs
description: Exemple de script Azure PowerShell - Sécuriser le serveur principal
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
ms.openlocfilehash: a372b4fe246d74c0689475bf316513ba32e9ea5c
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48267264"
---
# <a name="secure-back-end"></a>Sécuriser le serveur principal

Cet exemple de script permet de sécuriser le serveur principal avec l’authentification mutuelle des certificats.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 3.6 ou version ultérieure pour les besoins de ce didacticiel. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/api-management/secure-backend-with-mutual-certificate-authentication/secure_backend_with_mutual_certificate_authentication.ps1 "Secures backend")]

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour la Gestion des API Azure sur la page [Exemples PowerShell](../powershell-samples.md).
