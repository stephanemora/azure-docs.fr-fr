---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 5e2ab0b9d7f61539a16fc685134bef6c9047229d
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104988264"
---
Selon le système d’exploitation du client, les procédures permettant de se connecter à distance à l’appareil sont différentes.

### <a name="remotely-connect-from-a-windows-client"></a>Connexion à distance depuis un client Windows


#### <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que :

- Votre client Windows exécute Windows PowerShell 5.0 ou une version ultérieure.
- Votre client Windows dispose de la chaîne de signature (certificat racine) correspondant au certificat de nœud installé sur l’appareil. Pour obtenir des instructions détaillées, consultez [Installer un certificat sur votre client Windows](../articles/databox-online/azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).
- Le fichier `hosts` sous `C:\Windows\System32\drivers\etc` pour votre client Windows contient une entrée correspondant au certificat de nœud au format suivant :

    `<Device IP>    <Node serial number>.<DNS domain of the device>`

    Voici un exemple d’entrée pour le fichier `hosts` :
 
    `10.100.10.10    1HXQG13.wdshcsso.com`
  

#### <a name="detailed-steps"></a>Procédure détaillée

Suivez cette procédure pour effectuer une connexion distante depuis un client Windows.

1. Démarrez une session Windows PowerShell en tant qu’administrateur.
2. Assurez-vous que le service Windows Remote Management est en cours d’exécution sur votre client. À l’invite de commandes, tapez :

    `winrm quickconfig`

    Pour plus d’informations, consultez [Installation et configuration de Windows Remote Management](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management#quick-default-configuration).

3. Attribuez une variable à l’adresse IP de l’appareil.

    $ip = "<ip_appareil>"

    Remplacez `<device_ip>` par l’adresse IP de votre appareil.

4. Tapez la commande suivante pour ajouter l’adresse IP de l’appareil à la liste des hôtes approuvés du client :

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Démarrez une session Windows PowerShell sur l’appareil :

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL`

    Si vous voyez une erreur liée à une relation de confiance, vérifiez si la chaîne de signature du certificat de nœud chargé sur votre appareil est également installée sur le client qui accède à votre appareil.

    > [!NOTE] 
    > Lorsque vous utilisez l’option `-UseSSL`, vous utilisez la communication à distance via PowerShell sur *https*. Nous vous recommandons de toujours utiliser le *protocole HTTPS* pour vous connecter à distance via PowerShell. Bien qu’une session *http* ne soit pas la méthode de connexion la plus sécurisée, elle est acceptable sur des réseaux approuvés.

6. Indiquez le mot de passe lorsque vous y êtes invité. Utilisez le mot de passe vous permettant de vous connecter à l’interface utilisateur web locale. Le mot de passe par défaut de cette interface est *Password1*. Lorsque vous êtes connecté à l’appareil à l’aide de PowerShell à distance, vous pouvez voir l’exemple de sortie suivant :  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Connexion à distance depuis un client Linux

Sur le client Linux que vous utiliserez pour vous connecter :

- [Installez la plus récente de PowerShell Core pour Linux](/powershell/scripting/install/installing-powershell-core-on-linux), à partir de GitHub, afin d’obtenir la fonctionnalité d’accès distant SSH. 
- [Installez uniquement le package `gss-ntlmssp` à partir du module NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Pour les clients Ubuntu, exécutez la commande ci-dessous :
    - `sudo apt-get install gss-ntlmssp`

Pour plus d’informations, voir [Accès distant à PowerShell via SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Suivez cette procédure pour effectuer une connexion distante depuis un client NFS.

1. Pour ouvrir la session PowerShell, saisissez :

    `pwsh`
 
2. Pour vous connecter à l’aide du client distant, saisissez :

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Lorsque vous y êtes invité, indiquez le mot de passe utilisé pour la connexion à votre appareil.
 
> [!NOTE]
> Cette procédure ne fonctionne pas sur Mac OS.