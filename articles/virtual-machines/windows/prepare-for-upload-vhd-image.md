---
title: Préparer un disque dur virtuel Windows à charger sur Azure | Microsoft Docs
description: Préparer un disque dur virtuel Windows ou VHDX avant de charger sur Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: 5ae0e7855db6bec9f48d2b9511f0d0626d883111
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65561350"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Préparer un disque dur virtuel Windows à charger sur Azure

Avant de charger une machine virtuelle Windows locale sur Microsoft Azure, vous devez préparer le disque dur virtuel (VHD ou VHDX). Azure prend en charge les machines virtuelles des générations 1 et 2 au format de fichier VHD avec un disque de taille fixe. La taille maximale autorisée pour le disque dur virtuel s’élève à 1 023 Go. Vous pouvez convertir une machine virtuelle génération 1, du système de fichiers VHDX vers un disque VHD, et d’un disque à expansion dynamique à un disque de taille fixe. En revanche, vous ne pouvez pas modifier la génération d’une machine virtuelle. Pour plus d’informations, consultez la page [Dois-je créer une machine virtuelle de génération 1 ou 2 dans Hyper-V ?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) et [Machines virtuelles de génération 2 sur Azure](generation-2.md).

Pour plus d’informations sur la stratégie de prise en charge des machines virtuelles Azure, consultez la page [Prise en charge des logiciels serveur Microsoft pour les machines virtuelles Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!Note]
> Les instructions fournies dans cet article s’appliquent à la version 64 bits de Windows Server 2008 R2 et aux systèmes d’exploitation Windows Server ultérieurs. Pour plus d’informations sur l’exécution de la version 32 bits du système d’exploitation avec Azure, consultez l’article [Prise en charge pour les systèmes d’exploitation 32 bits sur des machines virtuelles Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Convertir le disque virtuel en disque VHD de taille fixe 
Pour convertir votre disque virtuel au format exigé par Azure, utilisez une des méthodes décrites dans cette section. Sauvegardez la machine virtuelle avant d’exécuter le processus de conversion de disque virtuel puis assurez-vous que le disque dur virtuel Windows fonctionne correctement sur le serveur local. Résolvez toute erreur dans la machine virtuelle elle-même avant de tenter une conversion ou un chargement sur Azure.

Après avoir converti le disque, créez une machine virtuelle qui utilise le disque converti. Démarrez et connectez-vous à la machine virtuelle pour terminer la préparation de la machine virtuelle en vue du chargement.

### <a name="convert-disk-using-hyper-v-manager"></a>Convertir un disque à l’aide du Gestionnaire Hyper-V
1. Ouvrez le Gestionnaire Hyper-V et sélectionnez votre ordinateur local sur la gauche. Dans le menu au-dessus de la liste d’ordinateurs, cliquez sur **Action** > **Modifier le disque**.
2. Dans l’écran **Rechercher un disque dur virtuel**, recherchez et sélectionnez le disque virtuel.
3. Dans l’écran **Choisir une action**, sélectionnez **Convertir** puis **Suivant**.
4. Pour convertir un disque VHDX, sélectionnez **VHD**, puis cliquez sur **Suivant**.
5. Pour convertir un disque de taille dynamique, sélectionnez **Taille fixe**, puis cliquez sur **Suivant**.
6. Recherchez et sélectionnez le chemin d’enregistrement du nouveau fichier de disque VHD.
7. Cliquez sur **Terminer**.

>[!NOTE]
>Les commandes de cet article doivent être exécutées dans une session PowerShell avec élévation de privilèges.

### <a name="convert-disk-by-using-powershell"></a>Convertir un disque à l’aide de PowerShell
Vous pouvez convertir un disque virtuel à l’aide de la commande [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) dans Windows PowerShell. Sélectionnez **Exécuter en tant qu’administrateur** lorsque vous démarrez PowerShell. 

L’exemple de commande suivant convertit un disque VHDX en disque VHD et un disque à expansion dynamique en disque à taille fixe :

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
Dans cette commande, remplacez la valeur de « -Path » par le chemin d’accès au disque dur virtuel que vous souhaitez convertir, et la valeur de « -DestinationPath » par le nouveau chemin d’accès et le nom du disque converti.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Convertir à partir du format de disque VMDK VMware
Si vous disposez d’une image de machine virtuelle Windows au [format de fichier VMDK](https://en.wikipedia.org/wiki/VMDK), convertissez-la au format VHD à l’aide de [Microsoft VM Converter](https://www.microsoft.com/download/details.aspx?id=42497). Pour plus d’informations, consultez l’article de blog [How to Convert a VMWare VMDK to Hyper-V VHD](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (en anglais).

## <a name="set-windows-configurations-for-azure"></a>Définir les configurations Windows pour Azure

Sur la machine virtuelle que vous souhaitez charger dans Azure, exécutez toutes les commandes décrites dans les étapes suivantes dans une [fenêtre d’invite de commandes avec élévation de privilèges](https://technet.microsoft.com/library/cc947813.aspx) :

1. Supprimez tout itinéraire statique persistant de la table de routage :
   
   * Pour afficher la table d’itinéraires, exécutez `route print` dans la fenêtre d’invite de commandes.
   * Vérifiez les sections **Persistence Routes** . S’il existe un itinéraire persistant, utilisez la commande **route delete** pour le supprimer.
2. Supprimez le proxy WinHTTP :
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Si la machine virtuelle doit fonctionner avec n’importe quel proxy spécifique, vous devez ajouter une exception de proxy à l’adresse IP Azure ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)), afin que la machine virtuelle dispose d’une connectivité à Azure :
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Définissez la stratégie SAN des disques sur [Onlineall](https://technet.microsoft.com/library/gg252636.aspx) :
   
    ```PowerShell
    diskpart 
    ```
    Dans la fenêtre d’invite de commandes ouverte, entrez les commandes suivantes :

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Réglez l’heure UTC (Temps universel coordonné) pour Windows et définissez le type de démarrage du service de Temps Windows (w32time) sur **automatique**:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Définissez le profil d’alimentation pour de **hautes performances** :

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Vérifiez que les variables d’environnement **TEMP** et **TMP** sont définies avec leurs valeurs par défaut :

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>Vérifier les services Windows
Vérifiez que chacun des services Windows suivants est défini sur les **valeurs par défaut Windows**. Voici le nombre minimal de services qui doivent être configurés pour vous assurer que la machine virtuelle dispose d’une connectivité. Pour réinitialiser les paramètres de démarrage, exécutez les commandes suivantes :
   
```PowerShell
Set-Service -Name bfe -StartupType Automatic
Set-Service -Name dhcp -StartupType Automatic
Set-Service -Name dnscache -StartupType Automatic
Set-Service -Name IKEEXT -StartupType Automatic
Set-Service -Name iphlpsvc -StartupType Automatic
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Automatic
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Automatic
Set-Service -Name RemoteRegistry -StartupType Automatic
```

## <a name="update-remote-desktop-registry-settings"></a>Mettre à jour les paramètres de registre du Bureau à distance
Assurez-vous que les paramètres suivants sont configurés correctement pour la connexion Bureau à distance :

>[!Note] 
>Pendant ces étapes, vous pourriez recevoir un message d’erreur lors de l’exécution de la commande **Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name &lt;nom de l’objet&gt; -value &lt;valeur&gt;** . Ce message d’erreur peut être ignoré en toute sécurité. Il indique simplement que le domaine n’envoie pas cette configuration via un objet de stratégie de groupe.
>
>

1. Le protocole RDP (Remote Desktop Protocol) est activé :
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. Le port RDP est correctement configuré (port 3389 par défaut) :
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    Lorsque vous déployez une machine virtuelle, les règles par défaut sont créés sur le port 3389. Si vous souhaitez modifier le numéro de port, faites-le une fois la machine virtuelle déployée dans Azure.

3. L’écouteur surveille chaque interface réseau :
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. Configurez le mode d’authentification au niveau du réseau pour les connexions RDP :
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -force
     ```

5. Définissez la valeur KeepAlive :
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" -Value 1 -Type DWord -force
    ```
6. Reconnectez-vous :
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" -Value 0 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" -Value 1 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" -Value 0 -Type DWord -force
    ```
7. Limitez le nombre de connexions simultanées :
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" -Value 4294967295 -Type DWord -force
    ```
8. Si des certificats auto-signés sont liés à l’écouteur RDP, supprimez-les :
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    Il s’agit de vous assurer que vous pouvez vous connecter au début du déploiement de la machine virtuelle. Vous pouvez également vérifier ce détail ultérieurement, après le déploiement de la machine virtuelle dans Azure, si nécessaire.

9. Si la machine virtuelle doit faire partie d’un domaine, vérifiez les paramètres suivants pour vous assurer que les anciens paramètres ne sont pas rétablis. Les stratégies qui doivent être vérifiées sont les suivantes :
    
    | Objectif                                     | Stratégie                                                                                                                                                       | Valeur                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | Activer le protocole RDP                           | Configuration de l’ordinateur\Stratégies\Paramètres Windows\Modèles d’administration\Composants\Services Bureau à distance\Hôte de session Bureau à distance\Connexions         | Autoriser les utilisateurs à se connecter à distance avec le Bureau à distance                                  |
    | Stratégie de groupe d’authentification au niveau du réseau                         | Paramètres\Modèles d’administration\Composants\Services Bureau à distance\Hôte de session Bureau à distance\Sécurité                                                    | Demander l’authentification utilisateur au niveau du réseau pour les connexions à distance |
    | Paramètres KeepAlive                      | Configuration de l’ordinateur\Stratégies\Paramètres Windows\Modèles d’administration\Composants Windows\Services Bureau à distance\Hôte de session Bureau à distance\Connexions | Configurer l’intervalle de connexion KeepAlive                                                 |
    | Paramètres de reconnexion                       | Configuration de l’ordinateur\Stratégies\Paramètres Windows\Modèles d’administration\Composants Windows\Services Bureau à distance\Hôte de session Bureau à distance\Connexions | Reconnexion automatique                                                                   |
    | Limiter le nombre de paramètres de connexion | Configuration de l’ordinateur\Stratégies\Paramètres Windows\Modèles d’administration\Composants Windows\Services Bureau à distance\Hôte de session Bureau à distance\Connexions | Limiter le nombre de connexions                                                              |

## <a name="configure-windows-firewall-rules"></a>Configurer les règles du Pare-feu Windows
1. Activez le pare-feu Windows sur les trois profils (domaine, standard et public) :

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Exécutez la commande suivante dans PowerShell pour autoriser WinRM sur les trois profils de pare-feu (domaine, privé et public) et pour activer le service à distance PowerShell :
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Activez les règles de pare-feu suivantes pour autoriser le trafic RDP :

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Activez la règle de partage de fichiers et d’imprimantes afin que la machine virtuelle puisse répondre à une commande ping à l’intérieur du réseau virtuel :

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Si la machine virtuelle doit faire partie d’un domaine, vérifiez les paramètres suivants pour vous assurer que les anciens paramètres ne sont pas rétablis. Les stratégies AD qui doivent être vérifiées sont les suivantes :

    | Objectif                                 | Stratégie                                                                                                                                                  | Valeur                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Activer les profils de pare-feu Windows | Configuration de l’ordinateur\Stratégies\Paramètres Windows\Modèles d’administration\Réseau\Connexion réseau\Pare-feu Windows\Profil de domaine\Pare-feu Windows   | Protéger toutes les connexions réseau         |
    | Activer le protocole RDP                           | Configuration de l’ordinateur\Stratégies\Paramètres Windows\Modèles d’administration\Réseau\Connexion réseau\Pare-feu Windows\Profil de domaine\Pare-feu Windows   | Autoriser les exceptions du Bureau à distance entrantes |
    |                                      | Configuration de l’ordinateur\Stratégies\Paramètres Windows\Modèles d’administration\Réseau\Connexion réseau\Pare-feu Windows\Profil standard\Pare-feu Windows | Autoriser les exceptions du Bureau à distance entrantes |
    | Activer le protocole ICMP-V4                       | Configuration de l’ordinateur\Stratégies\Paramètres Windows\Modèles d’administration\Réseau\Connexion réseau\Pare-feu Windows\Profil de domaine\Pare-feu Windows   | Autoriser les exceptions ICMP                   |
    |                                      | Configuration de l’ordinateur\Stratégies\Paramètres Windows\Modèles d’administration\Réseau\Connexion réseau\Pare-feu Windows\Profil standard\Pare-feu Windows | Autoriser les exceptions ICMP                   |

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Vérifier que la machine virtuelle est saine, sécurisée et accessible via une connexion RDP 
1. Pour vous assurer de l’intégrité et de la cohérence du disque, exécutez une opération de vérification de disque au redémarrage suivant de la machine virtuelle :

    ```PowerShell
    Chkdsk /f
    ```
    Vérifiez que le rapport indique que le disque est propre et sain.

2. Définissez les paramètres Données de configuration de démarrage (BCD). 

    > [!Note]
    > Veillez à exécuter ces commandes dans une fenêtre PowerShell avec élévation de privilèges.
   
   ```powershell
    cmd

    bcdedit /set {bootmgr} integrityservices enable
    bcdedit /set {default} device partition=C:
    bcdedit /set {default} integrityservices enable
    bcdedit /set {default} recoveryenabled Off
    bcdedit /set {default} osdevice partition=C:
    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set {bootmgr} displaybootmenu yes
    bcdedit /set {bootmgr} timeout 5
    bcdedit /set {bootmgr} bootems yes
    bcdedit /ems {current} ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200

    exit
   ```
3. Le journal de vidage peut être utile pour résoudre les problèmes de blocage de Windows. Activez la collecte des journaux de vidage :

    ```powershell
    # Setup the Guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    #Setup the Guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Vérifiez que le référentiel Windows Management Instrumentation est cohérent. Pour ce faire, exécutez la commande suivante :

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Si le référentiel est endommagé, consultez [WMI: Repository Corruption, or Not](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Vérifiez qu’aucune autre application n’utilise le port 3389. Ce port est utilisé pour le service RDP dans Azure. Vous pouvez exécuter la commande **netstat -anob** pour savoir quels ports sont utilisés sur la machine virtuelle :

    ```PowerShell
    netstat -anob
    ```

6. Si le disque dur virtuel Windows à charger est un contrôleur de domaine, suivez ces étapes :

    1. Suivez [ces étapes supplémentaires](https://support.microsoft.com/kb/2904015) pour préparer le disque.

    1. Assurez-vous que vous connaissez le mot de passe DSRM au cas où vous devriez démarrer la machine virtuelle en mode DSRM à un moment donné. Consultez ce lien pour savoir comment définir le [mot de passe DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Assurez-vous que vous connaissez le compte Administrateur intégré et le mot de passe associé. Réinitialisez le mot de passe administrateur local actuel et vérifiez que vous pouvez utiliser ce compte pour vous connecter à Windows par le biais de la connexion RDP. Cette autorisation d’accès est contrôlée par l’objet de stratégie de groupe « Autoriser l’ouverture de session par les services Bureau à distance ». Vous pouvez afficher cet objet dans l’Éditeur d’objets de stratégie de groupe sous :

    Configuration ordinateur\Paramètres Windows\Paramètres de sécurité\Stratégies locales\Attribution des droits utilisateur

8. Vérifiez les stratégies AD suivantes pour vous assurer que votre accès RDP via RDP ou via le réseau n’est pas bloqué :

    - Configuration de l’ordinateur\Paramètres Windows\Paramètres de sécurité\Stratégies locales\Attribution des droits utilisateurs\Refuser l’accès à cet ordinateur depuis le réseau

    - Configuration de l’ordinateur\Paramètres Windows\Paramètres de sécurité\Stratégies locales\Attribution des droits utilisateurs\Refuser la connexion via les services Bureau à distance


9. Vérifiez la stratégie AD suivante pour vous assurer que vous ne supprimez pas un des comptes d’accès nécessaires suivants :

   - Configuration de l’ordinateur\Paramètres Windows\Paramètres de sécurité\Stratégies locales\Attribution des droits utilisateurs\Accéder à cet ordinateur à partir du réseau

     Les groupes suivants doivent être répertoriés sur cette stratégie :

   - Administrateurs
   - Opérateurs de sauvegarde
   - Tout le monde
   - Utilisateurs

10. Redémarrez la machine virtuelle pour vous assurer que Windows est toujours sain et qu’il est accessible par le biais de la connexion RDP. À ce stade, il peut être judicieux de créer une machine virtuelle dans votre Hyper-V local afin de vous assurer qu’elle démarre complètement et de vérifier si elle est accessible via le protocole RDP.

11. Supprimez tous les filtres de TDI (Transport Driver Interface) supplémentaires, tels que les logiciels qui analysent les paquets TCP ou les pare-feu supplémentaires. Vous pouvez également vérifier ce détail ultérieurement, après le déploiement de la machine virtuelle dans Azure, si nécessaire.

12. Désinstallez tous les autres logiciels et pilotes tiers liés aux composants physiques ou toute autre technologie de virtualisation.

### <a name="install-windows-updates"></a>Installer les mises à jour Windows
Pour une configuration idéale, **le niveau de correctif logiciel le plus récent doit être installé sur la machine**. Si ce n’est pas possible, vérifiez que les mises à jour suivantes sont installées :

| Composant               | Binary         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 version 2016, Windows Server 1607 version 1607 | Windows 10 version 1703    | Windows 10 1709, Windows Server 2016 version 1709 | Windows 10 1803, Windows Server 2016 version 1803 |
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
| Principal                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
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
       
### Quand utiliser sysprep <a id="step23"></a>    

Sysprep est un processus que vous pouvez exécuter dans une installation Windows. Il réinitialise l’installation du système pour une « expérience prête à l’emploi » en supprimant toutes les données personnelles et en réinitialisant plusieurs composants. En général, ce processus est utile si vous souhaitez créer un modèle à partir duquel déployer plusieurs autres machines virtuelles ayant une configuration spécifique. On appelle cela une **image généralisée**.

Si, au lieu de cela, vous souhaitez uniquement créer une machine virtuelle à partir d’un disque, il est inutile d’utiliser sysprep. Dans ce cas, vous pouvez simplement créer la machine virtuelle à partir de ce que l’on appelle une **image spécialisée**.

Pour plus d’informations sur la création d’une machine virtuelle à partir d’un disque spécialisé, consultez les pages :

- [Créer une machine virtuelle à partir d’un disque spécialisé](create-vm-specialized.md)
- [Créer une machine virtuelle à partir d’un disque dur virtuel spécialisé](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Si vous souhaitez créer une image généralisée, vous devez exécuter sysprep. Pour plus d’informations sur Sysprep, consultez [Introduction à l’utilisation de Sysprep](https://technet.microsoft.com/library/bb457073.aspx). 

Tous les rôles ou toutes les applications installés sur un ordinateur Windows ne prennent pas forcément en charge cette généralisation. Avant d’exécuter cette procédure, consultez donc l’article suivant pour vous assurer que le rôle de l’ordinateur est pris en charge par sysprep. Pour plus d’informations, consultez la page [Prise en charge de Sysprep pour les rôles serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="steps-to-generalize-a-vhd"></a>Étapes de généralisation d’un disque dur virtuel

>[!NOTE]
> Après l’exécution de sysprep.exe comme indiqué dans les étapes suivantes, arrêtez la machine virtuelle et ne la redémarrez pas avant d’avoir créé une image de cette dernière dans Azure.

1. Connectez-vous à la machine virtuelle Windows.
2. Exécutez une **invite de commandes** en tant qu’administrateur. 
3. Remplacez le répertoire par : **%windir%\system32\sysprep**, puis exécutez **sysprep.exe**.
3. Dans la boîte de dialogue **Outil de préparation du système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** et vérifiez que la case **Généraliser** est cochée.

    ![Outil de préparation système](media/prepare-for-upload-vhd-image/syspre.png)
4. Dans **Options d’arrêt**, sélectionnez **Arrêter**.
5. Cliquez sur **OK**.
6. Lorsque l’exécution de Sysprep est terminée, arrêtez la machine virtuelle. N’utilisez pas l’option **Redémarrer** pour arrêter la machine virtuelle.
7. Le disque dur virtuel est maintenant prêt à être chargé. Pour plus d’informations sur la création d’une machine virtuelle à partir d’un disque généralisé, consultez la page [Charger un disque dur virtuel généralisé sur Azure pour créer une machine virtuelle](sa-upload-generalized.md).


>[!NOTE]
> Un fichier unattend.xml personnalisé n’est pas pris en charge. Bien que nous prenions en charge la propriété additionalUnattendContent property, ceci offre seulement une prise en charge limitée pour l’ajout d’options [microsoft-windows-shell-setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) dans le fichier unattend.xml que l’agent d’approvisionnement utilise. Par exemple,  ils peuvent utiliser [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) pour ajouter FirstLogonCommands et LogonCommands. Voir aussi l’[exemple additionalUnattendContent FirstLogonCommands](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-recommended-configurations"></a>Remplir les configurations recommandées
Les paramètres suivants n’affectent pas le chargement du disque dur virtuel. Toutefois, nous vous recommandons vivement de les configurer.

* Installez [l’agent de machine virtuelle Azure](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vous pouvez ensuite activer les extensions de machine virtuelle. Les extensions de machine virtuelle mettent en œuvre la plupart des fonctionnalités stratégiques que vous pourriez vouloir utiliser avec vos machines virtuelles, telles que la réinitialisation des mots de passe, la configuration de RDP, etc. Pour plus d’informations, consultez l’article [Vue d’ensemble de l’agent de machine virtuelle Azure](../extensions/agent-windows.md).
* Une fois la machine virtuelle créée dans Azure, nous vous recommandons de placer le fichier d’échange sur le volume de « disque temporaire » pour améliorer les performances. Pour ce faire :

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -force
   ```
  S’il existe un disque de données attaché à la machine virtuelle, la lettre de lecteur du volume de disque temporaire est généralement « D ». Cette désignation peut être différente, en fonction du nombre de disques disponibles et des paramètres que vous définissez.

## <a name="next-steps"></a>Étapes suivantes
* [Télécharger une image de machine virtuelle Windows dans Azure pour des déploiements Resource Manager](upload-generalized-managed.md)
* [Résoudre des problèmes liés à l’activation de machines virtuelles Windows Azure](troubleshoot-activation-problems.md)

