---
title: 'Script PowerShell : Ajouter un utilisateur externe à un laboratoire personnalisé dans Azure Lab Services | Microsoft Docs'
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
ms.openlocfilehash: b089067a889f0ffd3b317fcc3f0784d176473b91
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-add-an-external-user-to-a-custom-lab"></a>Utiliser PowerShell pour ajouter un utilisateur externe à un laboratoire personnalisé

Cet exemple de script PowerShell ajoute un utilisateur externe à un laboratoire personnalisé dans Azure Lab Services. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Prérequis

* **Un laboratoire personnalisé**. Le script vous demande d’avoir un laboratoire personnalisé. 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a custom lab")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : 

| Commande | Notes |
|---|---|
| [Get-AzureRmADUser](/powershell/module/azurerm.resources/get-azurermaduser) | Tente une nouvelle fois d’obtenir l’objet utilisateur à partir d’Azure Active Directory. |
| [New-AzureRmRoleAssignment](/module/azurerm.resources/new-azurermroleassignment) | Affecte le rôle spécifié au principal spécifié, dans l’étendue spécifiée. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

D’autres exemples de scripts PowerShell pour Azure Lab Services sont disponibles dans [Exemples PowerShell pour Azure Lab Services](../samples-powershell.md).