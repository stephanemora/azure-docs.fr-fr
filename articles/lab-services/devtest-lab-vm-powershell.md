---
title: Créer une machine virtuelle dans DevTest Labs avec Azure PowerShell | Microsoft Docs
description: Découvrez comment utiliser Azure DevTest Labs pour créer et gérer des machines virtuelles avec Azure PowerShell.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: a9629cd14c71a163612c2c4ba3c7b109a52b91ad
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60008356"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Créer une machine virtuelle avec DevTest Labs à l’aide d’Azure PowerShell
Cet article vous montre comment créer une machine virtuelle dans Azure DevTest Labs à l’aide d’Azure PowerShell. Vous pouvez utiliser des scripts PowerShell pour automatiser la création de machines virtuelles dans un laboratoire dans Azure DevTest Labs. 

## <a name="prerequisites"></a>Conditions préalables
Avant de commencer :

- [Créer un laboratoire](devtest-lab-create-lab.md) si vous ne souhaitez pas utiliser un laboratoire existant pour tester le script ou des commandes dans cet article. 
- [Installez Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0) ou utiliser Azure Cloud Shell est intégré dans le portail Azure. 

## <a name="powershell-script"></a>Script PowerShell
L’exemple de script dans cette section utilise les [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) applet de commande.  Cette applet de commande prend l’ID de ressource du laboratoire, nom de l’action à effectuer (`createEnvironment`), et les paramètres nécessaires effectuer cette action. Les paramètres sont dans une table de hachage qui contient toutes les propriétés de description de machine virtuelle. 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

Les propriétés de la machine virtuelle dans le script ci-dessus permettent de créer une machine virtuelle avec Windows Server 2016 DataCenter en tant que le système d’exploitation. Pour chaque type de machine virtuelle, ces propriétés seront légèrement différentes. Le [définir une machine virtuelle](#define-virtual-machine) section vous montre comment déterminer quelles propriétés à utiliser dans ce script.

La commande suivante fournit un exemple d’exécution du script enregistré dans un nom de fichier : Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Définir une machine virtuelle
Cette section vous montre comment obtenir les propriétés qui sont spécifiques à un type de machine virtuelle que vous souhaitez créer. 

### <a name="use-azure-portal"></a>Utiliser le portail Azure
Vous pouvez générer un modèle Azure Resource Manager lors de la création d’une machine virtuelle dans le portail Azure. Vous n’avez pas besoin de terminer le processus de création de la machine virtuelle. Vous ne suivez la procédure jusqu'à ce que vous voyiez le modèle. Il s’agit de la meilleure façon d’obtenir la description de JSON nécessaire si vous n’avez pas déjà d’un laboratoire de que machine virtuelle créée. 

1. Accédez au [portail Azure](https://portal.azure.com).
2. Sélectionnez **tous les Services** dans le menu de navigation gauche.
3. Recherchez et sélectionnez **dev/test** à partir de la liste des services. 
4. Sur le **dev/test** , sélectionnez votre laboratoire dans la liste des laboratoires.
5. Sur la page d’accueil pour votre laboratoire, sélectionnez **+ ajouter** sur la barre d’outils. 
6. Sélectionnez un **image de base** pour la machine virtuelle. 
7. Sélectionnez **options d’automatisation** en bas de la page ci-dessus le **envoyer** bouton. 
8. Vous voyez la **modèle Azure Resource Manager** pour la création de la machine virtuelle. 
9. Le segment JSON dans le **ressources** section a la définition pour le type d’image que vous avez sélectionné précédemment. 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

Dans cet exemple, vous allez apprendre à obtenir une définition d’une image de Place de marché Azure. Vous pouvez obtenir une définition d’une image personnalisée, une formule ou un environnement de la même façon. Ajoutez les artefacts nécessaires pour la machine virtuelle et définir des paramètres avancés requis. Après avoir entré les valeurs pour les champs obligatoires et les champs facultatifs, avant de sélectionner le **options d’automatisation** bouton.

### <a name="use-azure-rest-api"></a>Utiliser l’API REST Azure
La procédure suivante vous donne la procédure permettant d’obtenir les propriétés d’une image à l’aide de l’API REST : Ces étapes fonctionnent uniquement pour une machine virtuelle existante dans un laboratoire. 

1. Accédez à la [liste des Machines virtuelles -](/rest/api/dtl/virtualmachines/list) page, sélectionnez **essayez-le** bouton. 
2. Sélectionnez votre **abonnement Azure**.
3. Entrez le **groupe de ressources pour le laboratoire**.
4. Entrez le **nom de l’atelier**. 
5. Sélectionnez **Exécuter**.
6. Vous voyez la **propriétés de l’image** selon lequel la machine virtuelle a été créée. 



## <a name="next-steps"></a>Étapes suivantes
Consultez le contenu suivant : [Documentation PowerShell Azure pour Azure DevTest Labs](/powershell/module/az.devtestlabs/)
