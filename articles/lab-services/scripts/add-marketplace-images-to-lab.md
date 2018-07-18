---
title: 'Script PowerShell : Ajouter une image de place marché à un lab dans Azure DevTest Labs | Microsoft Docs'
description: Ce script PowerShell ajoute une image de place de marché à un lab dans Azure DevTest Labs.
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
ms.openlocfilehash: 9a65237b3eba6c9878d73148f0143f20dd60dd79
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636546"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Utiliser PowerShell pour ajouter une image de place de marché à un lab dans Azure DevTest Labs

Cet exemple de script PowerShell ajoute une image de place de marché à un lab dans Azure DevTest Labs. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Prérequis
* **Un lab**. Le script vous demande d’avoir un lab. 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

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