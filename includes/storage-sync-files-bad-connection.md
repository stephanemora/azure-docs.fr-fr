---
title: Fichier include
description: Fichier include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4407ffb9ab16a720ef00a288d72b0fb4c2dbced1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774560"
---
Cette erreur peut se produire chaque fois que le service Azure File Sync n’est pas accessible à partir du serveur. Vous pouvez résoudre cette erreur en parcourant les étapes suivantes :

1. Vérifiez que le service Windows `FileSyncSvc.exe` n’est pas bloqué par votre pare-feu.
2. Vérifiez que le port 443 est ouvert pour les connexions sortantes vers le service Azure File Sync. Pour cela, utilisez l’applet de commande `Test-NetConnection`. L’URL correspondant à l’espace réservé `<azure-file-sync-endpoint>` ci-dessous se trouve dans le document [Paramètres de proxy et de pare-feu d’Azure File Sync](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Vérifiez que la configuration du proxy est définie comme prévu. Pour ce faire, utilisez l’applet de commande `Get-StorageSyncProxyConfiguration`. Vous trouverez des informations supplémentaires sur la configuration du proxy pour Azure File Sync dans [Paramètres de proxy et de pare-feu d’Azure File Sync](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. Utilisez l’applet de commande Test-StorageSyncNetworkConnectivity pour vérifier la connectivité réseau aux points de terminaison de service. Pour plus d’informations, consultez [Tester la connectivité réseau aux points de terminaison de service](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints).    

5. En cas de problèmes de connectivité réseau, contactez votre administrateur réseau pour obtenir une aide supplémentaire.