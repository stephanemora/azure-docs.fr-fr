---
title: 'Script PowerShell : Créer une invitation Azure Data Share'
description: Ce script PowerShell envoie une invitation de partage de données.
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 5f65d081e724206c1c64ad08189d1b620bbb4f2c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221330"
---
# <a name="use-a-powershell-script-to-monitor-the-usage-of-a-sent-data-share"></a>Utiliser un script PowerShell pour surveiller l’utilisation d’un partage de données envoyé

Ce script PowerShell crée une invitation de partage de données.

## <a name="sample-script"></a>Exemple de script


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$targetEmail = "<Target email>"

# Send a data share invitation
New-AzDataShareInvitation -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $dataShareName -TargetEmail $targetEmail

```


## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : 

| Commande | Notes |
|---|---|
| [New-AzDataShareInvitation](/powershell/module/az.datashare/new-azdatashareinvitation) | Créez une invitation Data Share. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/).

Des exemples supplémentaires de scripts PowerShell pour Azure Data Share sont à votre disposition dans [Exemples PowerShell pour Azure Data Share](../../samples-powershell.md).
