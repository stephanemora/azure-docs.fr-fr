---
title: Erreur interne en cas de connexion Bureau à distance aux machines virtuelles Azure | Microsoft Docs
description: Découvrez comment résoudre les erreurs internes RDP dans Microsoft Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: 8046e4f42db50db15c840a13b95ae1f3620a8c7f
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918255"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>Erreur interne en cas de connexion à une machine virtuelle Azure avec le Bureau à distance

Cet article décrit une erreur qui peut se produire lors d’une tentative de connexion à une machine virtuelle dans Microsoft Azure.


## <a name="symptoms"></a>Symptômes

Vous ne parvenez pas à vous connecter à une machine virtuelle Azure à l’aide du protocole RDP (Remote Desktop Protocol). Soit la connexion se bloque sur la section « Configuration de la connexion à distance », soit vous recevez le message d’erreur suivant :

- Erreur interne RDP.
- Une erreur interne s'est produite.
- Cet ordinateur ne peut pas se connecter à l’ordinateur distant. Réessayez de vous connecter. Si le problème persiste, contactez le propriétaire de l’ordinateur distant ou votre administrateur réseau.


## <a name="cause"></a>Cause :

Ce problème peut se produire pour les raisons suivantes :

- Les clés de chiffrement RSA locales ne sont pas accessibles.
- Le protocole TLS est désactivé.
- Le certificat est endommagé ou a expiré.

## <a name="solution"></a>Solution

Avant de suivre cette procédure, faites en sauvegarde en prenant un instantané du disque du système d’exploitation de la machine virtuelle affectée. Pour plus d’informations, consultez [Créer un instantané](../windows/snapshot-copy-managed-disk.md).

Pour résoudre ce problème, utilisez la console série ou [réparez la machine virtuelle en mode hors connexion](#repair-the-vm-offline) en attachant le disque du système d’exploitation de la machine virtuelle à une machine virtuelle de récupération.


### <a name="use-serial-control"></a>Utiliser le contrôle série

Connectez-vous à la [console série et ouvrez une instance de PowerShell](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Si la console série n’est pas activée sur votre machine virtuelle, accédez à la section [Réparer la machine virtuelle en mode hors connexion](#repair-the-vm-offline).

#### <a name="step-1-check-the-rdp-port"></a>Étape 1 : Vérifier le port RDP

1. Dans une instance de PowerShell, utilisez [NETSTAT](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
) pour vérifier si le port 8080 est utilisé par d’autres applications :

        Netstat -anob |more
2. Si Termservice.exe utilise le port 8080, passez à l’étape 2. Si un autre service ou une autre application utilise le port 8080, suivez ces étapes :

    1. Arrêtez le service de l’application qui utilise le service 3389 :

            Stop-Service -Name <ServiceName> -Force

    2. Démarrez le service de terminal :

            Start-Service -Name Termservice

2. Si vous ne parvenez pas à arrêter l’application, ou si cette méthode ne s’applique pas à votre situation, modifiez le port pour le protocole RDP :

    1. Modifiez le port :

            Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

            Stop-Service -Name Termservice -Force
            
            Start-Service -Name Termservice 

    2. Définissez le pare-feu du nouveau port :

            Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>

    3. [Mettez à jour le groupe de sécurité réseau du nouveau port](../../virtual-network/security-overview.md) dans le port RDP du Portail Azure.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>Étape 2 : Définir les autorisations nécessaires sur le certificat auto-signé RDP

1.  Dans une instance de PowerShell, exécutez les commandes suivantes une par une pour renouveler le certificat auto-signé RDP :

        Import-Module PKI 
    
        Set-Location Cert:\LocalMachine 
        
        $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 
        
        Remove-Item -Path $RdpCertThumbprint

        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"

2. Si vous ne parvenez pas à renouveler le certificat suivant cette méthode, essayez de renouveler le certificat auto-signé RDP à distance :

    1. Sur une machine virtuelle disposant d’une connectivité à la machine virtuelle qui pose problème, tapez **mmc** dans la zone **Exécuter** pour ouvrir Microsoft Management Console.
    2. Dans le menu **Fichier**, sélectionnez **Ajouter/Supprimer un composant logiciel enfichable**, **Certificats**, puis **Ajouter**.
    3. Sélectionnez **Comptes d’ordinateur**, sélectionnez **Un autre ordinateur**, puis ajoutez l’adresse IP de la machine virtuelle problématique.
    4. Accédez au dossier **Bureau à distance\Certificats**, cliquez avec le bouton droit sur le certificat, puis sélectionnez **Supprimer**.
    5. Dans une instance de PowerShell dans la console série, redémarrez le service de configuration Bureau à distance :

            Stop-Service -Name "SessionEnv"

            Start-Service -Name "SessionEnv"
3. Réinitialisez l’autorisation du dossier MachineKeys.

        remove-module psreadline icacls

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 
        
        takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 
        
        Restart-Service TermService -Force

4. Redémarrez la machine virtuelle, puis essayez de lancer une connexion Bureau à distance à la machine virtuelle. Si l’erreur persiste, passez à l’étape suivante.

#### <a name="step-3-enable-all-supported-tls-versions"></a>Étape 3 : Activer toutes les versions prises en charge de TLS

Le client RDP utilise par défaut le protocole TLS 1.0. Toutefois, il peut être remplacé par TLS 1.1, qui est devenu le nouveau standard. Si TLS 1.1 est désactivé sur la machine virtuelle, la connexion échoue.
1.  Dans une instance CMD, activez le protocole TLS :

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  Pour empêcher la stratégie Active Directory de remplacer les modifications, arrêtez temporairement la mise à jour de la stratégie de groupe :

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  Redémarrez la machine virtuelle pour que les modifications prennent effet. Si le problème est résolu, exécutez la commande suivante pour réactiver la stratégie de groupe :

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    Si la modification est annulée, cela signifie qu’il existe une stratégie Active Directory dans le domaine de votre entreprise. Modifiez cette stratégie pour éviter que ce problème ne se reproduise.

### <a name="repair-the-vm-offline"></a>Réparer la machine virtuelle en mode hors connexion

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Attachez le disque du système d’exploitation à une machine virtuelle de récupération

1. [Attachez le disque du système d’exploitation à une machine virtuelle de récupération](../windows/troubleshoot-recovery-disks-portal.md).
2. Une fois le disque de système d’exploitation attaché à une machine virtuelle de récupération, vérifiez qu’il est marqué **En ligne** dans la console Disk Management. Notez la lettre de lecteur qui est affectée au disque du système d’exploitation attaché.
3. Établissez une connexion Bureau à distance avec la machine virtuelle de récupération.

#### <a name="enable-dump-log-and-serial-console"></a>Activer le journal de vidage et la console série

Pour activer le journal de vidage et la console série, exécutez le script suivant.

1. Ouvrez une session Invite de commande avec élévation de privilèges (**Exécuter en tant qu’administrateur**).
2. Exécutez le script suivant :

    Dans ce script, nous partons du principe que la lettre de lecteur affectée au disque de système d’exploitation attaché est F. Remplacez-la par la valeur correspondant à votre machine virtuelle.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="reset-the-permission-for-machinekeys-folder"></a>Réinitialiser l’autorisation du dossier MachineKeys

1. Ouvrez une session Invite de commande avec élévation de privilèges (**Exécuter en tant qu’administrateur**).
2. Exécutez le script suivant. Dans ce script, nous partons du principe que la lettre de lecteur affectée au disque de système d’exploitation attaché est F. Remplacez-la par la valeur correspondant à votre machine virtuelle.

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>Activer toutes les versions prises en charge de TLS

1.  Ouvrez une session d’invite de commandes avec élévation de privilèges (**Exécuter en tant qu’administrateur**), puis exécutez les commandes suivantes. Le script suivant part du principe que la lettre de lecteur affectée au disque de système d’exploitation attaché est F. Remplacez-la par la valeur correspondant à votre machine virtuelle.
2.  Identifiez quel TLS est activé :

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  Si la clé n’existe pas ou que sa valeur est **0**, activez le protocole en exécutant les scripts suivants :

        REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

4.  Activez NLA :

        REM Enable NLA

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
5.  [Détachez le disque de système d’exploitation et recréez la machine virtuelle](../windows/troubleshoot-recovery-disks-portal.md), puis vérifiez que le problème est résolu.





