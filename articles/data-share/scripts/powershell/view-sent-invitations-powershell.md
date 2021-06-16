---
title: 'Script PowerShell : Répertorier les invitations Azure Data Share envoyées à un consommateur'
description: Découvrez comment ce script PowerShell permet d'envoyer des invitations à un consommateur et consultez un exemple de script que vous pouvez utiliser.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0b39c31271a0382cce3388e9c8176ba943bc43e6
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110703869"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>Utiliser PowerShell pour obtenir une invitation de partage de données

Ce script PowerShell reçoit les invitations envoyées à un consommateur.

## <a name="sample-script"></a>Exemple de script
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : 

| Commande | Notes |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | Obtient et répertorie les invitations de partage de données envoyées. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/).

Des exemples supplémentaires de scripts PowerShell pour Azure Data Share sont à votre disposition dans [Exemples PowerShell pour Azure Data Share](../../samples-powershell.md).
