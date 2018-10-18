---
title: Comment créer des images de machines virtuelles Microsoft Azure avec Packer | Microsoft Docs
description: Découvrez comment utiliser Packer pour créer des images de machines virtuelles Windows dans Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/29/2018
ms.author: cynthn
ms.openlocfilehash: 03723b8653e42bca371f47e16657e7e973c257ae
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44294984"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Comment utiliser Packer pour créer des images de machines virtuelles Windows dans Azure
Chaque machine virtuelle dans Azure est créée à partir d’une image qui définit la distribution Windows et la version du système d’exploitation. Les images peuvent inclure des configurations et des applications pré-installées. La Place de marché Microsoft Azure fournit de nombreuses images internes et de tiers pour les systèmes d’exploitation et environnements d’application les plus courants. Vous pouvez également créer vos propres images personnalisées selon vos besoins. Cet article explique comment utiliser l’outil open source [Packer](https://www.packer.io/) pour définir et générer des images personnalisées dans Azure.


## <a name="create-azure-resource-group"></a>Créer un groupe de ressources Azure
Pendant le processus de génération, Packer crée des ressources Azure temporaires lorsqu’il génère la machine virtuelle source. Pour capturer cette machine virtuelle source afin de l’utiliser en tant qu’image, vous devez définir un groupe de ressources. La sortie du processus de génération Packer est stockée dans ce groupe de ressources.

Créez un groupe de ressources avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```powershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzureRmResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Créer des informations d’identification Azure
Packer s’authentifie auprès d’Azure à l’aide d’un principal de service. Un principal de service Azure est une identité de sécurité que vous pouvez utiliser avec des applications, des services et des outils d’automatisation comme Packer. Vous contrôlez et vous définissez les opérations que le principal du service est autorisé à effectuer dans Azure.

Créez un principal de service avec la commande [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) et assignez au principal de service les autorisations requises pour créer et gérer des ressources avec la commande [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) :

```powershell
$sp = New-AzureRmADServicePrincipal -DisplayName "AzurePacker" `
    -Password (ConvertTo-SecureString "P@ssw0rd!" -AsPlainText -Force)
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Pour vous authentifier auprès d’Azure, vous devez également obtenir vos ID client et d’abonnement Azure à l’aide de la commande [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) :

```powershell
$sub = Get-AzureRmSubscription
$sub.TenantId[0]
$sub.SubscriptionId[0]
```

Vous utiliserez ces deux ID à l’étape suivante.


## <a name="define-packer-template"></a>Définition du modèle Packer
Pour générer les images, vous devez créer un modèle en tant que fichier JSON. Dans le modèle, vous définissez des générateurs et des fournisseurs pour mener à bien le processus de génération réel. Packer intègre un [générateur pour Azure](https://www.packer.io/docs/builders/azure.html) qui vous permet de définir des ressources Azure, telles que les informations d’identification du principal de service créées à l’étape précédente.

Créez un fichier nommé *windows.json* et collez le contenu suivant : Saisissez vos propres valeurs comme suit :

| Paramètre                           | Emplacement |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Affichage de l’ID du principal de service avec `$sp.applicationId` |
| *client_secret*                     | Mot de passe que vous avez spécifié dans `$securePassword` |
| *tenant_id*                         | Sortie de la commande `$sub.TenantId` |
| *subscription_id*                   | Sortie de la commande `$sub.SubscriptionId` |
| *object_id*                         | Affichage de l’ID d’objet du principal de service avec `$sp.Id` |
| *managed_image_resource_group_name* | Nom du groupe de ressources créé lors de la première étape |
| *managed_image_name*                | Nom de l’image de disque géré créée |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "0831b578-8ab6-40b9-a581-9a880a94aab1",
    "client_secret": "P@ssw0rd!",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "object_id": "a7dfb070-0d5b-47ac-b9a5-cf214fff0ae2",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "3m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Ce modèle génère une machine virtuelle Windows Server 2016, installe IIS, puis généralise la machine virtuelle avec Sysprep. L’installation d’IIS montre comment utiliser le fournisseur PowerShell pour exécuter des commandes supplémentaires. L’image finale de Packer inclut l’installation et la configuration du logiciel requis.


## <a name="build-packer-image"></a>Génération de l’image Packer
Si Packer n’est pas encore installé sur votre ordinateur local, [suivez les instructions d’installation de Packer](https://www.packer.io/docs/install/index.html).

Générez l’image en spécifiant votre fichier de modèle Packer comme suit :

```bash
./packer build windows.json
```

Voici un exemple de la sortie des commandes précédentes :

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

La génération de la machine virtuelle, l’exécution des fournisseurs et le nettoyage du déploiement par Packer ne prennent que quelques minutes.


## <a name="create-a-vm-from-the-packer-image"></a>Créer une machine virtuelle à partir de l’image de Packer
Vous pouvez à présent créer une machine virtuelle à partir de votre image à l’aide de la commande [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Si elles n’existent pas déjà, les ressources réseau requises sont créées. À l’invite, entrez un nom d’utilisateur d’administration et un mot de passe à créer sur la machine virtuelle. L’exemple suivant permet de créer une machine virtuelle nommée *myVM* à partir de *myPackerImage* :

```powershell
New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Si vous souhaitez créer des machines virtuelles dans un autre groupe de ressources ou dans une autre région que votre image Packer, spécifiez l’ID de l’image plutôt que son nom. Pour obtenir l’ID d’image, exécutez la commande [Get-AzureRmImage](/powershell/module/AzureRM.Compute/Get-AzureRmImage).

La création de la machine virtuelle à partir de votre image Packer ne nécessite que quelques minutes.


## <a name="test-vm-and-webserver"></a>Tester la machine virtuelle et le serveur web
Obtenez l’adresse IP publique de votre machine virtuelle avec [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). L’exemple suivant obtient l’adresse IP pour *myPublicIP* créée précédemment :

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Pour voir votre machine virtuelle, qui inclut l’installation d’IIS à partir du fournisseur Packer, entrez l’adresse IP publique dans un navigateur web.

![Site IIS par défaut](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, IIS était déjà installé et vous avez utilisé Packer pour créer une image de machine virtuelle. Vous pouvez utiliser cette image de machine virtuelle avec les flux de travail de déploiement existants, par exemple pour déployer votre application sur les machines virtuelles créées à partir de l’image avec Azure DevOps Services, Ansible, Chef ou Puppet.

Pour obtenir d’autres exemples de modèles Packer pour d’autres distributions Windows, consultez [ce référentiel GitHub](https://github.com/hashicorp/packer/tree/master/examples/azure).
