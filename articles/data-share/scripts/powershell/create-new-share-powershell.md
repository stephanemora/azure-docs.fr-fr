---
title: 'Script PowerShell : Créer une nouvelle instance Azure Data Share'
description: Ce script PowerShell crée un nouveau partage de données dans un compte Data Share existant.
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3a3d4c85f5063623abd8d86304efcc3bcc375d22
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110704082"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Utiliser PowerShell pour créer un partage de données dans Azure

Ce script PowerShell crée un nouveau partage de données dans un compte Data Share existant.

## <a name="sample-script"></a>Exemple de script

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

# Create a new Azure Data Share
New-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : 

| Commande | Notes |
|---|---|
| [New-AzDataShare](/powershell/module/az.datashare/new-azdatashare) | Crée un partage de données. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/).

Des exemples supplémentaires de scripts PowerShell pour Azure Data Share sont à votre disposition dans [Exemples PowerShell pour Azure Data Share](../../samples-powershell.md).
