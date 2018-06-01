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
ms.openlocfilehash: c2a6727692a7a74b3e5fe32de8800722a9ed91b5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075185"
---
# <a name="back-up-azure-stack"></a>Sauvegarde d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Effectuez une sauvegarde à la demande sur Azure Stack avec la sauvegarde en place. Si vous devez activer le service de sauvegarde d’infrastructure, voir la section [Activer la sauvegarde d’Azure Stack à partir du portail d’administration](azure-stack-backup-enable-backup-console.md).

> [!Note]  
>  Pour obtenir des instructions sur la configuration de l’environnement PowerShell, consultez [Installer PowerShell pour Azure Stack](azure-stack-powershell-install.md).

## <a name="start-azure-stack-backup"></a>Démarrer la sauvegarde d’Azure Stack

Ouvrez Windows PowerShell avec une invite dans l’environnement de gestion de l’opérateur et exécutez les commandes suivantes :

```powershell
    Start-AzSBackup -Location $location.Name
```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Confirmer la sauvegarde dans le portail d’administration

1. Ouvrez le portail d’administration Azure Stack sur [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).
2. Sélectionnez **Plus de services** > **Sauvegarde d’infrastructure**. Choisissez **Configuration** dans le panneau **Sauvegarde d’infrastructure**.
3. Recherchez le **nom** et la **date d’exécution** de la sauvegarde dans la liste des **sauvegardes disponibles**.
4. Vérifiez que l’**état** indique une **réussite**.

<!-- You can also confirm the backup completed from the administration portal. Navigate to `\MASBackup\<datetime>\<backupid>\BackupInfo.xml`

In ‘Confirm backup completed’ section, the path at the end doesn’t make sense (ie relative to what, datetime format, etc?)
\MASBackup\<datetime>\<backupid>\BackupInfo.xml -->


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le flux de travail de récupération à partir d’un événement de perte de données. Voir [Récupérer des données suites à une perte catastrophique](azure-stack-backup-recover-data.md).
