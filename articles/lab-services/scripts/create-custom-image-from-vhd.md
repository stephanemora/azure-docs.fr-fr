---
title: 'Script PowerShell : Créer une image personnalisée à partir d’un fichier VHD dans Azure Lab Services | Microsoft Docs'
description: Ce script PowerShell crée une image personnalisée à partir d’un fichier VHD dans Azure Lab Services.
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
ms.openlocfilehash: e63265d7fea18736bf5c85bcc8954a575d70a51f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>Utilisez PowerShell pour créer une image personnalisée à partir d’un fichier VHD dans Azure Lab Services.

Cet exemple de script PowerShell crée une image personnalisée à partir d’un fichier VHD dans Azure Lab Services.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Prérequis

* **Un laboratoire personnalisé**. Le script vous demande d’avoir un laboratoire personnalisé. 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a custom lab")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : 

| Commande | Notes |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Obtient des ressources. |
| [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) | Obtient les clés d’accès pour le compte Azure Storage. |
| [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) | Ajoute un déploiement Azure à un groupe de ressources. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

D’autres exemples de scripts PowerShell pour Azure Lab Services sont disponibles dans [Exemples PowerShell pour Azure Lab Services](../samples-powershell.md).