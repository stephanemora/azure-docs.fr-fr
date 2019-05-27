---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161217"
---
Selon le système d’exploitation du client, les procédures permettant de se connecter à distance à l’appareil sont différents.

### <a name="remotely-connect-from-a-windows-client"></a>Se connecter à distance à partir d’un client Windows

Avant de commencer, assurez-vous que votre client Windows est en cours d’exécution Windows PowerShell 5.0 ou version ultérieure.

Suivez ces étapes pour vous connecter à distance à partir d’un client Windows.

1. Exécutez une session Windows PowerShell en tant qu’administrateur.
2. Assurez-vous que le service Windows Remote Management est en cours d’exécution sur votre client.  À l’invite de commandes, tapez :

    `winrm quickconfig`

3. Attribuer une variable à l’adresse IP du périphérique.

    $ip = « < device_ip > »

    Remplacez `<device_ip>` avec l’adresse IP de votre appareil.

4. Pour ajouter l’adresse IP de votre appareil à la liste des hôtes approuvés du client, tapez la commande suivante :

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Démarrez une session Windows PowerShell sur l’appareil :

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Fournir le mot de passe lorsque vous y êtes invité. Utilisez le même mot de passe qui est utilisé pour se connecter à l’interface utilisateur web locale. Le mot de passe du interface utilisateur web locale par défaut est *Password1*. Lorsque vous êtes connecté à l’appareil à l’aide de PowerShell à distance, vous consultez l’exemple de sortie suivant :  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Se connecter à distance à partir d’un client Linux

Sur le client Linux que vous utiliserez pour vous connecter :

- [Installer la plus récente de PowerShell Core pour Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) à partir de GitHub pour obtenir la fonctionnalité d’accès distant SSH. 
- [Installer uniquement le `gss-ntlmssp` package à partir du module NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Pour les clients d’Ubuntu, utilisez la commande suivante :
    - `sudo apt-get install gss-ntlmssp`

Pour plus d’informations, accédez à [communication à distance PowerShell via SSH](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6).

Suivez ces étapes pour vous connecter à distance à partir d’un client NFS.

1. Pour ouvrir la session PowerShell, tapez :

    `sudo pwsh`
 
2. Pour vous connecter à l’aide du client distant, tapez :

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Lorsque vous y êtes invité, fournissez le mot de passe utilisé pour se connecter à votre appareil.
 
> [!NOTE]
> Cette procédure ne fonctionne pas sur Mac OS.
