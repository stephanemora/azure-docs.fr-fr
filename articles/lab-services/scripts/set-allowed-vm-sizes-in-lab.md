---
title: 'Script PowerShell : définir les tailles de machine virtuelle autorisées dans Azure Lab Services | Microsoft Docs'
description: Cet article comprend un exemple de script PowerShell qui définit les tailles de machine virtuelle autorisées dans Azure Lab Services.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a1b0e9a4aed475f04ec8dcffa9bc95b7c7c713e1
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760449"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Utiliser PowerShell pour définir les tailles de machine virtuelle autorisées dans Azure Lab Services

Cet exemple de script PowerShell définit les tailles de machine virtuelle autorisées dans Azure Lab Services.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Conditions préalables requises
* **Un lab**. Le script vous demande d’avoir un lab. 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

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
