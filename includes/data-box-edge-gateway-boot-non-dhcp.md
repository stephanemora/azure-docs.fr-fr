---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 880b630ae48eda086f6454f0d7108d27d3403b77
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556581"
---
Si vous démarrez dans un environnement non DHCP, suivez ces étapes pour déployer l’ordinateur virtuel pour votre passerelle de la zone de données.

1. [Se connecter à l’interface Windows PowerShell de l’appareil](#connect-to-the-powershell-interface).
2. Utilisez le `Get-HcsIpAddress` applet de commande pour répertorier les interfaces réseau activées sur votre appareil virtuel. Si votre appareil possède une seule interface réseau activée, le nom par défaut affecté à cette interface est `Ethernet`.

    L’exemple suivant illustre l’utilisation de cette applet de commande :

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

