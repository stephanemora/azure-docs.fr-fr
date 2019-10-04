---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 880b630ae48eda086f6454f0d7108d27d3403b77
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177731"
---
Si vous effectuez un démarrage dans un environnement non DHCP, procédez comme suit pour déployer la machine virtuelle de votre Data Box Gateway.

1. [Connectez-vous à l’interface Windows PowerShell de l’appareil](#connect-to-the-powershell-interface).
2. Utilisez la cmdlet `Get-HcsIpAddress` pour répertorier les interfaces réseau activées sur votre appareil virtuel. Si votre appareil possède une seule interface réseau activée, le nom par défaut affecté à cette interface est `Ethernet`.

    L’exemple suivant montre comment utiliser cette cmdlet :

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

