---
title: 'Script PowerShell : Jeu autorisé de tailles de machine virtuelle dans Azure Lab Services | Microsoft Docs'
description: Ce script PowerShell définit les tailles de machine virtuelle autorisées dans Azure Lab Services.
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
ms.openlocfilehash: 0c82e304d3e3d8df1206c7c05883399b74229af7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160550"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Utiliser PowerShell pour définir les tailles de machine virtuelle autorisées dans Azure Lab Services

Cet exemple de script PowerShell définit les tailles de machine virtuelle autorisées dans Azure Lab Services.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Conditions préalables
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
