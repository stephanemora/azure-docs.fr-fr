---
title: PowerShell - Ajouter une image de place de marché à un lab dans Azure DevTest Labs
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 3d699a22ed06022c6d9df12ade7f202fb304648c
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166448"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Utiliser PowerShell pour ajouter une image de place de marché à un lab dans Azure DevTest Labs

Cet exemple de script PowerShell ajoute une image de place de marché à un lab dans Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Conditions préalables requises
* **Un lab**. Le script vous demande d’avoir un lab. 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : 

| Commande | Notes |
|---|---|
| Find-AzResource | Recherche des ressources en fonction des paramètres spécifiés. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Obtient des ressources. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modifie une ressource. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Crée une ressource. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

D’autres exemples de scripts PowerShell pour Azure Lab Services sont disponibles dans [Exemples PowerShell pour Azure Lab Services](../samples-powershell.md).
