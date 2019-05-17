---
title: Installez Office sur une image de disque dur virtuel principale - Azure
description: Comment installer et personnaliser Office sur une image d’aperçu maître bureau virtuel Windows Azure.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/02/2019
ms.author: v-chjenk
ms.openlocfilehash: 92a0ba8c0f43e26b7a1bbe82cc52f61d390fc04d
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827333"
---
# <a name="install-office-on-a-master-vhd-image"></a>Installer Office sur une image VHD principale

Cet article vous indique comment installer Office 365 ProPlus, OneDrive et autres applications sur une image de disque dur virtuel (VHD) maître pour les télécharger dans Azure. Si les utilisateurs doivent accéder à la ligne de certaine applications métier (LOB), nous vous recommandons de que les installer après avoir effectué les instructions fournies dans cet article.

Cet article suppose que vous avez déjà créé une machine virtuelle (VM). Dans le cas contraire, consultez [préparer et personnaliser une image de disque dur virtuel principale](set-up-customize-master-image.md#create-a-vm)

Cet article suppose également que vous avez un accès élevé sur la machine virtuelle, si elle est approvisionnée dans Azure ou le Gestionnaire Hyper-V. Dans le cas contraire, consultez [élever l’accès pour gérer tous les groupes de gestion et d’abonnement Azure](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin).

>[!NOTE]
>Ces instructions concernent une configuration de bureau virtuel Windows Preview spécifique qui peut être utilisée avec des processus existants de votre organisation.

## <a name="install-office-in-shared-computer-activation-mode"></a>Installer Office en mode de l’activation d’ordinateur partagé

Activation de l’ordinateur partagé vous permet de pour déployer Office 365 ProPlus sur un ordinateur de votre organisation qui est accessible par plusieurs utilisateurs. Pour plus d’informations sur l’activation d’ordinateurs partagés, consultez [vue d’ensemble de l’activation d’ordinateurs partagés pour Office 365 ProPlus](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus).

Utilisez le [outil déploiement d’Office](https://www.microsoft.com/download/details.aspx?id=49117) pour installer Office. Sessions multiples de Windows 10 entreprise prend uniquement en charge Office 365 ProPlus.

L’outil de déploiement Office nécessite un fichier XML de configuration. Pour personnaliser l’exemple suivant, consultez le [les Options de Configuration pour l’outil de déploiement Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Cet exemple de configuration XML, nous vous proposons effectuera les opérations suivantes :

- Installer Office à partir du canal d’initiés et fournir des mises à jour à partir du canal initiés lorsqu’elles sont exécutées.
- Utilisez le x64 architecture.
- Désactiver les mises à jour automatiques.
- Installez Visio et le projet.
- Supprimer toutes les installations existantes de Microsoft Office et migrer leurs paramètres.
- Activer l’activation d’ordinateurs partagés.

>[!NOTE]
>Fonctionnalité de recherche de gabarit dans Visio ne fonctionne pas dans un bureau virtuel Windows lors de la configuration de la version préliminaire.

Voici ce que cet exemple de configuration XML ne se passe :

- Installer Skype for Business
- Installez OneDrive en mode par utilisateur. Pour plus d’informations, consultez [OneDrive d’installer dans le mode par ordinateur](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Activation de l’ordinateur partagé peut être configurée via les objets de stratégie de groupe (GPO) ou les paramètres du Registre. L’objet de stratégie de groupe se trouve dans **Configuration ordinateur\\stratégies\\modèles d’administration\\(ordinateur) de Microsoft Office 2016\\paramètres de gestion des licences**

L’outil de déploiement Office contient setup.exe. Pour installer Office, exécutez la commande suivante dans une ligne de commande :

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Exemple de fichier configuration.xml

L’exemple XML suivant installe la version d’initiés.

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

## <a name="install-onedrive-in-per-machine-mode"></a>Installer OneDrive dans le mode par ordinateur

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
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Activer **configurer en mode silencieux de compte d’utilisateur** en exécutant la commande suivante.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Rediriger, puis déplacer Windows connu les dossiers dans OneDrive en exécutant la commande suivante.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Équipes et Skype

Bureau virtuel Windows ne prend pas en charge Skype pour entreprises et les équipes.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez ajouté à Office à l’image, vous pouvez continuer à personnaliser votre image de disque dur virtuel principal. Consultez [préparer et personnaliser une image de disque dur virtuel principale](set-up-customize-master-image.md).
