---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556791"
---
Si vous rencontrez des problèmes d’appareil, vous pouvez créer un package de prise en charge dans les journaux système. Le Support Microsoft utilise ce package pour résoudre les problèmes. Suivez ces étapes pour créer un package de prise en charge :

1. [Se connecter à l’interface PowerShell de votre appareil](#connect-to-the-powershell-interface).
2. Utilisez le `Get-HcsNodeSupportPackage` commande pour créer un package de prise en charge. L’utilisation de l’applet de commande est la suivante :

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    L’applet de commande collecte les journaux à partir de votre appareil et copie ces journaux à un réseau spécifié ou à un partage local.

    Les paramètres utilisés sont comme suit :

    - `-Path` -Spécifiez le réseau ou le chemin d’accès local pour copier le package de prise en charge. (obligatoire)
    - `-Credential` -Spécifiez les informations d’identification pour accéder au chemin protégé.
    - `-Zip` -Spécifiez pour générer un fichier zip.
    - `-Include` -Spécifier afin d’inclure les composants à inclure dans le package de prise en charge. Si non spécifié, `Default` est supposé.
    - `-IncludeArchived` -Spécifiez pour inclure les journaux archivés dans le package de prise en charge.
    - `-IncludePeriodicStats` -Spécifiez pour inclure les journaux stat périodiques dans le package de prise en charge.

    
