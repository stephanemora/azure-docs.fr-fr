---
title: 'Script PowerShell : créer un hub de notification Azure | Microsoft Docs'
description: Ce script PowerShell crée un hub de notification Azure.
services: data-factory
author: dimazaid
manager: kpiteira
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 747d743a0573bd959b4d3c7100be8ae9451c5ed5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-create-an-azure-notification-hub"></a>Utilisez PowerShell pour créer un hub de notification Azure.

Cet exemple de script PowerShell crée un exemple de hub de notification Azure. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Prérequis

* **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/notification-hubs/create-notification-hub/create-notification-hub.ps1 "Create a notification hub")]


## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Après avoir exécuté l’exemple de script, vous pouvez utiliser la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées :

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : 

| Commande | Notes |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzureRmNotificationHubsNamespace](/powershell/module//azurerm.notificationhubs/new-azurermnotificationhubsnamespace) | Crée un espace de noms pour le hub de notification. |
| [New-AzureRmNotificationHub](/powershell/module//azurerm.notificationhubs/new-azurermnotificationhubsnamespace) | Crée un hub de notification. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).
