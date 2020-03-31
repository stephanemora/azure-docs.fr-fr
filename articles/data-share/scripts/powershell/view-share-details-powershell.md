---
title: 'Script PowerShell : Répertorier les partages existants dans Azure Data Share | Microsoft Docs'
description: Ce script PowerShell répertorie et affiche les détails des partages.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 6314bd348c22c901001b88eda6875181a2f69df4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307135"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>Utiliser PowerShell pour afficher les détails d’un partage de données envoyé

Ce script PowerShell répertorie les partages de données à partir d’un compte existant et obtient les détails d’un partage spécifique.


## <a name="sample-script"></a>Exemple de script

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

#Lists all data shares within an account
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName

#Gets details of a specific data share
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : 

| Commande | Notes |
|---|---|
| [Get-AzDataShare](/powershell/module/az.datashare/get-azdatashare?view=azps-2.6.0) | Obtient les listes de partages dans un compte. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

Des exemples supplémentaires de scripts PowerShell pour Azure Data Share sont à votre disposition dans [Exemples PowerShell pour Azure Data Share](../../samples-powershell.md).
