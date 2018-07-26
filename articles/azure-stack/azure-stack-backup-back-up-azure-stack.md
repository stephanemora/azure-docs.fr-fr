---
title: Sauvegarde d’Azure Stack | Microsoft Docs
description: Effectuez une sauvegarde à la demande sur Azure Stack avec la sauvegarde en place.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/08/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: c9e7ffae1b988d0940d10acdb1b387a25e0466ec
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242884"
---
# <a name="back-up-azure-stack"></a>Sauvegarde d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Effectuez une sauvegarde à la demande sur Azure Stack avec la sauvegarde en place. Pour obtenir des instructions sur la configuration de l’environnement PowerShell, consultez [Installer PowerShell pour Azure Stack](azure-stack-powershell-install.md). Pour vous connecter à Azure Stack, consultez [Configurer l’environnement de l’opérateur et se connecter à Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="start-azure-stack-backup"></a>Démarrer la sauvegarde d’Azure Stack

Utilisez Start-AzSBackup pour lancer une nouvelle sauvegarde avec la variable - AsJob afin de suivre la progression. 

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output
```

## <a name="confirm-backup-completed-via-powershell"></a>Confirmer la sauvegarde effectuée via PowerShell

```powershell
    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

- Le résultat doit ressembler à la sortie suivante :

  ```powershell
      BackupDataVersion : 1.0.1
      BackupId          : <backup ID>
      RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
      Status            : Succeeded
      CreatedDateTime   : 7/6/2018 6:46:24 AM
      TimeTakenToCreate : PT20M32.364138S
      DeploymentID      : <deployment ID>
      StampVersion      : 1.1807.0.41
      OemVersion        : 
      Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
      Name              : local/<local name>
      Type              : Microsoft.Backup.Admin/backupLocations/backups
      Location          : local
      Tags              : {}
  ```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Confirmer la sauvegarde dans le portail d’administration

1. Ouvrez le portail d’administration Azure Stack sur [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).
2. Sélectionnez **Plus de services** > **Sauvegarde d’infrastructure**. Choisissez **Configuration** dans le panneau **Sauvegarde d’infrastructure**.
3. Recherchez le **nom** et la **date d’exécution** de la sauvegarde dans la liste des **sauvegardes disponibles**.
4. Vérifiez que l’**état** indique une **réussite**.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le flux de travail de récupération à partir d’un événement de perte de données. Voir [Récupérer des données suites à une perte catastrophique](azure-stack-backup-recover-data.md).