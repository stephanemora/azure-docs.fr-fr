---
title: Console série de machine virtuelle Azure | Microsoft Docs
description: Console série bidirectionnelle pour machines virtuelles Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: d5d855cac9f09f92798d955dda3d66ab6b631091
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="virtual-machine-serial-console-preview"></a>Console série de machine virtuelle (préversion) 


La console série de machine virtuelle Azure permet aux machines virtuelles Linux et Windows d’accéder à une console texte. Cette connexion série est effectuée via le port série COM1 de la machine virtuelle. Elle fournit l’accès à la machine virtuelle et n’est pas liée au réseau de la machine virtuelle ni à l’état du système d’exploitation. Pour une machine virtuelle, l’accès à la console série n’est possible que via le portail Azure. De plus, seuls les utilisateurs disposant d’un rôle de Contributeur (ou supérieur) pour cette machine virtuelle sont autorisés à accéder à la console. 

> [!Note] 
> Les préversions sont à votre disposition, à condition que vous acceptiez les conditions d’utilisation. Pour plus d’informations, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Actuellement, ce service est disponible en **préversion publique** et l’accès à la console série pour les machines virtuelles est disponible pour toutes les régions Azure. À ce stade, la console série n’est pas disponible pour Azure Government, Azure Allemagne et Azure Chine.

 

## <a name="prerequisites"></a>Prérequis
 

* L’option [Diagnostics de démarrage](boot-diagnostics.md) doit être activée dans la machine virtuelle. 
* Le compte qui utilise la console série doit disposer du [rôle Contributeur](../../role-based-access-control/built-in-roles.md) pour la machine virtuelle et pour le compte de stockage avec [diagnostics de démarrage](boot-diagnostics.md). 

## <a name="open-the-serial-console"></a>Ouvrir la console série
Pour les machines virtuelles, la console série est accessible uniquement via le [portail Azure](https://portal.azure.com). Voici les étapes permettant aux machines virtuelles d’accéder à la console série via le portail : 

  1. Ouvrez le portail Azure
  2. Dans le menu de gauche, sélectionnez Machines virtuelles.
  3. Cliquez sur la machine virtuelle de votre choix dans la liste. La page de présentation de la machine virtuelle s’ouvre.
  4. Faites défiler jusqu’à la section Support + dépannage, puis cliquez sur l’option Console série (en préversion). Un nouveau volet s’ouvre avec la console série, puis démarre la connexion.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>Désactiver la fonctionnalité
La fonctionnalité Console série peut être désactivée pour certaines machines virtuelles, en désactivant le paramètre de diagnostics de démarrage.

## <a name="serial-console-security"></a>Sécurité de la console série 

### <a name="access-security"></a>Sécurité des accès 
L’accès à la console série est limité aux utilisateurs qui disposent du rôle [Contributeur](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) (ou supérieur) pour la machine virtuelle. Si votre locataire AAD nécessite l’authentification MFA, l’accès à la console série nécessite également l’authentification MFA, puisque son accès s’effectue via le [portail Azure](https://portal.azure.com).

### <a name="channel-security"></a>Sécurité des canaux
Toutes les données envoyées sur les canaux sont chiffrées.

### <a name="audit-logs"></a>Journaux d’audit
Tous les accès à la console série sont journalisés dans les journaux [Diagnostics de démarrage](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) de la machine virtuelle. L’accès à ces journaux est détenu et contrôlé par l’administrateur de la machine virtuelle Azure.  

>[!CAUTION] 
Même si les mots de passe d’accès à la console ne sont pas journalisés, si des commandes exécutées dans la console contiennent ou affichent des mots de passe, des secrets, des noms d’utilisateur ou toute autre forme d’informations d’identification personnelle (PII), ces derniers seront écrits dans les journaux Diagnostics de démarrage de la machine virtuelle, avec tout autre texte visible, dans le cadre de l’implémentation de la fonctionnalité de défilement de la console série. Ces journaux sont circulaires et seules les personnes disposant d’autorisations de lecture pour le compte de stockage de diagnostics peuvent y accéder. Toutefois, nous vous recommandons de suivre les bonnes pratiques concernant l’utilisation du Bureau à distance pour tout élément pouvant impliquer des secrets et/ou des informations d’identification personnelle. 

### <a name="concurrent-usage"></a>Utilisation simultanée
Si un utilisateur est connecté à la console série alors qu’un autre utilisateur demande l’accès à la même machine virtuelle, le premier utilisateur est déconnecté pendant que le deuxième utilisateur est connecté. Le premier utilisateur quitte la console pendant que le nouvel utilisateur s’installe, en quelque sorte.

>[!CAUTION] 
Cela signifie donc que l’utilisateur qui laisse sa place n’est pas réellement déconnecté. La possibilité d’appliquer une déconnexion réelle (via SIGHUP ou autre mécanisme similaire) est actuellement étudiée. Pour Windows, un délai d’expiration automatique est activé dans la console SAC. Mais pour Linux, vous pouvez configurer un délai d’expiration terminal. 


## <a name="accessing-serial-console-for-windows"></a>Accès à la console série pour Windows 
La [console SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) est activée par défaut dans les nouvelles images Windows Server sur Azure. La console SAC est prise en charge sur les versions serveur de Windows, mais elle n’est pas disponible sur les versions client (par exemple Windows 10, Windows 8 ou Windows 7). Pour activer la console série dans les machines virtuelles Windows créées à l’aide de l’image Feb2018 ou d’images antérieures, procédez aux étapes suivantes : 

1. Connectez-vous à la machine virtuelle Windows via le Bureau à distance
2. Dans une invite de commandes d’administration, exécutez les commandes suivantes 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Redémarrez le système pour activer la console SAC

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Si nécessaire, la console SAC peut être activée hors connexion. 

1. Sélectionnez le disque Windows pour lequel vous souhaitez configurer la console SAC et attachez-le en tant que disque de données à la machine virtuelle existante. 
2. Dans une invite de commandes d’administration, exécutez les commandes suivantes 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>Comment savoir si la console SAC est activée ? 

Si la [console SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) n’est pas activée, la console série n’affiche pas l’invite pour la console SAC. Elle peut afficher des informations relatives à l’intégrité de la machine virtuelle dans certains cas, ou ne rien afficher.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>Activation du menu de démarrage à afficher dans la console série 

Si vous souhaitez que les invites de commandes du chargeur de démarrage Windows s’affichent dans la console série, vous pouvez définir les options supplémentaires suivantes pour le chargeur de démarrage de Windows.

1. Connectez-vous à la machine virtuelle Windows via le Bureau à distance
2. Dans une invite de commandes d’administration, exécutez les commandes suivantes : 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Redémarrer le système pour activer le menu de démarrage

> [!NOTE] 
> À ce stade, la prise en charge des clés de fonction n’est pas activée. Si vous avez besoin d’options de démarrage avancées, utilisez bcdedit /set {current} onetimeadvancedoptions on. Pour plus d’informations, consultez [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

## <a name="windows-commands---cmd"></a>Commandes Windows - CMD 

Cette section présente des exemples de commandes permettant d’effectuer des tâches courantes dans les situations où vous pouvez avoir besoin d’utiliser la console SAC pour accéder à la machine virtuelle, notamment pour résoudre les échecs de connexion RDP.

La console SAC est incluse dans toutes les versions de Windows depuis Windows Server 2003, mais elle est désactivée par défaut. La console SAC s’appuie sur le pilote du noyau `sacdrv.sys`, le service `Special Administration Console Helper` (`sacsvr`) et le processus `sacsess.exe`. Pour plus d’informations, consultez la page [Emergency Management Services Tools and Settings (Paramètres et outils des services de gestion d’urgence)](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

La console SAC vous permet de vous connecter à votre système d’exploitation en cours d’exécution par le biais du port série. Lorsque vous lancez CMD à partir de la console SAC, `sacsess.exe` lance `cmd.exe` au sein de votre système d’exploitation en cours d’exécution. Dans le Gestionnaire des tâches, vous pouvez constater que si vous établissez une connexion RDP vers votre machine virtuelle simultanément, vous êtes connecté à la console SAC par le biais de la fonctionnalité de console série. L’instance CMD à laquelle vous accédez par le biais de la console SAC est la même `cmd.exe` que vous utilisez lorsque la connexion est établie par le biais du protocole RDP. Les mêmes commandes et les mêmes outils sont disponibles, y compris la possibilité de lancer PowerShell à partir de cette instance CMD. La console SAC diffère de l’environnement de récupération Windows (WinRE) : la console SAC vous permet de gérer votre système d’exploitation en cours d’exécution, tandis que WinRE démarre dans un système d’exploitation minimal. Alors que les machines virtuelles Azure ne permettent pas d’accéder à WinRE, les machines virtuelles Azure peuvent être gérées par le biais de la console SAC avec la fonctionnalité de console série.

La console SAC étant limitée à une mémoire tampon d’écran de 80x24 sans défilement arrière, ajoutez `| more` aux commandes pour afficher la sortie page par page. Utilisez `<spacebar>` pour afficher la page suivante ou `<enter>` pour afficher la ligne suivante.  

`SHIFT+INSERT` est le raccourci de collage pour la fenêtre de console série.

En raison de la taille limitée de la mémoire tampon d’écran de la console SAC, il peut être plus facile de saisir les commandes les plus longues dans un éditeur de texte local, puis de les coller dans la console SAC.

### <a name="view-and-edit-windows-registry-settings"></a>Afficher et modifier les paramètres du Registre Windows
#### <a name="verify-rdp-is-enabled"></a>Vérifier que le protocole RDP est activé
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

La deuxième clé (sous \Policies) existe uniquement si le paramètre de stratégie de groupe approprié est configuré.

#### <a name="enable-rdp"></a>Activer le protocole RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

La deuxième clé (sous \Policies) est nécessaire uniquement si le paramètre de stratégie de groupe approprié a été configuré. La valeur sera réécrite lors de la prochaine actualisation de la stratégie de groupe si ce paramètre est configuré dans la stratégie de groupe.

### <a name="manage-windows-services"></a>Gérer les services Windows

#### <a name="view-service-state"></a>Afficher l’état du service
`sc query termservice`
####  <a name="view-service-logon-account"></a>Afficher le compte de connexion au service
`sc qc termservice`
#### <a name="set-service-logon-account"></a>Paramétrer le compte de connexion au service 
`sc config termservice obj= "NT Authority\NetworkService"`

Un espace est requis après le signe égal.
#### <a name="set-service-start-type"></a>Paramétrer le type de démarrage de service
`sc config termservice start= demand` 

Un espace est requis après le signe égal. Les valeurs possibles de démarrage sont `boot`, `system`, `auto`, `demand`, `disabled` et `delayed-auto`.
#### <a name="set-service-dependencies"></a>Paramétrer les dépendances de services
`sc config termservice depend= RPCSS`

Un espace est requis après le signe égal.
#### <a name="start-service"></a>Démarrer le service
`net start termservice`

or

`sc start termservice`
#### <a name="stop-service"></a>Arrêter le service
`net stop termservice`

or

`sc stop termservice`
### <a name="manage-networking-features"></a>Gérer les fonctionnalités réseau
#### <a name="show-nic-properties"></a>Afficher les propriétés de la carte réseau
`netsh interface show interface` 
#### <a name="show-ip-properties"></a>Afficher les propriétés IP
`netsh interface ip show config`
#### <a name="show-ipsec-configuration"></a>Afficher la configuration IPSec
`netsh nap client show configuration`  
#### <a name="enable-nic"></a>Activer la carte réseau
`netsh interface set interface name="<interface name>" admin=enabled`
#### <a name="set-nic-to-use-dhcp"></a>Paramétrer la carte réseau pour utiliser le protocole DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

Les machines virtuelles Azure doivent toujours être configurées dans le système d’exploitation invité pour que vous puissiez utiliser le protocole DHCP afin d’obtenir une adresse IP. Le paramètre IP statique Azure utilise toujours le protocole DHCP pour fournir l’adresse IP statique à la machine virtuelle.
#### <a name="ping"></a>Ping
`ping 8.8.8.8` 
#### <a name="port-ping"></a>Ping au niveau du port  
Installer le client telnet

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Tester la connectivité

`telnet bing.com 80`

Suppression du client telnet

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Lorsque vous êtes limité aux méthodes disponibles dans Windows par défaut, PowerShell peut constituer une meilleure approche pour tester la connectivité des ports. Consultez la section PowerShell ci-dessous pour obtenir des exemples.
#### <a name="test-dns-name-resolution"></a>Tester la résolution du nom DNS
`nslookup bing.com`
#### <a name="show-windows-firewall-rule"></a>Afficher la règle de Pare-feu Windows
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
#### <a name="disable-windows-firewall"></a>Désactiver le Pare-feu Windows
`netsh advfirewall set allprofiles state off`

Vous pouvez utiliser cette commande lors de la résolution des problèmes pour écarter temporairement le Pare-feu Windows. Il sera activé au prochain redémarrage ou lorsque vous l’activerez à l’aide de la commande ci-dessous. N’arrêtez pas le service de Pare-feu Windows (MPSSVC) ni le service de moteur de filtrage de base (BFE) pour écarter le Pare-feu Windows. L’arrêt des services MPSSVC ou BFE entraîne le blocage de toute la connectivité.
#### <a name="enable-windows-firewall"></a>Activer le Pare-feu Windows
`netsh advfirewall set allprofiles state on`
### <a name="manage-users-and-groups"></a>Gérer les utilisateurs et les groupes
#### <a name="create-local-user-account"></a>Créer un compte d’utilisateur local
`net user /add <username> <password>`
#### <a name="add-local-user-to-local-group"></a>Ajouter un utilisateur local au groupe local
`net localgroup Administrators <username> /add`
#### <a name="verify-user-account-is-enabled"></a>Vérifier que le compte d’utilisateur est activé
`net user <username> | find /i "active"`

Pour les machines virtuelles Azure créées à partir de l’image généralisée, le compte d’administrateur local sera renommé avec le nom spécifié lors de l’approvisionnement de la machine virtuelle. Il ne sera donc généralement pas `Administrator`.
#### <a name="enable-user-account"></a>Activer un compte d’utilisateur
`net user <username> /active:yes`  
#### <a name="view-user-account-properties"></a>Afficher les propriétés du compte d’utilisateur
`net user <username>`

Exemples de lignes d’intérêt provenant d’un compte administrateur local :

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

#### <a name="view-local-groups"></a>Afficher les groupes locaux
`net localgroup`
### <a name="manage-the-windows-event-log"></a>Gérer le journal des événements Windows
#### <a name="query-event-log-errors"></a>Interroger les erreurs du journal des événements
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Remplacez `/c:10` par le nombre souhaité d’événements à retourner, ou supprimez-le pour retourner tous les événements correspondant au filtre.
#### <a name="query-event-log-by-event-id"></a>Interroger le journal des événements en fonction de l’ID d’événement
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>Interroger le journal des événements en fonction de l’ID d’événement et du fournisseur
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Interroger le journal des événements en fonction de l’ID d’événement et du fournisseur sur les 24 dernières heures
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Utilisez `604800000` pour effectuer la recherche sur 7 jours au lieu de 24 heures.
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Interroger le journal des événements en fonction de l’ID d’événement, du fournisseur et d’EventData sur les 7 derniers jours
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>Afficher ou supprimer les applications installées
#### <a name="list-installed-applications"></a>Répertorier les applications installées
`wmic product get Name,InstallDate | sort /r | more`

`sort /r` permet d’effectuer un tri décroissant en fonction de la date d’installation pour vous aider à voir ce qui a été récemment installé. Utilisez `<spacebar>` pour passer à la page suivante de sortie ou `<enter>` pour avancer d’une ligne.
#### <a name="uninstall-an-application"></a>Désinstaller une application
`wmic path win32_product where name="<name>" call uninstall`

Remplacez `<name>` par le nom retourné dans la commande ci-dessus pour l’application que vous souhaitez supprimer.

### <a name="file-system-management"></a>Gestion du système de fichiers
#### <a name="get-file-version"></a>Obtenir la version du fichier
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Cet exemple retourne la version du fichier du pilote de carte réseau virtuel, qui se nomme netvsc.sys, netvsc63.sys ou netvsc60.sys selon la version de Windows.
#### <a name="scan-for-system-file-corruption"></a>Analyser l’endommagement des fichiers système
`sfc /scannow`

Voir aussi [Repair a Windows Image (Réparer une image Windows)](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/repair-a-windows-image).
#### <a name="scan-for-system-file-corruption"></a>Analyser l’endommagement des fichiers système
`dism /online /cleanup-image /scanhealth`

Voir aussi [Repair a Windows Image (Réparer une image Windows)](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/repair-a-windows-image).
#### <a name="export-file-permissions-to-text-file"></a>Exporter les autorisations de fichier vers un fichier texte
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
#### <a name="save-file-permissions-to-acl-file"></a>Enregistrer les autorisations de fichier dans un fichier ACL
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
#### <a name="restore-file-permissions-from-acl-file"></a>Restaurer les autorisations de fichier à partir d’un fichier ACL
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Le chemin d’accès lors de l’utilisation de `/restore` doit être le dossier parent du dossier que vous avez spécifié lors de l’utilisation de `/save`. Dans cet exemple, `\RSA` est le parent du dossier `\MachineKeys` spécifié dans l’exemple `/save` ci-dessus.
#### <a name="take-ntfs-ownership-of-a-folder"></a>S’approprier les fichiers NTFS d’un dossier
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
#### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Accorder des autorisations NTFS pour un dossier de manière récursive
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
### <a name="manage-devices"></a>Gérer des appareils
#### <a name="remove-non-present-pnp-devices"></a>Supprimer les appareils Plug-and-Play non présents
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
### <a name="manage-group-policy"></a>Gérer la stratégie de groupe
#### <a name="force-group-policy-update"></a>Forcer la mise à jour de la stratégie de groupe
`gpupdate /force /wait:-1`
### <a name="miscellaneous-tasks"></a>Tâches diverses
#### <a name="show-os-version"></a>Afficher la version du système d’exploitation
`ver`

or 

`wmic os get caption,version,buildnumber /format:list`

or 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
#### <a name="view-os-install-date"></a>Afficher la date d’installation du système d’exploitation
`systeminfo | find /i "original"`

or 

`wmic os get installdate`
#### <a name="view-last-boot-time"></a>Afficher l’heure du dernier démarrage
`systeminfo | find /i "system boot time"`
#### <a name="view-time-zone"></a>Afficher le fuseau horaire
`systeminfo | find /i "time zone"`

or

`wmic timezone get caption,standardname /format:list`
#### <a name="restart-windows"></a>Redémarrer Windows
`shutdown /r /t 0`

L’ajout de `/f` force la fermeture des applications en cours d’exécution sans avertir l’utilisateur.
#### <a name="detect-safe-mode-boot"></a>Détecter le démarrage en mode sans échec
`bcdedit /enum | find /i "safeboot"` 

## <a name="windows-commands---powershell"></a>Commandes Windows - PowerShell

Pour exécuter PowerShell dans la console SAC, après avoir accédé à une invite de commandes, entrez :

`powershell <enter>`

>[!CAUTION]
Supprimez le module PSReadLine à partir de la session PowerShell avant d’exécuter les autres commandes PowerShell. Il existe un problème connu qui entraîne l’introduction de caractères supplémentaires dans le texte collé à partir du Presse-papiers si le module PSReadLine est en cours d’exécution dans une session PowerShell au sein de la console SAC.

Vérifiez tout d’abord si PSReadLine est chargé. Il est chargé par défaut sur Windows Server 2016, Windows 10 et les versions ultérieures de Windows. Il sera uniquement présent dans les versions antérieures de Windows s’il a été installé manuellement. 

Si cette commande renvoie à une invite sans aucune sortie, cela signifie que le module n’a pas été chargé et vous pouvez continuer à utiliser normalement la session PowerShell dans la console SAC.

`get-module psreadline`

Si la commande ci-dessus retourne la version du module PSReadLine, exécutez la commande suivante pour la décharger. Cette commande ne supprime pas et ne désinstalle pas le module. Elle la décharge uniquement de la session PowerShell active.

`remove-module psreadline`

### <a name="view-and-edit-windows-registry-settings"></a>Afficher et modifier les paramètres du Registre Windows
#### <a name="verify-rdp-is-enabled"></a>Vérifier que le protocole RDP est activé
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

La deuxième clé (sous \Policies) existe uniquement si le paramètre de stratégie de groupe approprié est configuré.
#### <a name="enable-rdp"></a>Activer le protocole RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

La deuxième clé (sous \Policies) est nécessaire uniquement si le paramètre de stratégie de groupe approprié a été configuré. La valeur sera réécrite lors de la prochaine actualisation de la stratégie de groupe si ce paramètre est configuré dans la stratégie de groupe.
### <a name="manage-windows-services"></a>Gérer les services Windows
#### <a name="view-service-details"></a>Afficher les détails sur le service
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

Il est possible d’utiliser `Get-Service`, mais cela n’inclut pas le compte d’ouverture de session du service. `Get-WmiObject win32-service` l’inclut.
#### <a name="set-service-logon-account"></a>Paramétrer le compte de connexion au service
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Lorsque vous utilisez un compte de service autre que `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService` ou `LocalSystem`, spécifiez le mot de passe du compte en tant que dernier argument (huitième) après le nom du compte.
#### <a name="set-service-startup-type"></a>Paramétrer le type de démarrage du service
`set-service termservice -startuptype Manual`

`Set-service` accepte `Automatic`, `Manual` ou `Disabled` comme type de démarrage.
#### <a name="set-service-dependencies"></a>Paramétrer les dépendances de services
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
#### <a name="start-service"></a>Démarrer le service
`start-service termservice`
#### <a name="stop-service"></a>Arrêter le service
`stop-service termservice`
### <a name="manage-networking-features"></a>Gérer les fonctionnalités réseau
#### <a name="show-nic-properties"></a>Afficher les propriétés de la carte réseau
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

or 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` est disponible dans les versions 2012 et supérieures. Pour 2008R2, utilisez `Get-WmiObject`.
#### <a name="show-ip-properties"></a>Afficher les propriétés IP
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
#### <a name="enable-nic"></a>Activer la carte réseau
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

or

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` est disponible dans les versions 2012 et supérieures. Pour 2008R2, utilisez `Get-WmiObject`.
#### <a name="set-nic-to-use-dhcp"></a>Paramétrer la carte réseau pour utiliser le protocole DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` est disponible dans les versions 2012 et supérieures. Pour 2008R2, utilisez `Get-WmiObject`. Les machines virtuelles Azure doivent toujours être configurées dans le système d’exploitation invité pour que vous puissiez utiliser le protocole DHCP afin d’obtenir une adresse IP. Le paramètre IP statique Azure utilise toujours le protocole DHCP pour fournir l’adresse IP à la machine virtuelle.
#### <a name="ping"></a>Ping
`test-netconnection`

or

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection` sans aucun paramètre va tenter d’exécuter une commande ping sur `internetbeacon.msedge.net`. Cette commande est disponible sur les versions 2012 et supérieures. Pour 2008R2, utilisez `Get-WmiObject` comme dans le deuxième exemple.
#### <a name="port-ping"></a>Ping au niveau du port
`test-netconnection -ComputerName bing.com -Port 80`

or

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` est disponible dans les versions 2012 et supérieures. Pour 2008R2, utilisez `Net.Sockets.TcpClient`
#### <a name="test-dns-name-resolution"></a>Tester la résolution du nom DNS
`resolve-dnsname bing.com` 

or 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` est disponible dans les versions 2012 et supérieures. Pour 2008R2, utilisez `System.Net.DNS`.
#### <a name="show-windows-firewall-rule-by-name"></a>Afficher la règle de Pare-feu Windows en fonction du nom
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
#### <a name="show-windows-firewall-rule-by-port"></a>Afficher la règle de Pare-feu Windows en fonction du port
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

or

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` est disponible dans les versions 2012 et supérieures. Pour 2008R2, utilisez l’objet COM `hnetcfg.fwpolicy2`. 
#### <a name="disable-windows-firewall"></a>Désactiver le Pare-feu Windows
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` est disponible dans les versions 2012 et supérieures. Pour 2008R2, utilisez `netsh advfirewall` comme indiqué dans la section CMD ci-dessus.
### <a name="manage-users-and-groups"></a>Gérer les utilisateurs et les groupes
#### <a name="create-local-user-account"></a>Créer un compte d’utilisateur local
`new-localuser <name>`
#### <a name="verify-user-account-is-enabled"></a>Vérifier que le compte d’utilisateur est activé
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

or 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` est disponible dans les versions 2012 et supérieures. Pour 2008R2, utilisez `Get-WmiObject`. Cet exemple montre le compte administrateur local intégré, qui présente toujours le SID `S-1-5-21-*-500`. Pour les machines virtuelles Azure créées à partir de l’image généralisée, le compte d’administrateur local sera renommé avec le nom spécifié lors de l’approvisionnement de la machine virtuelle. Il ne sera donc généralement pas `Administrator`.
#### <a name="add-local-user-to-local-group"></a>Ajouter un utilisateur local au groupe local
`add-localgroupmember -group Administrators -member <username>`
#### <a name="enable-local-user-account"></a>Activer un compte d’utilisateur local
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

Cet exemple active le compte administrateur local intégré, qui présente toujours le SID `S-1-5-21-*-500`. Pour les machines virtuelles Azure créées à partir de l’image généralisée, le compte d’administrateur local sera renommé avec le nom spécifié lors de l’approvisionnement de la machine virtuelle. Il ne sera donc généralement pas `Administrator`.
#### <a name="view-user-account-properties"></a>Afficher les propriétés du compte d’utilisateur
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

or 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` est disponible dans les versions 2012 et supérieures. Pour 2008R2, utilisez `Get-WmiObject`. Cet exemple montre le compte administrateur local intégré, qui présente toujours le SID `S-1-5-21-*-500`.
#### <a name="view-local-groups"></a>Afficher les groupes locaux
`(get-localgroup).name | sort``(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` est disponible dans les versions 2012 et supérieures. Pour 2008R2, utilisez `Get-WmiObject`.
### <a name="manage-the-windows-event-log"></a>Gérer le journal des événements Windows
#### <a name="query-event-log-errors"></a>Interroger les erreurs du journal des événements
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Remplacez `/c:10` par le nombre souhaité d’événements à retourner, ou supprimez-le pour retourner tous les événements correspondant au filtre.
#### <a name="query-event-log-by-event-id"></a>Interroger le journal des événements en fonction de l’ID d’événement
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>Interroger le journal des événements en fonction de l’ID d’événement et du fournisseur
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Interroger le journal des événements en fonction de l’ID d’événement et du fournisseur sur les 24 dernières heures
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Utilisez `604800000` pour effectuer la recherche sur 7 jours au lieu de 24 heures. |
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Interroger le journal des événements en fonction de l’ID d’événement, du fournisseur et d’EventData sur les 7 derniers jours
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>Afficher ou supprimer les applications installées
#### <a name="list-installed-software"></a>Répertorier les logiciels installés
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
#### <a name="uninstall-software"></a>Désinstaller un logiciel
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
### <a name="file-system-management"></a>Gestion du système de fichiers
#### <a name="get-file-version"></a>Obtenir la version du fichier
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Cet exemple retourne la version du fichier du pilote de carte réseau virtuel, qui se nomme netvsc.sys, netvsc63.sys ou netvsc60.sys selon la version de Windows.
#### <a name="download-and-extract-file"></a>Télécharger et extraire le fichier
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Cet exemple crée un dossier `c:\bin`, puis télécharge et extrait la suite d’outils Sysinternals dans `c:\bin`.
### <a name="miscellaneous-tasks"></a>Tâches diverses
#### <a name="show-os-version"></a>Afficher la version du système d’exploitation
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
#### <a name="view-os-install-date"></a>Afficher la date d’installation du système d’exploitation
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
#### <a name="view-last-boot-time"></a>Afficher l’heure du dernier démarrage
`(get-wmiobject win32_operatingsystem).lastbootuptime`
#### <a name="view-windows-uptime"></a>Afficher la durée de fonctionnement dans Windows
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Retourne la durée de fonctionnement sous la forme `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`, par exemple `49:16:48:00.00`. 
#### <a name="restart-windows"></a>Redémarrer Windows
`restart-computer`

L’ajout de `-force` force la fermeture des applications en cours d’exécution sans avertir l’utilisateur.
### <a name="instance-metadata"></a>Métadonnées d’instance

Vous pouvez interroger les métadonnées d’instance Azure à partir de votre machine virtuelle Azure pour afficher des détails tels qu’osType, Location, vmSize, vmId, name, resourceGroupName, subscriptionId, privateIpAddress et publicIpAddress.

L’interrogation des métadonnées d’instance nécessite une connectivité de réseau invité saine, car elle effectue un appel REST par le biais de l’hôte Azure vers le service de métadonnées d’instance. Par conséquent, si vous êtes en mesure d’interroger les métadonnées d’instance, cela vous indique que l’invité peut communiquer sur le réseau vers un service Azure.

Pour plus d’informations, consultez [Service de métadonnées d’instance Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/instance-metadata-service).

#### <a name="instance-metadata"></a>Métadonnées d’instance
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
#### <a name="os-type-instance-metadata"></a>Type de système d’exploitation (métadonnées d’instance)
`$im.Compute.osType`
#### <a name="location-instance-metadata"></a>Emplacement (métadonnées d’instance)
`$im.Compute.Location`
#### <a name="size-instance-metadata"></a>Taille (métadonnées d’instance)
`$im.Compute.vmSize`
#### <a name="vm-id-instance-metadata"></a>ID de la machine virtuelle (métadonnées d’instance)
`$im.Compute.vmId`
#### <a name="vm-name-instance-metadata"></a>Nom de la machine virtuelle (métadonnées d’instance)
`$im.Compute.name`
#### <a name="resource-group-name-instance-metadata"></a>Nom du groupe de ressources (métadonnées d’instance)
`$im.Compute.resourceGroupName`
#### <a name="subscription-id-instance-metadata"></a>ID d’abonnement (métadonnées d’instance)
`$im.Compute.subscriptionId`
#### <a name="tags-instance-metadata"></a>Balises (métadonnées d’instance)
`$im.Compute.tags`
#### <a name="placement-group-id-instance-metadata"></a>ID du groupe de placement (métadonnées d’instance)
`$im.Compute.placementGroupId`
#### <a name="platform-fault-domain-instance-metadata"></a>Domaine d’erreur de plateforme (métadonnées d’instance)
`$im.Compute.platformFaultDomain`
#### <a name="platform-update-domain-instance-metadata"></a>Domaine de mise à jour de plateforme (métadonnées d’instance)
`$im.Compute.platformUpdateDomain`
#### <a name="ipv4-private-ip-address-instance-metadata"></a>Adresse IP privée IPv4 (métadonnées d’instance)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
#### <a name="ipv4-public-ip-address-instance-metadata"></a>Adresse IP publique IPv4 (métadonnées d’instance)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
#### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Adresse de sous-réseau/préfixe IPv4 (métadonnées d’instance)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
#### <a name="ipv6-ip-address-instance-metadata"></a>Adresse IP IPv6 (métadonnées d’instance)
`$im.network.interface.ipv6.ipAddress`
#### <a name="mac-address-instance-metadata"></a>Adresse MAC (métadonnées d’instance)
`$im.network.interface.macAddress`

## <a name="errors"></a>Errors
La plupart des erreurs sont de nature temporaire et peuvent être corrigées par une nouvelle tentative de connexion. Le tableau ci-dessous présente une liste d’erreurs accompagnées de solutions d’atténuation. 

Error                            |   Atténuation 
:---------------------------------|:--------------------------------------------|
Unable to retrieve boot diagnostics settings for '<VMNAME>'. To use the serial console, ensure that boot diagnostics is enabled for this VM (Impossible de récupérer les paramètres de diagnostic de démarrage. Pour utiliser la console série, vérifiez que les diagnostics de démarrage sont activés dans la machine virtuelle) | Vérifiez que l’option [diagnostics de démarrage](boot-diagnostics.md) est activée dans la machine virtuelle. 
The VM is in a stopped deallocated state. Start the VM and retry the serial console connection (La machine virtuelle est arrêtée et à l’état Désalloué. Démarrez la machine virtuelle, puis retentez une connexion à la console série) | La machine virtuelle doit être à l’état Démarré pour accéder à la console série.
You do not have the required permissions to use this VM serial console. Ensure you have at least VM Contributor role permissions (Vous ne disposez pas des autorisations nécessaires pour utiliser la console série sur cette machine virtuelle. Vous devez disposer du rôle Contributeur pour accéder à la console sur cette machine virtuelle)| L’accès à la console série nécessite certaines autorisations. Pour plus d’informations, consultez les [conditions d’accès](#prerequisites).
Unable to determine the resource group for the boot diagnostics storage account '<STORAGEACCOUNTNAME>'. Verify that boot diagnostics is enabled for this VM and you have access to this storage account (Impossible de déterminer le groupe de ressources pour le compte de stockage de diagnostic de démarrage. Vérifiez que les diagnostics de démarrage sont activés sur la machine virtuelle et que vous avez accès au compte de stockage) | L’accès à la console série nécessite certaines autorisations. Pour plus d’informations, consultez les [conditions d’accès](#prerequisites).

## <a name="known-issues"></a>Problèmes connus 
Étant donné qu’il s’agit d’une préversion, l’accès à la console série présente encore certains problèmes, pour lesquels nous fournissons les solutions ci-dessous. 

Problème                           |   Atténuation 
:---------------------------------|:--------------------------------------------|
Aucune option n’est disponible pour la console série de l’instance de groupe de machines virtuelles identiques | À ce stade de la préversion, l’accès à la console série n’est pas pris en charge pour les instances de groupe de machines virtuelles identiques.
L’invite de connexion ne s’affiche pas lorsque vous appuyez sur la touche Entrée après l’affichage de la bannière de connexion | [La touche Entrée n’a aucun effet](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Seules les informations d’intégrité sont affichées lors de la connexion à une machine virtuelle Windows| [Signaux d’intégrité Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
Impossible de saisir des données sur l’invite de commandes de la console SAC si le débogage du noyau est activé | Établissez une connexion RDP vers la machine virtuelle et exécutez `bcdedit /debug {current} off` à partir d’une invite de commandes avec élévation de privilèges. Si vous ne pouvez pas établir de connexion RDP, vous pouvez joindre le disque du système d’exploitation à une autre machine virtuelle Azure et le modifier lorsqu’il est joint en tant que disque de données à l’aide de `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, puis rebasculer le disque.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ) 
**Q. Comment puis-je envoyer des commentaires ?**

R. Pour nous contacter au sujet d’un problème, accédez à la page https://aka.ms/serialconsolefeedback (méthode conseillée). Vous pouvez également envoyer des commentaires sur azserialhelp@microsoft.com ou sous la catégorie Machine virtuelle de la page http://feedback.azure.com. 

**Q. J’obtiens l’erreur indiquant que le type de système d’exploitation (Windows) de la console existante est en conflit avec le type de système d’exploitation demandé (Linux)**

R. Il s’agit d’un problème connu. Pour le résoudre, ouvrez [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) en mode Bash, puis réessayez.

**Q. Je n’arrive pas à accéder à la console série. Où puis-je effectuer une demande de support ?**

R. Cette fonctionnalité est en préversion, et est donc couverte par les Conditions d’utilisation des préversions Azure. La prise en charge de cette fonctionnalité est optimale avec les canaux mentionnés plus haut. 

## <a name="next-steps"></a>Étapes suivantes
* La console série est également disponible pour les machines virtuelles [Linux](../linux/serial-console.md).
* En savoir plus sur les [diagnostics de démarrage](boot-diagnostics.md)
