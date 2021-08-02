---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: 6f732f7c59f76c99d82d5c4f7b062532c60a5a78
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110164025"
---
Selon le système d’exploitation du client, les procédures permettant de se connecter à distance à l’appareil sont différentes.

### <a name="remotely-connect-from-a-windows-client"></a>Connexion à distance depuis un client Windows


#### <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que :

- Votre client Windows exécute Windows PowerShell 5.0 ou une version ultérieure.
- Votre client Windows dispose de la chaîne de signature (certificat racine) correspondant au certificat de nœud installé sur l’appareil. Pour obtenir des instructions détaillées, consultez [Installer un certificat sur votre client Windows](../articles/databox-online/azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).
- Le fichier `hosts` sous `C:\Windows\System32\drivers\etc` pour votre client Windows contient une entrée correspondant au certificat de nœud au format suivant :

    `<Device IP>    <Node serial number>.<DNS domain of the device>`

    Voici un exemple d’entrée pour le fichier `hosts` :
 
    `10.100.10.10    1HXQG13.wdshcsso.com`
  

#### <a name="detailed-steps"></a>Procédure détaillée

Suivez cette procédure pour effectuer une connexion distante depuis un client Windows.

1. Démarrez une session Windows PowerShell en tant qu’administrateur.
2. Assurez-vous que le service Windows Remote Management est en cours d’exécution sur votre client. À l’invite de commandes, tapez :

    ```powershell
    winrm quickconfig
    ```

    Pour plus d’informations, consultez [Installation et configuration de Windows Remote Management](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management#quick-default-configuration).

3. Assignez une variable à la chaîne de connexion utilisée dans le fichier `hosts`.

    ```powershell
    $Name = "<Node serial number>.<DNS domain of the device>"
    ``` 

    Remplacez `<Node serial number>` et `<DNS domain of the device>` par le numéro de série du nœud et le domaine DNS de votre appareil. Vous pouvez obtenir la valeur du numéro de série du nœud à partir de la page **Certificats** et celle du domaine DNS à partir de la page **Appareil** dans l’interface utilisateur web locale de votre appareil.

4. Pour ajouter cette chaîne de connexion pour votre appareil à la liste des hôtes approuvés du client, saisissez la commande suivante :

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
    ```

5. Démarrez une session Windows PowerShell sur l’appareil :

    ```powershell
    Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL
    ```

    Si vous voyez une erreur liée à une relation de confiance, vérifiez si la chaîne de signature du certificat de nœud chargé sur votre appareil est également installée sur le client qui accède à votre appareil.

6. Indiquez le mot de passe lorsque vous y êtes invité. Utilisez le mot de passe vous permettant de vous connecter à l’interface utilisateur web locale. Le mot de passe par défaut de cette interface est *Password1*. Lorsque vous êtes connecté à l’appareil à l’aide de PowerShell à distance, vous pouvez voir l’exemple de sortie suivant :  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $Name = "1HXQG13.wdshcsso.com"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [1HXQG13.wdshcsso.com]: PS>
    ```

Lorsque vous utilisez l’option `-UseSSL`, vous utilisez la communication à distance via PowerShell sur *https*. Nous vous recommandons de toujours utiliser le *protocole HTTPS* pour vous connecter à distance via PowerShell. Dans les réseaux approuvés, la communication à distance via PowerShell sur http est acceptable. Vous activez d’abord PowerShell à distance sur http dans l’interface utilisateur locale. Vous pouvez ensuite vous connecter à l’interface PowerShell de l’appareil à l’aide de la procédure précédente sans l’option `-UseSSL`.

Si vous n’utilisez pas les certificats (nous vous recommandons fortement de les utiliser !), vous pouvez ignorer la vérification de la validation du certificat en utilisant les options de session : `-SkipCACheck -SkipCNCheck -SkipRevocationCheck`.

```powershell
$sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck 
Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions    
```
Voici un exemple de sortie lorsque vous ignorez la vérification du certificat :

```powershell
PS C:\WINDOWS\system32> $Name = "1HXQG13.wdshcsso.com"
PS C:\WINDOWS\system32> $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
PS C:\WINDOWS\system32> $sessOptions

MaximumConnectionRedirectionCount : 5
NoCompression                     : False
NoMachineProfile                  : False
ProxyAccessType                   : None
ProxyAuthentication               : Negotiate
ProxyCredential                   :
SkipCACheck                       : True
SkipCNCheck                       : True
SkipRevocationCheck               : True
OperationTimeout                  : 00:03:00
NoEncryption                      : False
UseUTF16                          : False
IncludePortInSPN                  : False
OutputBufferingMode               : None
MaxConnectionRetryCount           : 0
Culture                           :
UICulture                         :
MaximumReceivedDataSizePerCommand :
MaximumReceivedObjectSize         :
ApplicationArguments              :
OpenTimeout                       : 00:03:00
CancelTimeout                     : 00:01:00
IdleTimeout                       : -00:00:00.0010000

PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions
WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please
engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing.
Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
[1HXQG13.wdshcsso.com]: PS>
```

> [!IMPORTANT]
> Dans la version actuelle, vous pouvez vous connecter à l’interface PowerShell de l’appareil uniquement via un client Windows. L’option `-UseSSL` ne fonctionne pas avec les clients Linux.

<!--### Remotely connect from a Linux client-->

<!--On the Linux client that you'll use to connect:

- [Install the latest PowerShell Core for Linux](/powershell/scripting/install/installing-powershell-core-on-linux) from GitHub to get the SSH remoting feature. 
- [Install only the `gss-ntlmssp` package from the NTLM module](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). For Ubuntu clients, use the following command:
    - `sudo apt-get install gss-ntlmssp`

For more information, go to [PowerShell remoting over SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Follow these steps to remotely connect from an NFS client.

1. To open PowerShell session, type:

    `pwsh`
 
2. For connecting using the remote client, type:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    When prompted, provide the password used to sign into your device.
 
> [!NOTE]
> This procedure does not work on Mac OS.-->