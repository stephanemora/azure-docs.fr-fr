---
title: Configurer des scripts PowerShell pour l’attachement d’application MSIX Windows Virtual Desktop - Azure
description: Comment créer des scripts PowerShell pour l’attachement d’application MSIX Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5e45c51735e0b7ab4b263d3f3047b5848c82439d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98185765"
---
# <a name="create-powershell-scripts-for-msix-app-attach-preview"></a>Créer des scripts PowerShell pour l’attachement de l’application MSIX (préversion)

> [!IMPORTANT]
> L’application MSIX est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service, c’est pourquoi nous déconseillons son utilisation pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article vous guide tout au long de la configuration des scripts PowerShell pour l’attachement d’application MSIX.

>[!IMPORTANT]
>Avant de commencer, veillez à renseigner et à envoyer [ce formulaire](https://aka.ms/enablemsixappattach) pour activer l’attachement d’application MSIX dans votre abonnement. Si votre demande n’est pas approuvée, l’attachement d’application MSIX ne fonctionnera pas. L’approbation des demandes peut prendre jusqu’à 24 heures pendant les jours ouvrables. Vous recevrez un e-mail lorsque votre demande aura été acceptée et traitée.

## <a name="install-certificates"></a>Installer des certificats

Vous devez installer des certificats sur tous les hôtes de session dans le pool hôte qui hébergera les points d’accès à partir des packages d’attachement d’application MSIX.

Si votre application utilise un certificat qui n’est pas approuvé publiquement ou qui a été auto-signé, procédez comme suit pour l’installer :

1. Cliquez avec le bouton droit sur le package et sélectionnez **Propriétés**.
2. Dans la fenêtre qui s’affiche, sélectionnez l’onglet **Signatures numériques**. Il ne doit y avoir qu’un seul élément dans la liste de l’onglet, comme illustré dans l’image suivante. Sélectionnez cet élément pour le mettre en surbrillance, puis sélectionnez **Détails**.
3. Lorsque la fenêtre Détails de la signature numérique s’affiche, sélectionnez l’onglet **Général**, **Afficher le certificat**, puis **Installer le certificat**.
4. Quand le programme d’installation s’ouvre, sélectionnez **Machine locale** comme emplacement de stockage, puis sélectionnez **Suivant**.
5. Si le programme d’installation vous demande si vous souhaitez autoriser l’application à apporter des modifications à votre appareil, sélectionnez **Oui**.
6. Sélectionnez **Placer tous les certificats dans le magasin suivant**, puis sélectionnez **Parcourir**.
7. Lorsque la fenêtre Sélectionner un magasin de certificats s’affiche, sélectionnez **Personnes autorisées**, puis sélectionnez **OK**.
8. Sélectionnez **Suivant** et **Terminer**.

## <a name="enable-microsoft-hyper-v"></a>Activation de Microsoft Hyper-V

Microsoft Hyper-V doit être activé, car la commande `Mount-VHD` est nécessaire pour l’activation et la commande `Dismount-VHD` pour la désactivation.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>Cette modification va nécessiter le redémarrage de la machine virtuelle.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Préparer les scripts PowerShell pour l’attachement de l’application MSIX

L’attachement d’application MSIX comprend quatre phases distinctes qui doivent être exécutées dans l’ordre suivant :

1. Étape
2. Inscrire
3. Désinscrire
4. Retrait

Chaque phase crée un script PowerShell. Des exemples de scripts pour chaque phase sont disponibles [ici](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-powershell-script"></a>Indexer un script PowerShell

Avant de mettre à jour les scripts PowerShell, vérifiez que vous disposez du GUID de volume dans le disque dur virtuel. Pour récupérer le GUID du volume :

1.  Ouvrez le partage réseau où se trouve le disque dur virtuel à l’intérieur de la machine virtuelle où vous allez exécuter le script.

2.  Cliquez avec le bouton droit sur le disque dur virtuel, puis sélectionnez **Monter**. Le disque dur virtuel est alors monté sur une lettre de lecteur.

3.  Une fois le disque dur virtuel monté, la fenêtre de l’**Explorateur de fichiers** s’ouvre. Capturer le dossier parent et mettre à jour la variable **$parentFolder**

    >[!NOTE]
    >Si vous ne voyez pas de dossier parent, cela signifie que le MSIX n’a pas été développé correctement. Répétez la section précédente et réessayez.

4.  Ouvrez le dossier parent. S’il est correctement développé, vous verrez un dossier portant le même nom que le package. Mettez à jour la variable **$packageName** pour qu'elle corresponde au nom de ce dossier.

    Par exemple : `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Ouvrez une invite de commandes et entrez **mountvol**. Cette commande affiche une liste de volumes et leurs GUID. Copiez le GUID du volume sur lequel la lettre de lecteur correspond au lecteur sur lequel vous avez monté votre disque dur virtuel à l’étape 2.

    Par exemple, dans cet exemple de sortie pour la commande mountvol, si vous avez monté votre disque dur virtuel sur le lecteur C, vous souhaiterez copier la valeur au-dessus de `C:\` :

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Mettez à jour la variable **$volumeGuid** avec le GUID de volume que vous venez de copier.

7. Ouvrez une invite admin PowerShell et mettez à jour le script PowerShell suivant avec les variables qui s’appliquent à votre environnement.

    ```powershell
    #MSIX app attach staging sample

    #region variables
    $vhdSrc="<path to vhd>"
    $packageName = "<package name>"
    $parentFolder = "<package parent folder>"
    $parentFolder = "\" + $parentFolder + "\"
    $volumeGuid = "<vol guid>"
    $msixJunction = "C:\temp\AppAttach\"
    #endregion

    #region mountvhd
    try
    {
          Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly
          Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green
    }
    catch
    {
          Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red
    }
    #endregion

    #region makelink
    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"
    if (!(Test-Path $msixJunction))
    {
         md $msixJunction
    }

    $msixJunction = $msixJunction + $packageName
    cmd.exe /c mklink /j $msixJunction $msixDest
    #endregion

    #region stage
    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
    Add-Type -AssemblyName System.Runtime.WindowsRuntime
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]
    $asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])
    $packageManager = [Windows.Management.Deployment.PackageManager]::new()
    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd
    $path = ([System.Uri]$path).AbsoluteUri
    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")
    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))
    $task
    #endregion
    ```

### <a name="register-powershell-script"></a>Inscrire un script PowerShell

Pour exécuter le script d’inscription, exécutez les applets de commande PowerShell suivantes avec les valeurs d’espace réservé remplacées par les valeurs qui s’appliquent à votre environnement.

```powershell
#MSIX app attach registration sample

#region variables
$packageName = "<package name>"
$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"
#endregion

#region register
Add-AppxPackage -Path $path -DisableDevelopmentMode -Register
#endregion
```

### <a name="deregister-powershell-script"></a>Désinscription du script PowerShell

Pour ce script, remplacez l'espace réservé de **$packageName** par le nom du package que vous testez.

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>Retirer le script PowerShell

Pour ce script, remplacez l'espace réservé de **$packageName** par le nom du package que vous testez. Dans un déploiement de production, il serait préférable de l’exécuter à l’arrêt.

```powershell
#MSIX app attach de staging sample

$vhdSrc="<path to vhd>"

#region variables
$packageName = "<package name>"
$msixJunction = "C:\temp\AppAttach"
#endregion

#region deregister
Remove-AppxPackage -AllUsers -Package $packageName
Remove-Item "$msixJunction\$packageName" -Recurse -Force -Verbose
#endregion

#region Detach VHD
Dismount-DiskImage -ImagePath $vhdSrc -Confirm:$false
#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Configurer des scripts de simulation pour l’agent d’attachement de l’application MSIX

Une fois que vous avez créé les scripts, les utilisateurs peuvent les exécuter manuellement ou les configurer pour qu’ils s’exécutent automatiquement comme des scripts de démarrage, d’ouverture de session, de fermeture de session et d’arrêt. Pour en savoir plus sur ces types de scripts, consultez [Utiliser des scripts de démarrage, d’arrêt, d’ouverture de session et de fermeture de session dans une stratégie de groupe](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

Chacun de ces scripts automatiques exécute une phase des scripts d’attachement d’application :

- Le script de démarrage exécute le script de stockage.
- Le script d’ouverture de session exécute le script d’inscription.
- Le script de fermeture de session exécute le script de désinscription.
- Le script d’arrêt exécute le script de retrait.

## <a name="use-packages-offline"></a>Utiliser des packages hors connexion

Si vous utilisez des packages du [Microsoft Store pour Entreprises](https://businessstore.microsoft.com/) ou du [Microsoft Store pour l’Éducation](https://educationstore.microsoft.com/) au sein de votre réseau ou sur des appareils qui ne sont pas connectés à Internet, vous devez obtenir les licences de package à partir du Microsoft Store et les installer sur votre appareil pour exécuter l’application avec succès. Si votre appareil est en ligne et peut se connecter au Microsoft Store pour Entreprises, les licences requises devraient être téléchargées automatiquement, mais si vous êtes hors connexion, vous devez configurer les licences manuellement.

Pour installer les fichiers de licence, vous devez utiliser un script PowerShell qui appelle la classe MDM_EnterpriseModernAppManagement_StoreLicenses02_01 dans le fournisseur de pont WMI.

Voici comment configurer les licences pour une utilisation hors connexion :

1. Téléchargez le package d’application, les licences et les frameworks requis à partir du Microsoft Store pour Entreprises. Vous avez besoin à la fois des fichiers de licence encodés et non codés. Vous trouverez des instructions de téléchargement détaillées [ici](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Mettez à jour les variables suivantes dans le script de l’étape 3 :
      1. `$contentID` est la valeur ContentID du fichier de licence non codé (.xml). Vous pouvez ouvrir le fichier de licence dans l’éditeur de texte de votre choix.
      2. `$licenseBlob` est la chaîne entière pour l’objet Blob de licence dans le fichier de licence encodé (.bin). Vous pouvez ouvrir le fichier de licence encodé dans l’éditeur de texte de votre choix.
3. Exécutez le script suivant depuis une invite admin PowerShell ISE. Un bon emplacement pour effectuer l’installation de la licence est à la fin du [script de stockage](#stage-powershell-script) qui doit également être exécuté à partir d’une invite admin.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />'

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param)


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}
```

## <a name="next-steps"></a>Étapes suivantes

Cette fonctionnalité n’est pas prise en charge actuellement, mais vous pouvez poser des questions à la communauté sur la [TechCommunity Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Vous pouvez également laisser vos commentaires sur Windows Virtual Desktop sur le [Hub de commentaires Windows Virtual Desktop](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
