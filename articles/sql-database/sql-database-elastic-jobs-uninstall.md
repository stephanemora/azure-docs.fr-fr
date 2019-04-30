---
title: Désinstaller l’outil de tâche de base de données élastique
description: Découvrez comment désinstaller les composants de tâches de base de données élastique à l’aide du portail Azure de PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 12f923724616378a6ea6c83a947bd5362840a697
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61435393"
---
# <a name="uninstall-elastic-database-jobs-components"></a>Désinstallation des composants de Tâches de bases de données élastiques.


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


Les **Tâches de base de données élastique** peuvent être désinstallées à l’aide du portail Azure ou de PowerShell.

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>Désinstallez les composants de Tâches de bases de données élastiques à l'aide du portail Azure
1. Ouvrez le [portail Azure](https://portal.azure.com/).
2. Accédez à l'abonnement qui contient les composants de **Tâches de bases de données élastiques** , à savoir l'abonnement dans lequel les composants de Tâches de bases de données élastiques ont été installés.
3. Cliquez sur **Parcourir**, puis sur **Groupes de ressources**.
4. Sélectionnez le groupe de ressources intitulé « __ElasticDatabaseJob ».
5. Supprimez le groupe de ressources.

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>Désinstallez les composants de Tâches de bases de données élastiques à l’aide de PowerShell
1. Lancez une fenêtre de commande Microsoft Azure PowerShell et accédez au sous-répertoire des outils, sous le dossier Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x : tapez **cd tools**.
   
     PS C :\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools
2. Exécutez le script PowerShell .\UninstallElasticDatabaseJobs.ps1.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\UninstallElasticDatabaseJobs.ps1   PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\UninstallElasticDatabaseJobs.ps1

Ou exécutez simplement le script suivant, en supposant que les valeurs par défaut ont été utilisées pour l'installation des composants :

```powershell
$ResourceGroupName = "__ElasticDatabaseJob"
Switch-AzureMode AzureResourceManager

$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
if(!$resourceGroup)
{
     Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job components are uninstalled."
     return
}

Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
Remove-AzureResourceGroup -Name $ResourceGroupName -Force
Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job components are now uninstalled."
```

## <a name="next-steps"></a>Étapes suivantes
Pour réinstaller les tâches de bases de données élastiques, consultez [Installation du service de Tâche de bases de données élastiques](sql-database-elastic-jobs-service-installation.md)

Pour plus d’informations concernant les tâches de bases de données élastiques, consultez la rubrique [Vue d’ensemble des tâches de bases de données élastiques](sql-database-elastic-jobs-overview.md).
