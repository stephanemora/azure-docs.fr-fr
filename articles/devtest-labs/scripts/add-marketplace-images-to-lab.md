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
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: 1c221f86d721a14f82f75d1cd9c81e37bba32a60
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282617"
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

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/).

D’autres exemples de scripts PowerShell pour Azure Lab Services sont disponibles dans [Exemples PowerShell pour Azure Lab Services](../samples-powershell.md).
