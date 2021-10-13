---
title: Installer des modules linguistiques sur des machines virtuelles Windows 11 Entreprise dans Azure Virtual Desktop – Azure
description: Comment installer des modules linguistiques pour les machines virtuelles Windows 11 Entreprise dans Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 10/04/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 365d2821ce0fec8fdf901ad4dc663e8325267803
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129536629"
---
# <a name="add-languages-to-a-windows-11-enterprise-image"></a>Ajouter des langues à une image Windows 11 Entreprise

Il est important de s’assurer que les utilisateurs de votre organisation dans le monde entier peuvent utiliser votre déploiement Azure Virtual Desktop. C’est la raison pour laquelle vous pouvez personnaliser l’image Windows 11 Entreprise que vous utilisez pour vos machines virtuelles afin d’avoir des modules linguistiques différents. À compter de Windows 11, les comptes d’utilisateur non-administrateurs peuvent désormais ajouter la langue d’affichage et les fonctionnalités de langue correspondantes. Cette fonctionnalité signifie que vous n’aurez pas besoin de préinstaller des modules linguistiques pour les utilisateurs d’un pool d’hôtes personnel. Pour les pools d’hôtes mis en pool, nous vous recommandons quand même d’ajouter les langues que vous prévoyez d’ajouter à une image personnalisée. Vous pouvez utiliser les instructions de cet article pour les versions à session unique et à plusieurs sessions de Windows 11 Entreprise.

Lorsque votre organisation comprend des utilisateurs avec plusieurs langues différentes, vous avez deux options :

- Créer un pool d’hôtes dédié avec une image personnalisée par langue.
- Avoir plusieurs utilisateurs avec différentes langues dans le même pool d’hôtes.

La deuxième option est plus efficace en termes de ressources et de coût, mais nécessite quelques étapes supplémentaires. Heureusement, cet article vous aidera à créer une image qui peut s’adapter aux utilisateurs de toutes les langues et à tous les besoins en matière de localisation.

## <a name="requirements"></a>Spécifications

Avant de pouvoir ajouter des langues à une machine virtuelle Windows 11 Entreprise, vous devez disposer des éléments suivants :

-   Une machine virtuelle Azure avec Windows 11 Entreprise installé
-   Un ISO de langue et fonctionnalités facultatives (LoF). Vous pouvez télécharger l’ISO sur [ISO de langue et fonctionnalités facultatives LoF Windows 11](https://software-download.microsoft.com/download/sg/22000.1.210604-1628.co_release_amd64fre_CLIENT_LOF_PACKAGES_OEM.iso)
-   Un partage Azure Files ou un partage de fichiers sur une machine virtuelle de serveur de fichiers Windows

>[!NOTE]
>Le partage de fichiers (référentiel) doit être accessible à partir de la machine virtuelle Azure que vous allez utiliser pour créer l’image personnalisée.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Créer un référentiel de contenu pour les modules linguistiques et les fonctionnalités à la demande

Pour créer le référentiel de contenu que vous allez utiliser pour ajouter des langues et des fonctionnalités à votre machine virtuelle :

1. Ouvrez la machine virtuelle à laquelle vous souhaitez ajouter des langues dans Azure.

2. Ouvrez et montez le fichier ISO que vous avez téléchargé en fonction de la [Configuration requise](#requirements) sur la machine virtuelle.

3. Créez un dossier sur le partage de fichiers.

4. Copiez tout le contenu du dossier **LanguagesAndOptionalFeatures** de l’ISO vers le dossier que vous avez créé.

     >[!NOTE]
     > Si vous utilisez un stockage limité, vous pouvez utiliser le fichier ISO monté « Langues et fonctionnalités facultatives » en tant que référentiel. Pour savoir comment créer un référentiel, consultez [Créer un référentiel personnalisé FOD et de packs linguistiques](/windows-hardware/manufacture/desktop/languages-overview#build-a-custom-fod-and-language-pack-repository).

     >[!IMPORTANT]
     > Certaines langues requièrent des polices supplémentaires incluses dans les packages satellites qui suivent différentes conventions d’affectation de noms. Par exemple, les noms de fichiers de police japonais comprennent « Jpan ».
     >
     > [!div class="mx-imgBorder"]
     > ![Exemple de modules linguistiques japonais avec la balise de langue « Jpan » dans leurs noms de fichiers.](media/language-pack-example.png)

5. Définissez les autorisations sur le partage de contenu de la langue afin d’avoir un accès en lecture à partir de la machine virtuelle que vous allez utiliser pour créer l’image personnalisée.

## <a name="create-a-custom-windows-11-enterprise-image-manually"></a>Créer manuellement une image Windows 11 Entreprise personnalisée

Vous pouvez créer une image personnalisée en suivant ces étapes :

1. Déployez une machine virtuelle Azure, puis accédez à la galerie Azure et sélectionnez la version actuelle de Windows 11 Entreprise que vous utilisez.

2. Après avoir déployé la machine virtuelle, connectez-vous à l’aide du protocole RDP en tant qu’administrateur local.

3. Connectez-vous au référentiel de partage de fichiers que vous avez créé dans [Créer un référentiel de contenu pour les modules linguistiques et les fonctionnalités à la demande](#create-a-content-repository-for-language-packages-and-features-on-demand), puis montez-le sur un lecteur à lettre (par exemple, le lecteur E).

4. Exécutez le script PowerShell suivant à partir d’une session PowerShell avec élévation de privilèges pour installer les modules linguistiques et les packages satellites sur Windows 11 Entreprise :
   
   ```powershell
   ########################################################
   ## Add Languages to running Windows Image for Capture##
   ########################################################
   ##Disable Language Pack Cleanup##
   Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"
   Disable-ScheduledTask -TaskPath "\Microsoft\Windows\MUI\" -TaskName "LPRemove"
   Disable-ScheduledTask -TaskPath "\Microsoft\Windows\LanguageComponentsInstaller" -TaskName "Uninstallation"
   reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Control Panel\International" /v "BlockCleanupOfUnusedPreinstalledLangPacks" /t REG_DWORD /d 1 /f

   ##Set Language Pack Content Stores##
   $LIPContent = "E:"

   ##Set Path of CSV File##
   $CSVFile = "Windows-10-1809-FOD-to-LP-Mapping-Table.csv"
   $filePath = (Get-Location).Path + "/$CSVFile"

   ##Import Necesarry CSV File##
   $FODList = Import-Csv -Path $filePath -Delimiter ";"

   ##Set Language (Target)##
   $targetLanguage = "es-es"

   $sourceLanguage = (($FODList | Where-Object {$_.'Target Lang' -eq $targetLanguage}) | Where-Object {$_.'Source Lang' -ne $targetLanguage} | Select-Object -Property 'Source Lang' -Unique).'Source Lang'
   if(!($sourceLanguage)){
       $sourceLanguage = $targetLanguage
   }

   $langGroup = (($FODList | Where-Object {$_.'Target Lang' -eq $targetLanguage}) | Where-Object {$_.'Lang Group:' -ne ""} | Select-Object -Property 'Lang Group:' -Unique).'Lang Group:'

   ##List of additional features to be installed##
   $additionalFODList = @(
       "$LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~~.cab", 
       "$LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~$sourceLanguage~.cab",
       "$LIPContent\Microsoft-Windows-SnippingTool-FoD-Package~31bf3856ad364e35~amd64~$sourceLanguage~.cab",
       "$LIPContent\Microsoft-Windows-Lip-Language_x64_$sourceLanguage.cab" ##only if applicable##
   )
   
   $additionalCapabilityList = @(
    "Language.Basic~~~$sourceLanguage~0.0.1.0",
    "Language.Handwriting~~~$sourceLanguage~0.0.1.0",
    "Language.OCR~~~$sourceLanguage~0.0.1.0",
    "Language.Speech~~~$sourceLanguage~0.0.1.0",
    "Language.TextToSpeech~~~$sourceLanguage~0.0.1.0"
    )

    ##Install all FODs or fonts from the CSV file###
    Dism /Online /Add-Package /PackagePath:$LIPContent\Microsoft-Windows-Client-Language-Pack_x64_$sourceLanguage.cab
    Dism /Online /Add-Package /PackagePath:$LIPContent\Microsoft-Windows-Lip-Language-Pack_x64_$sourceLanguage.cab
    foreach($capability in $additionalCapabilityList){
       Dism /Online /Add-Capability /CapabilityName:$capability /Source:$LIPContent
    }

    foreach($feature in $additionalFODList){
    Dism /Online /Add-Package /PackagePath:$feature
    }

    if($langGroup){
    Dism /Online /Add-Capability /CapabilityName:Language.Fonts.$langGroup~~~und-$langGroup~0.0.1.0 
    }

    ##Add installed language to language list##
    $LanguageList = Get-WinUserLanguageList
    $LanguageList.Add("$targetlanguage")
    Set-WinUserLanguageList $LanguageList -force
    ```

    >[!NOTE]
    >Cet exemple de script utilise le code de langue Espagnol (es-es). Pour installer automatiquement les fichiers appropriés pour une langue différente, remplacez le paramètre *$targetLanguage* par le code de langue approprié. Pour une liste des codes de langue, voir [Packages linguistiques disponibles pour Windows](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

    Le script peut prendre un certain temps avant de se terminer en fonction du nombre de langues à installer. Vous pouvez également installer des langues supplémentaires après l’installation initiale en exécutant à nouveau le script avec un paramètre *$targetLanguage* différent.

5. Pour sélectionner automatiquement les fichiers d’installation appropriés, téléchargez et enregistrez la [Table de langues et fonctionnalités à la demande Windows 10 1809](https://download.microsoft.com/download/7/6/0/7600F9DC-C296-4CF8-B92A-2D85BAFBD5D2/Windows-10-1809-FOD-to-LP-Mapping-Table.xlsx ) en tant que fichier CSV, puis enregistrez-la dans le même dossier que votre script PowerShell.

6. Une fois l’exécution du script terminée, assurez-vous que les modules linguistiques sont correctement installés en accédant à **Démarrer** > **Paramètres** > **Heure et langue** > **Langue**. Si les fichiers de langue sont présents, cela signifie qu’ils sont bien installés.

7. Enfin, si la machine virtuelle est connectée à Internet lors de l’installation des langues, vous devez exécuter un processus de nettoyage pour supprimer les packs d’expérience linguistique inutiles. Pour nettoyer les fichiers, exécutez les commandes suivantes :

    ```powershell
    ##Cleanup to prepare sysprep##
    Remove-AppxPackage -Package Microsoft.LanguageExperiencePackes-ES_22000.8.13.0_neutral__8wekyb3d8bbwe

    Remove-AppxPackage -Package Microsoft.OneDriveSync_22000.8.13.0_neutral__8wekyb3d8bbwe
    ```

    Pour nettoyer d’autres modules linguistiques, remplacez « es-ES » par un code de langue différent.

8. Une fois que vous avez terminé le nettoyage, déconnectez le partage. 

## <a name="finish-customizing-your-image"></a>Finaliser la personnalisation de votre image

Après avoir installé les modules linguistiques, vous pouvez ajouter d’autres logiciels à votre image personnalisée.

Une fois votre image personnalisée, vous devez exécuter l’outil de préparation du système (sysprep).

Pour exécuter sysprep :

1. Ouvrez une invite de commandes avec élévation de privilèges et exécutez la commande suivante pour généraliser l’image :  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Si vous rencontrez des problèmes, consultez le fichier **SetupErr.log** sur votre lecteur C dans **Windows** > **System32** > **Sysprep** > **Panther**. Après cela, suivez les instructions de [Sysprep échoue avec les applications du Microsoft Store](/troubleshoot/windows-client/deployment/sysprep-fails-remove-or-update-store-apps) pour résoudre les problèmes liés à votre installation.

3. Si la configuration a réussi, arrêtez la machine virtuelle, puis capturez-la dans une image managée en suivant les instructions fournies dans [Créer une image managée d’une machine virtuelle généralisée dans Azure](../virtual-machines/windows/capture-image-resource.md).

4. Vous pouvez maintenant utiliser l’image personnalisée pour déployer un pool d’hôtes Azure Virtual Desktop. Pour savoir comment déployer un pool d’hôtes, consultez [Tutoriel : Créer un pool d’hôtes avec le portail Azure](create-host-pools-azure-marketplace.md).

>[!NOTE]
>Lorsqu'un utilisateur change sa langue d'affichage, il doit se déconnecter de sa session Azure Virtual Desktop, puis se reconnecter. Il doit se déconnecter à partir du menu Démarrer.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment installer des packages de langue pour des machines virtuelles à multisession Windows 10 dans [Ajouter des modules linguistiques à une image Windows 10 multisession](language-packs.md).

Pour obtenir la liste des problèmes connus, consultez [Ajout de langues dans Windows 10 : problèmes connus](/windows-hardware/manufacture/desktop/language-packs-known-issue).
