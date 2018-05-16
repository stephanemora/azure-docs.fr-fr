---
title: 'Script PowerShell : Ajouter une image marketplace à un laboratoire personnalisé dans Azure Lab Services | Microsoft Docs'
description: Ce script PowerShell ajoute une image marketplace à un laboratoire personnalisé dans Azure Lab Services.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: 64d168c132edce4ecd128b795fbfa5ab2607cb19
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-custom-lab"></a>Utiliser PowerShell pour ajouter une image marketplace à un laboratoire personnalisé

Cet exemple de script PowerShell ajoute une image marketplace à un laboratoire personnalisé dans Azure Lab Services.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Prérequis

* **Un laboratoire personnalisé**. Le script vous demande d’avoir un laboratoire personnalisé. 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a custom lab")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : 

| Commande | Notes |
|---|---|
| [Find-AzureRmResource](/module/azurerm.resources/find-azurermresource) | Recherche des ressources en fonction des paramètres spécifiés. |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Obtient des ressources. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Modifie une ressource. |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Crée une ressource. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

D’autres exemples de scripts PowerShell pour Azure Lab Services sont disponibles dans [Exemples PowerShell pour Azure Lab Services](../samples-powershell.md).