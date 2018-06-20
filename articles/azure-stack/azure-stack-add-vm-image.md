---
title: Ajouter une image de machine virtuelle à Azure Stack et la supprimer | Microsoft Docs
description: Ajoutez ou supprimez une image de machine virtuelle Windows ou Linux personnalisée de votre organisation à utiliser par les locataires.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 05/24/2018
ms.author: mabrigg
ms.reviewer: kivenkat
ms.openlocfilehash: 309bd35eabe1d5c4af9a35d5f2b879bce225a402
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604367"
---
# <a name="make-a-virtual-machine-image-available-in-azure-stack"></a>Mettre une image de machine virtuelle à la disposition des utilisateurs dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Dans Azure Stack, vous pouvez mettre des images de machine virtuelle à la disposition de vos utilisateurs. Ces images peuvent être référencées par des modèles Azure Resource Manager ou vous pouvez les ajouter à l’interface utilisateur de la Place de marché Azure en tant qu’élément sur la Place de marché. Vous pouvez utiliser une image à partir de la Place de marché Azure globale ou ajouter une image personnalisée. Vous pouvez ajouter une machine virtuelle à l’aide du portail ou de Windows PowerShell.

## <a name="add-a-vm-image-through-the-portal"></a>Ajouter une image de machine virtuelle par le biais du portail

> [!NOTE]
> Avec cette méthode, vous devez créer l’élément de la Place de marché séparément.

Les images doivent pouvoir être référencées par un URI de stockage d’objets blob. Préparez une image de système d’exploitation Windows ou Linux au format VHD (pas VHDX), puis chargez cette image dans un compte de stockage Azure ou Azure Stack. Si votre image est déjà chargée dans le stockage d’objets blob dans Azure ou Azure Stack, ignorez l’étape 1.

1. [Chargez une image de machine virtuelle Windows dans Azure pour les déploiements Resource Manager](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) ou, pour une image Linux, suivez les instructions fournies dans [Déployer des machines virtuelles Linux dans Azure Stack](azure-stack-linux.md). Avant de télécharger l’image, il est important de tenir compte des facteurs suivants :

   - Azure Stack prend en charge le format VHD de disque fixe. Le format fixe structure linéairement le disque logique dans le fichier de façon que l’offset du disque X soit stocké à l’offset de l’objet blob X. Un petit pied de page à la fin de l’objet blob décrit les propriétés du disque dur virtuel. Pour vérifier si votre disque est fixe, utilisez la commande PowerShell [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps).  

    > [!IMPORTANT]
    >  Azure Stack ne prend pas en charge les disques durs virtuels de disque dynamique. Le redimensionnement d’un disque dynamique attaché à une machine virtuelle laissera la machine virtuelle dans un état d’échec. Pour résoudre ce problème, supprimez la machine virtuelle sans supprimer le disque de la machine virtuelle, un objet blob VHD dans un compte de stockage. Ensuite, convertissez le disque dur virtuel d’un disque dynamique en un disque fixe et recréez la machine virtuelle.

   * Il est préférable de charger une image dans le stockage d’objets blob Azure Stack plutôt que dans le stockage d’objets blob Azure, car l’envoi (push) d’une image dans le référentiel d’images Azure Stack est plus rapide.

   * Quand vous chargez [l’image de machine virtuelle Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), pensez à substituer l’étape **Se connecter à Azure** par l’étape [Configurer l’environnement PowerShell de l’opérateur Azure Stack](azure-stack-powershell-configure-admin.md).  

   * Prenez note de l’URI de stockage d’objets blob dans lequel vous chargez l’image. L’URI de stockage d’objets blob est au format suivant : *&lt;compte_stockage&gt;/&lt;conteneur_objets_blob&gt;/&lt;nom_disque_dur_virtuel_cible&gt;*.vhd.

   * Pour rendre le stockage Blob accessible de manière anonyme, accédez au conteneur d’objets blob du compte de stockage dans lequel l’image de machine virtuelle VHD a été chargée. Sélectionnez **Blob** puis choisissez **Stratégie d’accès**. Si vous le souhaitez, vous pouvez à la place générer une signature d’accès partagé pour le conteneur et l’inclure dans l’URI de l’objet blob.

   ![Accéder aux objets blob du compte de stockage](./media/azure-stack-add-vm-image/image1.png)

   ![Définir un accès public pour les objets blob](./media/azure-stack-add-vm-image/image2.png)

2. Connectez-vous au Azure Stack en tant qu’opérateur. Dans le menu, sélectionnez sur **Autres services** > **Fournisseurs de ressources**. Ensuite, sélectionnez **Compute** > **Images de machine virtuelle** > **Ajouter**.

3. Dans le panneau **Ajouter une image de machine virtuelle**, saisissez l’éditeur, l’offre, la référence SKU et la version de l’image de machine virtuelle. Ces segments de nom référencent l’image de machine virtuelle dans les modèles Resource Manager. Sélectionnez la valeur **type de système d’exploitation** approprié. Pour **URI de l’objet blob OS Disk**, saisissez l’URI de l’objet Blob où l’image a été téléchargée. Cliquez ensuite sur **Créer** pour commencer la création d’une image de machine virtuelle.

   ![Démarrer la création de l’image](./media/azure-stack-add-vm-image/image4.png)

   Une fois que l’image de machine virtuelle a été créée, son état passe à **Réussi**.

4. Pour rendre une image de machine virtuelle plus rapidement disponible dans l’interface utilisateur, c’est une bonne idée de [créer un élément de Place de marché](azure-stack-create-and-publish-marketplace-item.md).

## <a name="remove-a-vm-image-through-the-portal"></a>Supprimer une image de machine virtuelle par le biais du portail

1. Ouvrez le portail d’administration à l’adresse [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).

2. Sélectionnez **Gestion de la Place de marché**, puis sélectionnez la machine virtuelle à supprimer.

3. Cliquez sur **Supprimer**.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>Ajouter une image de machine virtuelle à la Place de marché à l’aide de PowerShell

1. [Installez PowerShell pour Azure Stack](azure-stack-powershell-install.md).  

2. Connectez-vous à Azure Stack comme opérateur. Pour obtenir des instructions, consultez [Se connecter à Azure Stack comme opérateur](azure-stack-powershell-configure-admin.md).

3. Ouvrez PowerShell avec une invite de commandes avec élévation de privilèges et exécutez :

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>” `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
  ````

  L’applet de commande **Add-AzsPlatformimage** spécifie les valeurs utilisées par les modèles Azure Resource Manager pour référencer l’image de machine virtuelle. Les valeurs incluent :
  - **publisher**  
    Par exemple : `Canonical`  
    Segment du nom de l’éditeur de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. **Microsoft** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
  - **offer**  
    Par exemple : `UbuntuServer`  
    Segment du nom de l’offre de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. **WindowsServer** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
  - **sku**  
    Par exemple : `14.04.3-LTS`  
    Segment du nom de référence SKU de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. **Datacenter2016** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
  - **version**  
    Par exemple : `1.0.0`  
    Version de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. La version suit le format *\#.\#.\#*, **1.0.0** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
  - **osType**  
    Par exemple : `Linux`  
    Type d’exploitation de l’image, qui doit être **Windows** ou **Linux**.  
  - **OSUri**  
    Par exemple : `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
    Vous pouvez spécifier un URI de stockage d’objets blob comme `osDisk`.  

    Pour plus d’informations, consultez la référence sur PowerShell pour les cmdlet [Add-AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) et [New-DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject).

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>Ajouter une image de machine virtuelle personnalisée à la Place de marché à l’aide de PowerShell

1. [Installez PowerShell pour Azure Stack](azure-stack-powershell-install.md).

  ```PowerShell  
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ```

2. Si vous utilisez les **services de fédération Active Directory (AD FS)**, utilisez l’applet de commande suivante :

  ```PowerShell
  # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAuidence endpoint for your environment>"

  # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    ```

3. Connectez-vous à Azure Stack comme opérateur. Pour obtenir des instructions, consultez [Se connecter à Azure Stack comme opérateur](azure-stack-powershell-configure-admin.md).

4. Créez un compte de stockage dans Azure Stack ou Azure au niveau global pour stocker votre image de machine virtuelle personnalisée. Pour obtenir des instructions, consultez [Démarrage rapide : Charger, télécharger et répertorier des objets blob à l’aide du portail Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

5. Préparez une image de système d’exploitation Windows ou Linux au format VHD (et non VHDX), chargez l’image sur votre compte de stockage et obtenez l’URI où l’image de machine virtuelle peut être récupérée par PowerShell.  

  ````PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ````

6. (Facultatif) Vous pouvez charger un tableau de disques de données dans le cadre de l’image de machine virtuelle. Créez vos disques de données à l’aide de l’applet de commande New-DataDiskObject. Ouvrez PowerShell à partir d’une invite de commandes avec élévation de privilèges et exécutez :

  ````PowerShell  
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
  ````

7. Ouvrez PowerShell avec une invite de commandes avec élévation de privilèges et exécutez :

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>” -OSType "<ostype>" -OSUri "<osuri>"
  ````

    Pour plus d’informations sur les applets de commande Add-AzsPlatformimage et New-DataDiskObject, consultez la [documentation de l’opérateur sur les modules Azure Stack PowerShell](https://docs.microsoft.com/powershell/module/) de Microsoft.

## <a name="remove-a-vm-image-by-using-powershell"></a>Supprimer une image de machine virtuelle à l’aide de PowerShell

Si vous n’avez plus besoin de l’image de machine virtuelle que vous avez chargée, vous pouvez la supprimer sur la Place de marché à l’aide de l’applet de commande suivante :

1. [Installez PowerShell pour Azure Stack](azure-stack-powershell-install.md).

2. Connectez-vous à Azure Stack comme opérateur.

3. Ouvrez PowerShell avec une invite de commandes avec élévation de privilèges et exécutez :

  ````PowerShell  
  Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
  ````
  L’applet de commande **Remove-AzsPlatformImage** spécifie les valeurs utilisées par les modèles Azure Resource Manager pour référencer l’image de machine virtuelle. Les valeurs incluent :
  - **publisher**  
    Par exemple : `Canonical`  
    Segment du nom de l’éditeur de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. **Microsoft** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
  - **offer**  
    Par exemple : `UbuntuServer`  
    Segment du nom de l’offre de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. **WindowsServer** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
  - **sku**  
    Par exemple : `14.04.3-LTS`  
    Segment du nom de référence SKU de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. **Datacenter2016** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
  - **version**  
    Par exemple : `1.0.0`  
    Version de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. La version suit le format *\#.\#.\#*, **1.0.0** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ.  
    
    Pour plus d’informations sur l’applet de commande Remove-AzsPlatformImage, consultez la [documentation de l’opérateur sur les modules Azure Stack PowerShell](https://docs.microsoft.com/powershell/module/) de Microsoft.

## <a name="next-steps"></a>Étapes suivantes

[Approvisionner une machine virtuelle](azure-stack-provision-vm.md)