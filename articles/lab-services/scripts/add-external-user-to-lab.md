---
title: PowerShell - Ajouter un utilisateur externe à un lab dans Azure DevTest Labs
description: Ce script PowerShell ajoute un utilisateur externe à un lab dans Azure DevTest Labs.
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
ms.openlocfilehash: da5cc4a3809b1867e8aa2c3b21e88c2f30ae3d4d
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169678"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>Utiliser PowerShell pour ajouter un utilisateur externe à un lab dans Azure DevTest Labs

Cet exemple de script PowerShell ajoute un utilisateur externe à un lab dans Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Conditions préalables requises
* **Un lab**. Le script vous demande d’avoir un lab. 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : 

| Commande | Notes |
|---|---|
| [Get-AzADUser](/powershell/module/az.resources/get-azaduser) | Tente une nouvelle fois d’obtenir l’objet utilisateur à partir d’Azure Active Directory. |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | Affecte le rôle spécifié au principal spécifié, dans l’étendue spécifiée. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

D’autres exemples de scripts PowerShell pour Azure Lab Services sont disponibles dans [Exemples PowerShell pour Azure Lab Services](../samples-powershell.md).
