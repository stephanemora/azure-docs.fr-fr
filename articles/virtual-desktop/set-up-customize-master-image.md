---
title: Préparer et personnaliser une image de disque dur virtuel principale - Azure
description: Comment préparer, personnaliser et charger une image principale de bureau virtuel Windows version préliminaire dans Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/03/2019
ms.author: helohr
ms.openlocfilehash: aff96931f95442c67d08521e72952dd79dad44e2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870219"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Préparer et personnaliser une image de disque dur virtuel principale

Cet article vous indique comment préparer une image de disque dur virtuel (VHD) maître pour les télécharger dans Azure, y compris comment créer des machines virtuelles (VM) et installer et configurer les logiciels sur les. Ces instructions concernent une configuration de bureau virtuel Windows Preview spécifique qui peut être utilisée avec des processus existants de votre organisation.

## <a name="create-a-vm"></a>Créer une machine virtuelle

Sessions multiples de Windows 10 entreprise sont disponible dans la galerie d’images Azure. Il existe deux options pour la personnalisation de cette image.

La première option consiste à configurer une machine virtuelle (VM) dans Azure en suivant les instructions dans [créer une machine virtuelle à partir d’une image managée](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed), puis passez à l’avance à [préparation de logiciels et d’installation](set-up-customize-master-image.md#software-preparation-and-installation).

La deuxième option consiste à créer l’image localement par le téléchargement de l’image, l’approvisionnement d’une machine virtuelle Hyper-V et la personnalisation pour l’adapter à vos besoins, nous aborderons dans la section suivante.

### <a name="local-image-creation"></a>Création d’une image locale

Une fois que vous avez téléchargé l’image vers un emplacement local, ouvrez **Gestionnaire Hyper-V** pour créer une machine virtuelle avec le disque dur virtuel que vous venez de copier. Voici la version simple, mais vous trouverez des instructions plus détaillées dans [créer une machine virtuelle dans Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v).

Pour créer une machine virtuelle avec le disque dur virtuel copié :

1. Ouvrez le **Assistant Nouvel ordinateur virtuel**.

2. Dans la page spécifier la génération, sélectionnez **1E**.

    ![Une capture d’écran de la page spécifier la génération. L’option « Génération 1 » est sélectionnée.](media/a41174fd41302a181e46385e1e701975.png)

3. Sous Type de point de contrôle, désactivez les points de contrôle en décochant la case à cocher.

    ![Une capture d’écran de la section de Type de point de contrôle de la page points de contrôle.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Vous pouvez également exécuter l’applet de commande suivante dans PowerShell pour désactiver les points de contrôle.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Disque fixe

Si vous créez une machine virtuelle à partir d’un disque dur virtuel existant, il crée un disque dynamique par défaut. Il peut être remplacé en sélectionnant un disque fixe **modifier le disque...**  comme indiqué dans l’image suivante. Pour obtenir des instructions, consultez [préparer un disque dur virtuel Windows à charger sur Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

![Une capture d’écran de l’option Modifier le disque.](media/35772414b5a0f81f06f54065561d1414.png)

Vous pouvez également exécuter l’applet de commande PowerShell suivante pour modifier le disque sur un disque fixe.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Installation et préparation de logiciel

Cette section explique comment préparer et d’installer Office 365 ProPlus, OneDrive, FSLogix, Windows Defender et autres applications. Si vos utilisateurs ont besoin d’accéder à certaines applications métier, nous vous recommandons de que les installer à la fin des instructions de cette section.

Cette section part du principe que vous avez un accès élevé sur la machine virtuelle, si elle est approvisionnée dans Azure ou le Gestionnaire Hyper-V.

### <a name="install-office-in-shared-computer-activation-mode"></a>Installer Office en mode de l’activation d’ordinateur partagé

Utilisez le [outil déploiement d’Office](https://www.microsoft.com/download/details.aspx?id=49117) pour installer Office. Sessions multiples de Windows 10 entreprise prend uniquement en charge Office 365 ProPlus, pas les perpétuel Office 2019.

L’outil de déploiement Office nécessite un fichier XML de configuration. Pour personnaliser l’exemple suivant, consultez le [les Options de Configuration pour l’outil de déploiement Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Cet exemple de configuration XML, nous vous proposons effectuera les opérations suivantes :

- Installer Office à partir du canal d’initiés et fournir des mises à jour à partir du canal initiés lorsqu’elles sont exécutées.
- Utilisez le x64 architecture.
- Désactiver les mises à jour automatiques.
- Installez Visio et le projet.
- Supprimer toutes les installations existantes de Microsoft Office et migrer leurs paramètres.
- Activer les ordinateurs partagés des licences pour l’opération dans un environnement de serveur terminal server.

Voici ce que cet exemple de configuration XML ne se passe :

- Installer Skype for Business
- Installez OneDrive en mode par utilisateur. Pour plus d’informations, consultez [OneDrive d’installer dans le mode par ordinateur](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Licence d’ordinateur partagé peut être configuré via les objets de stratégie de groupe (GPO) ou les paramètres du Registre. L’objet de stratégie de groupe se trouve dans **Configuration ordinateur\\stratégies\\modèles d’administration\\(ordinateur) de Microsoft Office 2016\\paramètres de gestion des licences**

L’outil de déploiement Office contient setup.exe. Pour installer Office, exécutez la commande suivante dans une ligne de commande :

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Exemple de fichier configuration.xml

L’exemple XML suivant installe la version Insiders, également appelée initiés rapide ou initiés principal.

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
        <Product ID="O365ProPlusRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Groove" />
            <ExcludeApp ID="Lync" />
            <ExcludeApp ID="OneDrive" />
            <ExcludeApp ID="Teams" />
        </Product>
        <Product ID="VisioProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" /> 
        </Product>
        <Product ID="ProjectProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" />
        </Product>
    </Add>
    <RemoveMSI All="True" />
    <Updates Enabled="FALSE" UpdatePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
    <Display Level="None" AcceptEULA="TRUE" />
    <Logging Level="Verbose" Path="%temp%\WVDOfficeInstall" />
    <Property Value="TRUE" Name="FORCEAPPSHUTDOWN"/>
    <Property Value="1" Name="SharedComputerLicensing"/>
    <Property Value="TRUE" Name="PinIconsToTaskbar"/>
</Configuration>
```

>[!NOTE]
>L’équipe Office recommande d’utiliser installer 64 bits pour le **OfficeClientEdition** paramètre.

Après avoir installé Office, vous pouvez mettre à jour le comportement de Office par défaut. Exécutez les commandes suivantes individuellement ou dans un fichier de commandes pour mettre à jour le comportement.

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

### <a name="disable-automatic-updates"></a>Désactiver les mises à jour automatiques

Pour désactiver les mises à jour automatiques via la stratégie de groupe locale :

1. Ouvrez **éditeur de stratégie de groupe locale\\modèles d’administration\\les composants Windows\\mise à jour Windows**.
2. Avec le bouton droit **configurer la mise à jour automatique** et affectez-lui la valeur **désactivé**.

Vous pouvez également exécuter la commande suivante dans une invite de commande pour désactiver les mises à jour automatiques.

```batch
reg add HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Spécifier la mise en page de démarrage pour les PC Windows 10 (facultatif)

Exécutez cette commande pour spécifier une mise en page de démarrage pour les PC Windows 10.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="install-onedrive-in-per-machine-mode"></a>Installer OneDrive dans le mode par ordinateur

OneDrive est normalement installée par utilisateur. Dans cet environnement, il doit être installé par ordinateur.

Voici comment installer OneDrive dans le mode par ordinateur :

1. Commencez par créer un emplacement pour le programme d’installation de OneDrive à l’étape. Un dossier de disque local ou [\\\\unc] (file://unc) se trouve bien.

2. Téléchargez OneDriveSetup.exe vers votre emplacement intermédiaire avec ce lien : <https://aka.ms/OneDriveWVD-Installer>

3. Si vous avez installé office avec OneDrive en omettant  **\<ExcludeApp ID = « OneDrive » /\>**, désinstaller les installations par utilisateur de OneDrive existantes à partir d’une invite de commandes avec élévation de privilèges en exécutant la commande suivante commande :
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Exécutez cette commande à partir d’une invite de commandes avec élévation de privilèges pour définir le **AllUsersInstall** valeur de Registre :

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Exécutez cette commande pour installer OneDrive dans le mode par ordinateur :

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Exécutez cette commande pour configurer OneDrive pour démarrer lors de la connexion pour tous les utilisateurs :

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Activer **configurer en mode silencieux de compte d’utilisateur** en exécutant la commande suivante.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Rediriger, puis déplacer Windows connu les dossiers dans OneDrive en exécutant la commande suivante.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

### <a name="teams-and-skype"></a>Équipes et Skype

Bureau virtuel Windows ne prend pas en charge le Skype pour entreprises et équipes officiellement.

### <a name="set-up-user-profile-container-fslogix"></a>Configurer le conteneur de profil utilisateur (FSLogix)

Pour inclure le conteneur FSLogix en tant que partie de l’image, suivez les instructions de [configurez un partage de profil utilisateur pour un pool de l’hôte](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). Vous pouvez tester la fonctionnalité du conteneur FSLogix avec [ce démarrage rapide](https://docs.fslogix.com/display/20170529/Profile+Containers+-+Quick+Start).

### <a name="configure-windows-defender"></a>Configurer Windows Defender

Si Windows Defender est configuré dans la machine virtuelle, assurez-vous qu’il a configuré pour pas d’analyse tout le contenu des fichiers VHD et VHDX lors de l’attachement de la même.

Cette configuration supprime l’analyse des fichiers VHD et VHDX au cours de la pièce jointe uniquement, mais n’affecte pas l’analyse en temps réel.

Pour obtenir des instructions pour savoir comment configurer Windows Defender sur Windows Server, consultez [exclusions de configurer un Antivirus Windows Defender sur Windows Server](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus).

Pour en savoir plus sur la façon de configurer Windows Defender pour exclure certains fichiers de l’analyse, consultez [configurer et valider des exclusions basées sur l’emplacement du fichier de l’extension et le dossier](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus).

### <a name="configure-session-timeout-policies"></a>Configurer des stratégies de délai d’expiration de session

Stratégies de session à distance peuvent être appliquées au niveau de stratégie de groupe dans la mesure où toutes les machines virtuelles dans un pool d’hôte font partie du même groupe de sécurité.

Pour configurer des stratégies de session à distance :

1. Accédez à **modèles d’administration** > **les composants Windows** > **des Services Bureau à distance**  >  **Hôte de Session Bureau à distance** > **limitation de durée de Session**.
2. Dans le volet situé à droite, sélectionnez le **définir la limite de temps pour les sessions de Services Bureau à distance mais inactives** stratégie.
3. Une fois que la fenêtre modale s’affiche, modifiez l’option de stratégie à partir de **ne pas configuré** à **activé** pour activer la stratégie.
4. Dans le menu déroulant sous l’option de stratégie, définissez la quantité de temps à **4 heures**.

Vous pouvez également configurer manuellement des stratégies de session à distance en exécutant les commandes suivantes :

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fResetBroken /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxConnectionTime /t REG_DWORD /d 10800000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxDisconnectionTime /t REG_DWORD /d 5000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxIdleTime /t REG_DWORD /d 7200000 /f
```

### <a name="set-up-time-zone-redirection"></a>Configurer la redirection de fuseau horaire

La redirection de fuseau horaire peut être appliquée au niveau de stratégie de groupe dans la mesure où toutes les machines virtuelles dans un pool d’hôte font partie du même groupe de sécurité.

Pour rediriger les fuseaux horaires :

1. Sur le serveur Active Directory, ouvrez le **Group Policy Management Console**.
2. Développez votre domaine et des objets de stratégie de groupe.
3. Cliquez sur le **objet stratégie de groupe** que vous avez créé pour les paramètres de stratégie de groupe, puis sélectionnez **modifier**.
4. Dans le **éditeur de gestion de stratégie de groupe**, accédez à **Configuration ordinateur** > **stratégies** > **administratif Modèles** > **les composants Windows** > **des Services Bureau à distance** > **hôte de Session Bureau à distance**   >  **Appareil et des ressources**.
5. Activer la **autoriser la redirection de fuseau horaire** paramètre.

Vous pouvez également exécuter cette commande sur l’image maître pour rediriger les fuseaux horaires :

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Désactiver la logique de stockage

Pour l’hôte de session de bureau virtuel Windows qui utilisent Windows 10 entreprise ou multisessions Windows 10 entreprise, nous vous recommandons de désactiver la logique de stockage. Vous pouvez désactiver la logique de stockage dans le menu Paramètres sous **stockage**, comme illustré dans la capture d’écran suivante :

![Une capture d’écran du menu de stockage sous paramètres. L’option « Détection de stockage » est désactivée.](media/storagesense.png)

Vous pouvez également modifier le paramètre du Registre en exécutant la commande suivante :

```batch
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Inclure la prise en charge des langues supplémentaires

Cet article n’aborde pas la configuration de langue et support régional. Pour plus d’informations, consultez les articles suivants :

- [Ajouter des langues pour les images Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/add-language-packs-to-windows)
- [Fonctionnalités à la demande](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities)
- [Fonctionnalités de langue et région à la demande (DOM)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-language-fod)

### <a name="other-applications-and-registry-configuration"></a>D’autres applications et la configuration du Registre

Cette section décrit l’application et la configuration du système d’exploitation. Toute la configuration dans cette section est effectuée par le biais des entrées de Registre qui peuvent être exécutées par la ligne de commande et les outils de regedit.

>[!NOTE]
>Vous pouvez implémenter les meilleures pratiques de configuration avec les objets de stratégie général (GPO) ou des importations de Registre. L’administrateur peut choisir une option selon les besoins de leur organisation.

Pour des commentaires hub collection de données de télémétrie de sessions multiples de Windows 10 entreprise, exécutez cette commande :

```batch
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection "AllowTelemetry"=dword:00000003
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection /v AllowTelemetry /d 3
```

Exécutez la commande suivante pour résoudre des incidents de Watson :

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Entrez les commandes suivantes dans l’Éditeur du Registre pour corriger la prise en charge de 5 k résolution. Vous devez exécuter les commandes avant de pouvoir activer la pile côte à côte.

```batch
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp]
"MaxMonitors"=dword:00000004
"MaxXResolution"=dword:00001400
"MaxYResolution"=dword:00000b40

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs]
"MaxMonitors"=dword:00000004
"MaxXResolution"=dword:00001400
"MaxYResolution"=dword:00000b40
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Préparer l’image à télécharger vers Azure

Une fois que vous avez terminé la configuration et toutes les applications installées, suivez les instructions de [préparer un disque dur virtuel Windows à charger sur Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) pour préparer l’image.

Après avoir préparé l’image pour le téléchargement, assurez-vous que la machine virtuelle reste dans un état désactivé ou désalloué.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Télécharger l’image maître à un compte de stockage dans Azure

Cette section s’applique uniquement lorsque l’image principale a été créée localement.

Les instructions suivantes vous indiquera comment charger votre image principale dans un compte de stockage Azure. Si vous ne disposez pas d’un compte de stockage Azure, suivez les instructions de [cet article](https://code.visualstudio.com/tutorials/static-website/create-storage) pour en créer un.

1. Convertissez l’image de machine virtuelle (VHD) sur Fixed si vous n’avez pas déjà. Si vous ne convertissez l’image fixe, vous ne peut pas créer l’image.

2. Téléchargez le disque dur virtuel dans un conteneur d’objets blob dans votre compte de stockage. Vous pouvez télécharger rapidement avec les [outil Explorateur de stockage](https://azure.microsoft.com/features/storage-explorer/). Pour en savoir plus sur l’outil Explorateur de stockage, consultez [cet article](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

    ![Une capture d’écran de la fenêtre de l’outil Microsoft Azure Storage Explorer recherche. La case à cocher « Télécharger les fichiers .vhd ou vhdx en tant qu’objets BLOB de pages (recommandé) » est sélectionné.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Ensuite, accédez au portail Azure dans votre navigateur et de la recherche pour « Images ». Votre recherche doit vous conduire à la **créer une image** page, comme indiqué dans la capture d’écran suivante :

    ![Une capture d’écran de la page d’image créer du portail Azure, rempli avec des exemples de valeurs pour l’image.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Une fois que vous avez créé l’image, vous devez voir une notification, comme celui de la capture d’écran suivante :

    ![Une capture d’écran de la notification de « image créé avec succès ».](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une image, vous pouvez créer ou mettre à jour des pools de l’hôte. Pour en savoir plus sur la création et de mettre à jour des pools d’hôte, consultez les articles suivants :

- [Créer un pool de l’hôte avec un modèle Azure Resource Manager](create-host-pools-arm-template.md)
- [Tutoriel : Créer un pool de l’hôte avec la place de marché Azure](create-host-pools-azure-marketplace.md)
- [Créer un pool de l’hôte avec PowerShell](create-host-pools-powershell.md)
- [Configurez un partage de profil utilisateur pour un pool de l’hôte](create-host-pools-user-profile.md)
- [Configurer la méthode d’équilibrage de charge de bureau virtuel Windows](configure-host-pool-load-balancing.md)
