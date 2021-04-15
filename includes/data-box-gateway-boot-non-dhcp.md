---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: db4e27c0972a93d870d0a6565f1bd3212146df62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96580917"
---
Si vous effectuez un démarrage dans un environnement non DHCP, procédez comme suit pour déployer la machine virtuelle de votre Data Box Gateway.

1. [Connectez-vous à l’interface Windows PowerShell de l’appareil](#connect-to-the-powershell-interface).
2. Utilisez la cmdlet `Get-HcsIpAddress` pour répertorier les interfaces réseau activées sur votre appareil virtuel. Si votre appareil possède une seule interface réseau activée, le nom par défaut affecté à cette interface est `Ethernet`.

    L’exemple suivant montre comment utiliser cette cmdlet :

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. Utilisez l’applet de commande `Set-HcsIpAddress` pour configurer le réseau. Voir l’exemple suivant :

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

