---
title: Créer une image personnalisée dans Azure DevTest Labs à partir d’un fichier de disque dur virtuel à l’aide de PowerShell | Microsoft Docs
description: Automatiser la création d’une image personnalisée dans Azure DevTest Labs à partir d’un fichier de disque dur virtuel avec PowerShell
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: c1cdb64e4c8c99eeca4cc66c0d0ad2b755144917
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60201941"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Créer une image personnalisée à partir d’un fichier de disque dur virtuel avec PowerShell

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Instructions pas à pas

La procédure suivante décrit comment créer une image personnalisée à partir d’un fichier de disque dur virtuel avec PowerShell :

1. À l’invite de PowerShell, connectez-vous à votre compte Azure avec l’appel suivant à la **Connect-AzAccount** applet de commande.

    ```powershell
    Connect-AzAccount
    ```

1.  Sélectionnez l’abonnement Azure souhaité en appelant le **sélectionnez-AzSubscription** applet de commande. Remplacez l’espace suivant réservé à la variable **$subscriptionId** par un ID d’abonnement Azure valide.

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  Obtenir l’objet de laboratoire en appelant le **Get-AzResource** applet de commande. Remplacez les espaces suivants réservés aux variables **$labRg** et **$labName** par les valeurs appropriées pour votre environnement.

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  Récupérez les valeurs de clé du compte de stockage et du compte de stockage du laboratoire dans l’objet de laboratoire.

    ```powershell
    $labStorageAccount = Get-AzResource -ResourceId $lab.Properties.defaultStorageAccount
    $labStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value
    ```

1.  Remplacez l’espace suivant réservé à la variable **$vhdUri** par l’URI vers le fichier VHD chargé. Vous pouvez obtenir l’URI du fichier de disque dur virtuel dans le panneau d’objet blob du compte de stockage, dans le portail Azure.

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Créer l’image personnalisée à l’aide de la **New-AzResourceGroupDeployment** applet de commande. Remplacez les espaces suivants réservés aux variables **$customImageName** et **$customImageDescription** par des noms significatifs pour votre environnement.

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>Script PowerShell pour créer une image personnalisée à partir d’un fichier de disque dur virtuel

Le script PowerShell suivant peut être utilisé pour créer une image personnalisée à partir d’un fichier de disque dur virtuel. Remplacez les espaces réservés (entre crochets) par les valeurs adaptées à vos besoins.

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the lab storage account and lab storage account key values.
$labStorageAccount = Get-AzResource -ResourceId $lab.Properties.defaultStorageAccount
$labStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value

# Set the URI of the VHD file.
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Billets de blog connexes

- [Custom images or formulas? (Images personnalisées ou formules ?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Copie d’images personnalisées entre plusieurs Azure DevTest Labs)](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Étapes suivantes

- [Ajout d’une machine virtuelle à votre laboratoire](devtest-lab-add-vm.md)
