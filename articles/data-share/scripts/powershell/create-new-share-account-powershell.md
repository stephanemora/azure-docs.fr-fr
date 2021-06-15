---
title: 'Script PowerShell : Créer un nouveau compte Azure Data Share'
description: Ce script PowerShell crée un nouveau compte Data Share.
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3c9a538a82d0490b898537a1a9e104d835898555
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110693862"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>Utiliser PowerShell pour créer un compte de partage de données dans Azure

Ce script PowerShell crée un nouveau compte Data Share. 

## <a name="sample-script"></a>Exemple de script

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name"
$location = "<Location>"
$dataShareAccountName = "<Data share account name>"

# Create a new Azure Data Share account
New-AzDataShareAccount -ResourceGroupName $resourceGroupName -Name $dataShareAccountName -Location $location
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : 

| Commande | Notes |
|---|---|
| [New-AzDataShareAccount](/powershell/module/az.datashare/new-azdatashareaccount) | Crée un compte Data Share. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/).

Des exemples supplémentaires de scripts PowerShell pour Azure Data Share sont à votre disposition dans [Exemples PowerShell pour Azure Data Share](../../samples-powershell.md).
