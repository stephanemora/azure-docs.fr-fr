---
title: 'Script PowerShell : Créer un rôle personnalisé dans un laboratoire personnalisé dans Azure Lab Services | Microsoft Docs'
description: Ce script PowerShell ajoute un utilisateur externe à un laboratoire personnalisé dans Azure Lab Services.
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
ms.openlocfilehash: df91c9f842d338e1725fec2734129f2f1f3d3721
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-create-a-custom-role-in-a-custom-lab-in-azure-lab-services"></a>Utiliser PowerShell pour créer un rôle personnalisé dans un laboratoire personnalisé Azure Lab Services

Cet exemple de script PowerShell crée un rôle personnalisé pouvant être utilisé dans un laboratoire personnalisé Azure Lab Services. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Prérequis

* **Un laboratoire personnalisé**. Le script vous demande d’avoir un laboratoire personnalisé. 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a custom lab")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : 

| Commande | Notes |
|---|---|
| [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) | Obtient les opérations d’un fournisseur de ressources Azure qui sont sécurisables à l’aide d’Azure RBAC. |
| [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) | Répertorie tous les rôles Azure RBAC qui peuvent être affectés. |
| [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) | Crée un rôle personnalisé. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

D’autres exemples de scripts PowerShell pour Azure Lab Services sont disponibles dans [Exemples PowerShell pour Azure Lab Services](../samples-powershell.md).