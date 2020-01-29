---
title: PowerShell - Créer un rôle personnalisé dans un lab dans Azure DevTest Labs
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
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: bcad4e02105bff2dc78a4adc3ca614f997794d52
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166422"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Utiliser PowerShell pour créer un rôle personnalisé dans un lab dans Azure DevTest Labs

Cet exemple de script PowerShell crée un rôle personnalisé pouvant être utilisé dans un lab Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Conditions préalables requises
* **Un lab**. Le script vous demande d’avoir un lab. 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : 

| Commande | Notes |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Obtient les opérations d’un fournisseur de ressources Azure qui sont sécurisables à l’aide d’Azure RBAC. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Répertorie tous les rôles Azure RBAC qui peuvent être affectés. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Crée un rôle personnalisé. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

D’autres exemples de scripts PowerShell pour Azure Lab Services sont disponibles dans [Exemples PowerShell pour Azure Lab Services](../samples-powershell.md).
