---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 796b6280c2814e6358ce1942a230488cd484415d
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58405931"
---
Pour résoudre les problèmes de l’appareil, vous devez collaborer avec l’équipe de Support Microsoft. Support Microsoft devront peut-être utiliser une session de support pour se connecter à votre appareil.

1. Exécutez Windows PowerShell en tant qu’administrateur.
2. Attribuer une variable à l’adresse IP du périphérique.  À l’invite de commandes, tapez :

    $ip = « < device_ip > »

    Remplacez `<device_ip>` avec l’adresse IP de votre appareil.
 
3. Démarrer une session Windows PowerShell sur l’appareil et se connecter à l’instance d’exécution minishell.

    ```
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Force
    $minishellSession= New-PSSession -ComputerName $ip -ConfigurationName "Minishell" -Credential ~\EdgeUser
    ```
    Lorsque vous y êtes invité, fournissez le mot de passe utilisé pour se connecter à l’appareil.

4. Se connecter à l’instance d’exécution de la session prise en charge.  

    ```
    Invoke-Command -Session $minishellSession -ScriptBlock { Enable-HcsSupportAccess }
    ```  
    Cette commande génère une clé chiffrée. Copiez cette chaîne dans un éditeur de texte comme le Bloc-notes.

5. Envoyer cette clé à l’ingénieur du Support technique par courrier électronique. L’ingénieur de Support crée la clé d’accès correspondant pour la session de support.

6. L’ingénieur du Support utilisera la clé d’accès comme mot de passe dans la commande suivante :

    ``` 
    $supportSession = New-PSSession -ComputerName $ip -Credential ~\EdgeSupport -ConfigurationName SupportSession
    Enter-PSSession -Session $supportSession
    ```
7. Vous êtes maintenant dans la session de support. L’exemple de sortie suivant montre comment se connecter à la session de Support :

    ```
    PS C:\WINDOWS\system32> $ip = "10.100.10.10";
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Force
    PS C:\WINDOWS\system32> $minishellSession= New-PSSession -ComputerName $ip -ConfigurationName "Minishell" -Credential ~\EdgeUser
    WARNING: The Windows PowerShell interface of your device is intended to
    be used only for the initial network configuration. Please
    engage Microsoft Support if you need to access this interface
    to troubleshoot any potential issues you may be experiencing.
    Changes made through this interface without involving Microsoft
    Support could result in an unsupported configuration.
    PS C:\WINDOWS\system32> Invoke-Command -Session $minishellSession -ScriptBlock { Enable-HcsSupportAccess }
    UAAAADcAMQAyAEYAQwBDAEEANwA0ADIARABCADUANgA1ADkANQA0AEIAQgA0ADgAMQBEADEAMQAxADMAMgAyADYAOAA3AEIANwA0ADgAMwBFAEMAiix6gA6zBIELa6vbb73CSO9/Yo/g85QRp2g5ngw773sKgBVcirk5sTHFuSQXWRirggGEip9NI5m54iPcVxdIEcoH+2vlvxCAJVWXOLGOB6WqWDtzR3XWSwJKig95LZfBjtPO1sM5TZLu65iCRCB4AV9nOezhAoy2lGdTuZOpXP2w5FIZPFvmgR4+4m+pfsD0NQSsw+PD3hNBHvUUyIhc4WTVaIJbzoJBzg06uJnc6C0Zo1YnYD6u8SoevejXbt3dgU7m36Vg3K0qPfCPA9WNLd71uiUsV1lMLwCabj60rAuRp/qJlPRXPU5PbGeayKNQJQfUkQYh6afUxtOXohke+A==
    PS C:\WINDOWS\system32> $supportSession = New-PSSession -ComputerName $ip -Credential ~\EdgeSupport -ConfigurationName SupportSession
    PS C:\WINDOWS\system32> Enter-PSSession -Session $supportSession
    [10.100.10.10]: PS C:\Users\EdgeSupport\Documents>
    ```
    Si vous avez besoin obtenir la même clé d’accès chiffré que vous avez utilisé lors de la session de support a été activée, utilisez le `Get-HcsSupportAccessKey` applet de commande.

    ```
    [10.128.24.33]: PS C:\Users\EdgeSupport\Documents> Get-HcsSupportAccessKey
    UAAAADcAMQAyAEYAQwBDAEEANwA0ADIARABCADUANgA1ADkANQA0AEIAQgA0ADgAMQBEADEAMQAxADMAMgAyADYAOAA3AEIANwA0ADgAMwBFAEMAiix6gA6zBIELa6vbb73CSO9/Yo/g85QRp2g5ngw773sKgBVcirk5sTHFuSQXWRirggGEip9NI5m54iPcVxdIEcoH+2vlvxCAJVWXOLGOB6WqWDtzR3XWSwJKig95LZfBjtPO1sM5TZLu65iCRCB4AV9nOezhAoy2lGdTuZOpXP2w5FIZPFvmgR4+4m+pfsD0NQSsw+PD3hNBHvUUyIhc4WTVaIJbzoJBzg06uJnc6C0Zo1YnYD6u8SoevejXbt3dgU7m36Vg3K0qPfCPA9WNLd71uiUsV1lMLwCabj60rAuRp/qJlPRXPU5PbGeayKNQJQfUkQYh6afUxtOXohke+A==
    [10.128.24.33]: PS C:\Users\EdgeSupport\Documents>
    ```

    La session de support reste activée pour les 8 heures. Pour désactiver la session de support à tout moment, utilisez la `Disable-HcsSupportAccess` applet de commande.

