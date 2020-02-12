---
title: Préparer un disque dur virtuel Windows à charger sur Azure
description: Découvrez comment préparer un disque dur virtuel ou VHDX Windows pour le charger sur Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: 933f0c52cf0d65c7dca480971589c0d0f2ebabf0
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906782"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Préparer un disque dur virtuel Windows à charger sur Azure

Avant de charger une machine virtuelle Windows locale sur Azure, vous devez préparer le disque dur virtuel (VHD ou VHDX). Azure prend en charge les machines virtuelles des générations 1 et 2 qui sont au format de fichier VHD et ont un disque de taille fixe. La taille maximale autorisée pour le disque dur virtuel s’élève à 1 023 Go. 

Sur une machine virtuelle de génération 1, vous pouvez convertir un système de fichiers VHDX en disque dur virtuel. Vous pouvez également convertir un disque de taille dynamique en disque de taille fixe. En revanche, vous ne pouvez pas modifier la génération d’une machine virtuelle. Pour plus d’informations, consultez la page [Dois-je créer une machine virtuelle de génération 1 ou 2 dans Hyper-V ?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) et [Support Azure pour machines virtuelles de génération 2 (aperçu)](generation-2.md).

Pour plus d’informations sur la stratégie de prise en charge des machines virtuelles Azure, consultez la page [Prise en charge des logiciels serveur Microsoft pour les machines virtuelles Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!NOTE]
> Les instructions de cet article s’appliquent à :
>1. la version 64 bits de Windows Server 2008 R2 et aux systèmes d’exploitation Windows Server ultérieurs. Pour plus d’informations sur l’exécution d’un système d'exploitation 32 bits dans Azure, consultez l’article [Prise en charge pour les systèmes d’exploitation 32 bits sur des machines virtuelles Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).
>2. Si un outil de récupération d'urgence comme Azure Site Recovery ou Azure Migrate est utilisé pour effectuer une migration de la charge de travail, ce processus reste nécessaire et doit être suivi sur le système d’exploitation invité pour préparer l’image avant la migration.

## <a name="system-file-checker-sfc-command"></a>Commande SFC (vérificateur des fichiers système)

### <a name="run-windows-system-file-checker-utility-run-sfc-scannow-on-os-prior-to-generalization-step-of-creating-customer-os-image"></a>Exécuter le vérificateur des fichiers système (exécuter sfc/scannow) sur le système d’exploitation avant l’étape de généralisation de création d’une image de système d’exploitation client

La commande SFC permet de vérifier et de remplacer les fichiers système Windows.

Pour exécuter la commande SFC :

1. Ouvrez une invite de commandes avec élévation de privilèges en tant qu’administrateur.
1. Entrez `sfc /scannow`, puis sélectionnez **Entrée**.

    ![Vérificateur des fichiers système](media/prepare-for-upload-vhd-image/system-file-checker.png)


Une fois l’analyse SFC terminée, essayez d’installer les mises à jour Windows et redémarrez l’ordinateur.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>Convertissez le disque virtuel en disque VHD de taille fixe

Pour convertir votre disque virtuel au format exigé par Azure, utilisez une des méthodes décrites dans cette section :

1. Sauvegardez la machine virtuelle avant d’exécuter le processus de conversion de disque virtuel.

1. Vérifiez que le disque dur virtuel Windows fonctionne correctement sur le serveur local. Résolvez toute erreur dans la machine virtuelle elle-même avant de tenter une conversion ou un chargement sur Azure.

1. Concernant la taille du disque dur virtuel :

   1. Tous les VDH sur Azure doivent avoir une taille virtuelle alignée à 1 Mo. Avant de convertir un disque brut en VHD, vous devez vous assurer que la taille du disque brut est un multiple de 1 Mo. Les fractions d’un mégaoctet entraînent des erreurs lors de la création d’images à partir du disque dur virtuel chargé.

   2. La taille maximale autorisée pour le disque dur virtuel du système d’exploitation est de 2 To.


Après avoir converti le disque, créez une machine virtuelle qui utilise le disque. Démarrez et connectez-vous à la machine virtuelle pour finir de la préparer en vue du chargement.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Utilisez le gestionnaire Hyper-V pour convertir le disque 
1. Ouvrez le Gestionnaire Hyper-V et sélectionnez votre ordinateur local sur la gauche. Dans le menu au-dessus de la liste d’ordinateurs, sélectionnez **Action** > **Modifier le disque**.
2. À la page **Rechercher un disque dur virtuel**, sélectionnez le disque virtuel.
3. À la page **Choisir une action**, sélectionnez **Convertir** > **suivant**.
4. Pour convertir un disque VHDX, sélectionnez **VHD** > **Suivant**.
5. Pour convertir un disque de taille dynamique, sélectionnez **Taille fixe** > **Suivan**t.
6. Recherchez et sélectionnez le chemin d’enregistrement du nouveau fichier de disque VHD.
7. Sélectionnez **Terminer**.

> [!NOTE]
> Utilisez une session PowerShell avec élévation de privilèges pour exécuter les commandes dans cet article.

### <a name="use-powershell-to-convert-the-disk"></a>Utilisez PowerShell pour convertir le disque 
Vous pouvez convertir un disque virtuel à l’aide de la commande [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) dans Windows PowerShell. Sélectionnez **Exécuter en tant qu’administrateur** lorsque vous démarrez PowerShell. 

L’exemple de commande suivant convertit le disque VHDX en disque dur virtuel. La commande convertit également le disque de taille dynamique en disque de taille fixe.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

Dans cette commande, remplacez la valeur de `-Path` par le chemin du disque dur virtuel que vous souhaitez convertir. Remplacez la valeur de `-DestinationPath` par le nouveau chemin du disque converti.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Convertir à partir du format de disque VMDK VMware
Si vous disposez d’une image de machine virtuelle Windows au [format de fichier VMDK](https://en.wikipedia.org/wiki/VMDK), utilisez [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) pour le convertir au format de disque dur virtuel. Pour plus d'informations, consultez [How to convert a VMware VMDK to Hyper-V VHD](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (en anglais).

## <a name="set-windows-configurations-for-azure"></a>Définir les configurations Windows pour Azure

> [!NOTE]
> La plateforme Azure monte un fichier ISO sur le DVD-ROM lorsqu’une machine virtuelle Windows est créée à partir d’une image généralisée.
> C’est la raison pour laquelle le DVD-ROM doit être activé dans le système d’exploitation de l’image généralisée. S’il est désactivé, la machine virtuelle Windows est bloquée sur OOBE.

Sur la machine virtuelle que vous souhaitez charger dans Azure, exécutez les commandes suivantes dans une [fenêtre d’invite de commandes avec élévation de privilèges](https://technet.microsoft.com/library/cc947813.aspx) :

1. Supprimez tout itinéraire statique persistant de la table de routage :
   
   * Pour afficher la table d’itinéraires, exécutez `route print` dans la fenêtre d’invite de commandes.
   * Vérifier les sections `Persistence Routes`. S’il existe un itinéraire persistant, utilisez la commande `route delete` pour le supprimer.
2. Supprimez le proxy WinHTTP :
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Si la machine virtuelle doit fonctionner avec un proxy spécifique, vous devez ajouter une exception de proxy à l’adresse IP Azure ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)) pour que la machine virtuelle puisse se connecter à Azure :
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Définissez la stratégie SAN des disques sur [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx) :
   
    ```PowerShell
    diskpart 
    ```
    Dans la fenêtre d’invite de commandes ouverte, entrez les commandes suivantes :

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Définissez l’heure de temps universel coordonné (UTC) pour Windows. Définissez également le type de démarrage du service de temps Windows (`w32time`) sur `Automatic` :
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -Name "RealTimeIsUniversal" -Value 1 -Type DWord -Force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Définissez le profil d’alimentation sur de hautes performances :

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Vérifiez que les variables d’environnement `TEMP` et `TMP` sont définies avec leurs valeurs par défaut :

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
    ```

## <a name="check-the-windows-services"></a>Vérifier les services Windows
Vérifiez que chacun des services Windows suivants est défini sur les valeurs par défaut Windows. Ces services sont le minimum qui doit être configuré pour garantir la connectivité de la machine virtuelle. Pour réinitialiser les paramètres de démarrage, exécutez les commandes suivantes :
   
```PowerShell
Get-Service -Name bfe | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dhcp | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dnscache | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name IKEEXT | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name iphlpsvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name netlogon | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name netman | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name nsi | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name TermService | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name MpsSvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name RemoteRegistry | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
```
## <a name="update-remote-desktop-registry-settings"></a>Mettez à jour les paramètres de registre du Bureau à distance
Assurez-vous que les paramètres suivants sont configurés correctement pour un accès à distance :

>[!NOTE] 
>Il est possible que vous receviez un message d’erreur lorsque vous exécutez `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <object name> -Value <value>`. Vous pouvez sans risque ignorer ce message. Il indique simplement que le domaine n’envoie pas cette configuration via un objet de stratégie de groupe.

1. Le protocole RDP (Remote Desktop Protocol) est activé :
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force
    ```
   
2. Le port RDP est correctement configuré. Le port par défaut est 3389 :
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "PortNumber" -Value 3389 -Type DWord -Force
    ```
    Lorsque vous déployez une machine virtuelle, les règles par défaut sont créés sur le port 3389. Si vous souhaitez modifier le numéro de port, faites-le une fois la machine virtuelle déployée dans Azure.

3. L’écouteur surveille chaque interface réseau :
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "LanAdapter" -Value 0 -Type DWord -Force
   ```
4. Configurez le mode d’authentification au niveau du réseau (NLA) pour les connexions RDP :
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "UserAuthentication" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SecurityLayer" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -Force
     ```

5. Définissez la valeur KeepAlive :
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveEnable" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveInterval" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "KeepAliveTimeout" -Value 1 -Type DWord -Force
    ```
6. Reconnectez-vous :
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDisableAutoReconnect" -Value 0 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fInheritReconnectSame" -Value 1 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fReconnectSame" -Value 0 -Type DWord -Force
    ```
7. Limitez le nombre de connexions simultanées :
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "MaxInstanceCount" -Value 4294967295 -Type DWord -Force
    ```
8. Supprimez les certificats auto-signés liés à l’écouteur RDP :
    
    ```PowerShell
    if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains "SSLCertificateSHA1Hash")
    {
        Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SSLCertificateSHA1Hash" -Force
    }
    ```
    Ce code garantit que vous pouvez vous connecter au début du déploiement de la machine virtuelle. Si vous devez vérifier ça ultérieurement, vous pouvez le faire une fois que la machine virtuelle est déployée dans Azure.

9. Si la machine virtuelle doit faire partie d’un domaine, vérifiez les stratégies suivantes pour vous assurer que les anciens paramètres ne sont pas rétablis. 
    
    | Objectif                                     | Stratégie                                                                                                                                                       | Valeur                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | Activer le protocole RDP                           | Configuration de l’ordinateur\Stratégies\Paramètres Windows\Modèles d’administration\Composants\Services Bureau à distance\Hôte de session Bureau à distance\Connexions         | Autoriser les utilisateurs à se connecter à distance avec le Bureau à distance                                  |
    | Stratégie de groupe d’authentification au niveau du réseau                         | Paramètres\Modèles d’administration\Composants\Services Bureau à distance\Hôte de session Bureau à distance\Sécurité                                                    | Exigez une authentification de l’utilisateur pour l’accès à distance à l’aide de l’authentification NLA |
    | Paramètres de connexion toujours active                      | Configuration de l’ordinateur\Stratégies\Paramètres Windows\Modèles d’administration\Composants Windows\Services Bureau à distance\Hôte de session Bureau à distance\Connexions | Configurer l’intervalle de connexion KeepAlive                                                 |
    | Paramètres de reconnexion                       | Configuration de l’ordinateur\Stratégies\Paramètres Windows\Modèles d’administration\Composants Windows\Services Bureau à distance\Hôte de session Bureau à distance\Connexions | Se reconnecter automatiquement                                                                   |
    | Nombre limité de paramètres de connexion | Configuration de l’ordinateur\Stratégies\Paramètres Windows\Modèles d’administration\Composants Windows\Services Bureau à distance\Hôte de session Bureau à distance\Connexions | Limiter le nombre de connexions                                                              |

## <a name="configure-windows-firewall-rules"></a>Configurer les règles du Pare-feu Windows
1. Activez le pare-feu Windows sur les trois profils (domaine, standard et public) :

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Exécutez la commande suivante dans PowerShell pour autoriser WinRM sur les trois profils de pare-feu (domaine, privé et public) et pour activer le service à distance PowerShell :
   
   ```PowerShell
    Enable-PSRemoting -Force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Activez les règles de pare-feu suivantes pour autoriser le trafic RDP :

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Activez la règle de partage de fichiers et d’imprimantes afin que la machine virtuelle puisse répondre à une commande ping à l’intérieur du réseau virtuel :

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Si la machine virtuelle doit faire partie d’un domaine, vérifiez les stratégies Azure AD suivantes pour vous assurer que les anciens paramètres ne sont pas rétablis. 

    | Objectif                                 | Stratégie                                                                                                                                                  | Valeur                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Activer les profils de pare-feu Windows | Configuration de l’ordinateur\Stratégies\Paramètres Windows\Modèles d’administration\Réseau\Connexion réseau\Pare-feu Windows\Profil de domaine\Pare-feu Windows   | Protéger toutes les connexions réseau         |
    | Activer le protocole RDP                           | Configuration de l’ordinateur\Stratégies\Paramètres Windows\Modèles d’administration\Réseau\Connexion réseau\Pare-feu Windows\Profil de domaine\Pare-feu Windows   | Autoriser les exceptions du Bureau à distance entrantes |
    |                                      | Configuration de l’ordinateur\Stratégies\Paramètres Windows\Modèles d’administration\Réseau\Connexion réseau\Pare-feu Windows\Profil standard\Pare-feu Windows | Autoriser les exceptions du Bureau à distance entrantes |
    | Activer le protocole ICMP-V4                       | Configuration de l’ordinateur\Stratégies\Paramètres Windows\Modèles d’administration\Réseau\Connexion réseau\Pare-feu Windows\Profil de domaine\Pare-feu Windows   | Autoriser les exceptions ICMP                   |
    |                                      | Configuration de l’ordinateur\Stratégies\Paramètres Windows\Modèles d’administration\Réseau\Connexion réseau\Pare-feu Windows\Profil standard\Pare-feu Windows | Autoriser les exceptions ICMP                   |

## <a name="verify-the-vm"></a>Vérification de la machine virtuelle 

Assurez-vous que la machine virtuelle est saine, sécurisé et accessible au protocole RDP (Remote Desktop Protocol) : 

1. Pour vous assurer de l’intégrité et de la cohérence du disque, vérifiez le disque au redémarrage suivant de la machine virtuelle :

    ```PowerShell
    Chkdsk /f
    ```
    Vérifiez que le rapport indique que le disque est propre et sain.

2. Définissez les paramètres Données de configuration de démarrage (BCD). 

    > [!NOTE]
    > Utilisez une fenêtre PowerShell avec élévation de privilèges pour exécuter ces commandes.
   
   ```powershell
    bcdedit /set "{bootmgr}" integrityservices enable
    bcdedit /set "{default}" device partition=C:
    bcdedit /set "{default}" integrityservices enable
    bcdedit /set "{default}" recoveryenabled Off
    bcdedit /set "{default}" osdevice partition=C:
    bcdedit /set "{default}" bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set "{bootmgr}" displaybootmenu yes
    bcdedit /set "{bootmgr}" timeout 5
    bcdedit /set "{bootmgr}" bootems yes
    bcdedit /ems "{current}" ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
3. Le journal de vidage peut être utile pour résoudre les problèmes de blocage de Windows. Activez la collecte des journaux de vidage :

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
    New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Vérifiez que le référentiel Windows Management Instrumentation (WMI) est cohérent :

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Si le référentiel est endommagé, consultez [WMI: Corruption du référentiel ou pas](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Assurez-vous qu’aucune autre application n’utilise le port 3389. Ce port est utilisé pour le service RDP dans Azure. Pour voir quels ports sont utilisés sur la machine virtuelle, exécutez `netstat -anob` :

    ```PowerShell
    netstat -anob
    ```

6. Pour charger un disque dur virtuel Windows qui est un contrôleur de domaine :

   * Suivez [ces étapes supplémentaires](https://support.microsoft.com/kb/2904015) pour préparer le disque.

   * Assurez-vous que vous connaissez le mot de passe du mode de restauration des services d’annuaire (DSRM) pour le cas où vous devriez démarrer la machine virtuelle en mode DSRM à un moment donné. Pour plus d’informations, consultez [Définir un mot de passe DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Assurez-vous que vous connaissez le compte administrateur intégré et le mot de passe. Vous souhaiterez peut-être réinitialiser le mot de passe administrateur local actuel et vérifier que vous pouvez utiliser ce compte pour vous connecter à Windows par le biais de la connexion RDP. Cette autorisation d’accès est contrôlée par l’objet de stratégie de groupe « Autoriser l’ouverture de session par les services Bureau à distance ». Affichez cet objet dans l’Éditeur d’objets de stratégie de groupe ici :

    Configuration ordinateur\Paramètres Windows\Paramètres de sécurité\Stratégies locales\Attribution des droits utilisateur

8. Vérifiez les stratégies Azure AD suivantes pour vous assurer que votre accès RDP via RDP ou via le réseau n’est pas bloqué :

    - Configuration de l’ordinateur\Paramètres Windows\Paramètres de sécurité\Stratégies locales\Attribution des droits utilisateurs\Refuser l’accès à cet ordinateur depuis le réseau

    - Configuration de l’ordinateur\Paramètres Windows\Paramètres de sécurité\Stratégies locales\Attribution des droits utilisateurs\Refuser la connexion via les services Bureau à distance


9. Vérifiez la stratégie Azure AD suivante pour vous assurer que vous ne supprimez pas un des comptes d’accès nécessaires :

   - Configuration de l’ordinateur\Paramètres Windows\Paramètres de sécurité\Stratégies locales\Attribution des droits utilisateurs\Accéder à cet ordinateur à partir du réseau

   La stratégie doit répertorier les groupes suivants :

   - Administrateurs

   - Opérateurs de sauvegarde

   - Tout le monde

   - Utilisateurs

10. Redémarrez la machine virtuelle pour vous assurer que Windows est toujours sain et qu’il est accessible par le biais de la connexion RDP. À ce stade, vous souhaiterez peut-être créer une machine virtuelle dans votre Hyper-V local pour vous assurer que la machine virtuelle démarre complètement. Faites un test pour vous assurer que vous pouvez contacter la machine virtuelle via RDP.

11. Supprimez tous les filtres Interface TDI supplémentaires. Par exemple, supprimez les logiciels qui analysent les paquets TCP ou les pare-feu supplémentaires. Si vous devez vérifier ça ultérieurement, vous pouvez le faire une fois que la machine virtuelle est déployée dans Azure.

12. Désinstallez tous les autres logiciels ou pilotes tiers liés aux composants physiques ou toute autre technologie de virtualisation.

### <a name="install-windows-updates"></a>Installer les mises à jour Windows
Dans l’idéal, vous devez conserver l’ordinateur à jour au *niveau du correctif logiciel*. Si ce n’est pas possible, vérifiez que les mises à jour suivantes sont installées. Pour obtenir les dernières mises à jour, consultez les pages de l’historique des mises à jour de Windows : [Windows 10 et Windows Server 2019](https://support.microsoft.com/help/4000825), [Windows 8.1 et Windows Server 2012 R2](https://support.microsoft.com/help/4009470) et [Windows 7 SP1 et Windows Server 2008 R2 SP1](https://support.microsoft.com/help/4009469).

| Composant               | Binary         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Stockage                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 – KB4072650                | 6.3.9600.18080 – KB3063109                  | 6.3.9600.18907 – KB4072650         | 10.0.14393.2007 – KB4345418                             | 10.0.15063.850 – KB4345419 | 10.0.16299.371 – KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| Réseau                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Core                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Services Bureau à distance | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Sécurité                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
> [!NOTE]
> Pour éviter un redémarrage accidentel durant le provisionnement d’une machine virtuelle, nous vous recommandons de vous assurer que toutes les installations de Windows Update sont terminées et qu’aucune mise à jour n’est en attente. Pour ce faire, vous pouvez, par exemple, installer toutes les mises à jour Windows disponibles et effectuer un redémarrage avant d’exécuter la commande Sysprep.

### Déterminer quand utiliser Sysprep <a id="step23"></a>    

L’outil de préparation système (Sysprep) est un processus que vous pouvez exécuter pour réinitialiser une installation Windows. Sysprep fournit une expérience « prête à l’emploi » en supprimant toutes les données personnelles et en réinitialisant plusieurs composants. 

En général, vous exécutez Sysprep pour créer un modèle à partir duquel déployer plusieurs autres machines virtuelles ayant une configuration spécifique. Le modèle est appelé *image généralisée*.

Si vous souhaitez créer uniquement une machine virtuelle à partir d’un disque, il est inutile d’utiliser Sysprep. Au lieu de cela, vous pouvez créer la machine virtuelle à partir d’une *image spécialisée*. Pour obtenir des informations sur la création d’une machine virtuelle à partir d’un disque spécialisé, consultez les pages :

- [Créer une machine virtuelle à partir d’un disque spécialisé](create-vm-specialized.md)
- [Créer une machine virtuelle à partir d’un disque dur virtuel spécialisé](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Si vous souhaitez créer une image généralisée, vous devez exécuter Sysprep. Pour plus d’informations, consultez [Utilisation de Sysprep : Une introduction](https://technet.microsoft.com/library/bb457073.aspx). 

Tous les rôles ou toutes les applications installés sur un ordinateur Windows ne prennent pas forcément en charge des images généralisées. Par conséquent, avant d’exécuter cette procédure, vérifiez que Sysprep prend bien en charge le rôle de l’ordinateur. Pour plus d’informations, voir [Prise en charge de Sysprep pour les rôles serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="generalize-a-vhd"></a>Généraliser un disque dur virtuel

>[!NOTE]
> Après avoir exécuté `sysprep.exe` dans les étapes suivantes, arrêtez la machine virtuelle. Ne la réactivez pas avant d’avoir créé une image à partir de celle-ci dans Azure.

1. Connectez-vous à la machine virtuelle Windows.
1. Exécutez une **invite de commandes** en tant qu’administrateur. 
1. Modifier le répertoire en `%windir%\system32\sysprep`. Exécutez ensuite `sysprep.exe`.
1. Dans la boîte de dialogue **Outil de préparation du système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** et vérifiez que la case **Généraliser** est cochée.

    ![Outil de préparation système](media/prepare-for-upload-vhd-image/syspre.png)
1. Dans **Options d’arrêt**, sélectionnez **Arrêter**.
1. Sélectionnez **OK**.
1. Lorsque l’exécution de Sysprep est terminée, arrêtez la machine virtuelle. N’utilisez pas l’option **Redémarrer** pour arrêter la machine virtuelle.

Le disque dur virtuel est maintenant prêt à être chargé. Pour plus d’informations sur la création d’une machine virtuelle à partir d’un disque généralisé, consultez la page [Charger un disque dur virtuel généralisé sur Azure pour créer une machine virtuelle](sa-upload-generalized.md).


>[!NOTE]
> Un fichier *unattend.xml* personnalisé n’est pas pris en charge. Cependant, nous prenons en charge la propriété `additionalUnattendContent`, qui offre seulement une prise en charge limitée pour l’ajout d’options [microsoft-windows-shell-setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) dans le fichier *unattend.xml* utilisé par l’agent d’approvisionnement Azure. Vous pouvez, par exemple, utiliser [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) pour ajouter FirstLogonCommands et LogonCommands. Pour plus d'informations, consultez l’[exemple additionalUnattendContent FirstLogonCommands](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>Remplir les configurations recommandées
Les paramètres suivants n’affectent pas le chargement du disque dur virtuel. Toutefois, nous vous recommandons vivement de les configurer.

* Installez [l’agent Machine virtuelles Azure](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vous pouvez ensuite activer les extensions de machine virtuelle. Les extensions de machine virtuelle mettent en œuvre la plupart des fonctionnalités stratégiques que vous pourriez vouloir utiliser avec vos machines virtuelles. Vous aurez, par exemple, besoin des extensions pour réinitialiser les mots de passe ou configurer le protocole RDP (Remote Desktop Protocol). Pour plus d’informations, consultez l’article [Vue d’ensemble de l’agent de machine virtuelle Azure](../extensions/agent-windows.md).
* Après avoir créé la machine virtuelle dans Azure, nous vous recommandons de placer le fichier d’échange sur le *volume de disque temporaire* pour améliorer les performances. Vous pouvez définir le positionnement du fichier comme suit :

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -Force
   ```
  Si un disque de données est attaché à la machine virtuelle, la lettre de lecteur du volume de disque temporaire est généralement *D*. Cette désignation peut être différente, en fonction de vos paramètres et du nombre de disques disponibles.
  * Nous vous recommandons de désactiver les bloqueurs de scripts fournis par des logiciels antivirus. Ils pourraient interférer avec les scripts de l’agent de provisionnement Windows et en bloquer l’exécution lorsque vous déployez une nouvelle machine virtuelle à partir de votre image.
  
## <a name="next-steps"></a>Étapes suivantes
* [Télécharger une image de machine virtuelle Windows dans Azure pour des déploiements Resource Manager](upload-generalized-managed.md)
* [Résoudre des problèmes liés à l’activation de machines virtuelles Windows Azure](troubleshoot-activation-problems.md)

