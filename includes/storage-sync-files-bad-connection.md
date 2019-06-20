---
title: Fichier Include
description: Fichier Include
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 359347e41264711a6ac0fa4d2dd0c3633590e917
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177136"
---
Cette erreur peut se produire chaque fois que le service Azure File Sync n’est pas accessible à partir du serveur. Vous pouvez résoudre cette erreur en parcourant les étapes suivantes :

1. Vérifiez que le service Windows `FileSyncSvc.exe` n’est pas bloqué par votre pare-feu.
2. Vérifiez que le port 443 est ouvert pour les connexions sortantes vers le service Azure File Sync. Pour cela, utilisez l’applet de commande `Test-NetConnection`. L’URL correspondant à l’espace réservé `<azure-file-sync-endpoint>` ci-dessous se trouve dans le document [Paramètres de proxy et de pare-feu d’Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Vérifiez que la configuration du proxy est définie comme prévu. Pour ce faire, utilisez l’applet de commande `Get-StorageSyncProxyConfiguration`. Vous trouverez des informations supplémentaires sur la configuration du proxy pour Azure File Sync dans [Paramètres de proxy et de pare-feu d’Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. En cas de problèmes de connectivité réseau, contactez votre administrateur réseau pour obtenir une aide supplémentaire.