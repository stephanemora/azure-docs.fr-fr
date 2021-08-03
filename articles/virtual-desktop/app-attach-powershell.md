---
title: Attachement d’application MSIX pour Azure Virtual Desktop à l’aide de PowerShell – Azure
description: Comment configurer l’attachement d’application MSIX pour Azure Virtual Desktop à l’aide de PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 6a5dd53bee4e6ae99559d63441f1240d5905644e
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745418"
---
# <a name="set-up-msix-app-attach-using-powershell"></a>Configurer l’attachement d’application MSIX avec PowerShell

En plus du portail Azure, vous pouvez configurer manuellement l'attachement d'application MSIX à l'aide de PowerShell. Cet article vous guide pas à pas dans l’utilisation de PowerShell pour configurer l’attachement d’application MSIX.

## <a name="requirements"></a>Configuration requise

Voici ce dont vous avez besoin pour configurer l’attachement d’application MSIX :

- Un déploiement Azure Virtual Desktop opérationnel. Pour savoir comment déployer Azure Virtual Desktop (classique), consultez [Créer un locataire dans Azure Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Pour savoir comment déployer Azure Virtual Desktop avec une intégration Azure Resource Manager, consultez [Créer un pool d’hôtes avec le portail Azure](./create-host-pools-azure-marketplace.md).
- Un pool d’hôtes Azure Virtual Desktop avec au moins un hôte de session actif.
- Un groupe d’applications distant de bureau.
- Outil de d’empaquetage MSIX.
- Une application empaquetée au format MSIX et développée en image MSIX qui est chargée dans un partage de fichiers.
- Un partage de fichiers dans votre déploiement Azure Virtual Desktop où le package MSIX sera stocké.
- Le partage de fichiers dans lequel vous avez chargé l’image MSIX doit également être accessible à toutes les machines virtuelles du pool d’hôtes. Les utilisateurs auront besoin d’autorisations en lecture seule pour accéder à l’image.
- Téléchargez et installez PowerShell Core.
- Téléchargez le module Azure PowerShell en préversion publique et développez-le dans un dossier local.
- Installez le module Azure en exécutant l’applet de commande suivante :

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Se connecter à Azure et importer le module

Une fois que toutes les conditions requises sont prêtes, ouvrez PowerShell Core dans une invite de commandes avec élévation de privilèges et exécutez cette applet de commande :

```powershell
Connect-AzAccount
```

Après l’avoir exécutée, authentifiez votre compte avec vos informations d’identification. Dans ce cas, vous pouvez être invité à entrer une URL d’appareil ou un jeton.

## <a name="import-the-azwindowsvirtualdesktop-module"></a>Importer le module Az.WindowsVirtualDesktop

Vous avez besoin du module Az.DesktopVirtualization pour suivre les instructions de cet article.

>[!NOTE]
>Pour la préversion publique, nous fournissons le module sous forme de fichiers ZIP distincts que vous devez importer manuellement.

Avant de commencer, vous pouvez exécuter l’applet de commande suivante pour voir si le module Az.DesktopVirtualization est déjà installé sur votre session ou votre machine virtuelle :

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

Pour désinstaller une copie existante du module et recommencer, exécutez cette applet de commande :

```powershell
Uninstall-Module Az.DesktopVirtualization
```

Si le module est bloqué sur votre machine virtuelle, exécutez cette applet de commande pour le débloquer :

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

Une fois ce nettoyage effectué, il est temps d’importer le module.

1. Exécutez l’applet de commande suivante, puis appuyez sur la touche **R** quand vous êtes invité à accepter d’exécuter le code personnalisé.

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. Une fois que vous avez exécuté l’applet de commande d’importation, vérifiez si le module dispose des applets de commande pour MSIX en exécutant l’applet de commande suivante :

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   Si les applets de commande sont incluses, la sortie doit ressembler à ceci :

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   Si vous ne voyez pas cette sortie, fermez toutes les sessions PowerShell et PowerShell Core, puis réessayez.

## <a name="set-up-helper-variables"></a>Configurer les variables d’assistance

Une fois que vous avez importé le module, vous devez configurer les variables d’assistance. Les exemples suivants vous montrent comment procéder pour chacune.

Pour obtenir votre ID d’abonnement :

```powershell
Get-AzContext -ListAvailable | fl
```

Pour sélectionner le contexte d’un locataire Azure et d’un abonnement avec un nom :

```powershell
$obj = Select-AzContext -Name "<Name>"
```

Pour définir la variable d’abonnement :

```powershell
$subId = $obj.Subscription.Id
```

Pour définir le nom de l’espace de travail :

```powershell
$ws = "<WorksSpaceName>"
```

Pour définir le nom du pool d’hôtes :

```powershell
$hp = "<HostPoolName>"
```

Pour configurer le groupe de ressources dans lequel les machines virtuelles hôtes de session sont configurées :

```powershell
$rg = "<ResourceGroupName>"
```

Et enfin, pour confirmer que vous avez correctement défini toutes les variables :

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>Ajouter un package MSIX à un pool hôte

Une fois que vous avez défini tous les éléments, il est temps d’ajouter le package MSIX à un pool hôtes. Pour ce faire, vous devez d’abord obtenir le chemin UNC vers l’image MSIX.

Avec ce chemin UNC, exécutez cette applet de commande pour développer l’image MSIX :

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

Exécutez cette applet de commande pour ajouter le package MSIX au pool d’hôtes de votre choix :

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

Une fois terminé, confirmez que le package a été créé avec cette applet de commande :

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>Supprimer un package MSIX d’un pool d’hôtes

Pour supprimer un package d’un pool d’hôtes :

Obtenez la liste de tous les packages associés à un pool d’hôtes avec cette applet de commande, puis recherchez le nom du package que vous souhaitez supprimer dans la sortie :

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

Vous pouvez également obtenir un package particulier en fonction de son nom complet avec cette applet de commande :

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

Pour supprimer le package, exécutez cette applet de commande :

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>Publier des applications MSIX dans un groupe d’applications

Vous ne pouvez suivre les instructions de cette section que si vous avez terminé de suivre les instructions des sections précédentes. Si vous disposez d’un pool d’hôtes avec un hôte de session active, au moins un groupe d’applications de bureau et que vous avez ajouté un package MSIX au pool d’hôtes, vous êtes prêt.

Pour publier une application à partir du package MSIX dans un groupe d’applications, vous devez rechercher son nom, puis utiliser ce nom dans l’applet de commande de publication.

Pour publier une application :

Exécutez cette applet de commande pour dresser la liste de tous les groupes d’applications disponibles :

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

Une fois que vous avez trouvé le nom du groupe d’applications dans lequel vous souhaitez publier des applications, utilisez son nom dans cette applet de commande :

```powershell
$grName = "<AppGroupName>"
```

Enfin, vous devez publier l’application.

- Pour publier l’application MSIX dans un groupe d’applications de bureau, exécutez cette applet de commande :

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- Pour publier l’application dans un groupe d’applications distant, exécutez cette applet de commande à la place :

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>Si un utilisateur est affecté à un groupe d’applications distant et à un groupe d’applications de bureau dans le même pool d’hôtes, lorsque l’utilisateur se connecte à son bureau à distance, il voit les applications MSIX des deux groupes.

## <a name="next-steps"></a>Étapes suivantes

Posez vos questions concernant cette fonctionnalité à la [communauté technique Azure Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Vous pouvez également formuler vos commentaires concernant Azure Virtual Desktop sur le [Hub de commentaires Azure Virtual Desktop](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Voici d’autres articles qui peuvent vous être utiles :

- [Glossaire Attachement d’application MSIX](app-attach-glossary.md)
- [FAS sur l’attachement d’application MSIX](app-attach-faq.md)
