---
title: 'Script PowerShell : Répertorier les partages existants dans Azure Data Share'
description: Ce script PowerShell répertorie et affiche les détails des partages.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2692cbd836b351e5c41dfe40168b365885997ac0
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110703896"
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
| [Get-AzDataShare](/powershell/module/az.datashare/get-azdatashare) | Obtient les listes de partages dans un compte. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/).

Des exemples supplémentaires de scripts PowerShell pour Azure Data Share sont à votre disposition dans [Exemples PowerShell pour Azure Data Share](../../samples-powershell.md).
