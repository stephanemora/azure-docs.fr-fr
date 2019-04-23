---
title: Activer le chiffrement de disque pour les clusters Azure Service Fabric Linux | Microsoft Docs
description: Cet article explique comment activer le chiffrement de disque pour l’échelle de cluster Service Fabric définie dans Azure en utilisant Azure Resource Manager et Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2019
ms.author: aljo
ms.openlocfilehash: f580bf02b222f01a3d5aad1254f208791ea22b38
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046973"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>Activer le chiffrement de disque pour les nœuds de cluster Linux Service Fabric 
> [!div class="op_single_selector"]
> * [Chiffrement de disque pour Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Chiffrement de disque pour Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Suivez les étapes ci-dessous pour activer le chiffrement de disque sur les nœuds de cluster Linux Service Fabric. Vous devez les effectuer pour chacun des types de nœuds/groupes de machines virtuelles identiques. Pour chiffrer les nœuds, nous allons utiliser les fonctionnalités d’Azure Disk Encryption sur les groupes de machines virtuelles identiques.

Ce guide couvre les procédures suivantes :

* Concepts clés que vous devez connaître pour activer le chiffrement de disque sur le groupe de machines virtuelles identiques de cluster Linux Service Fabric.
* Étapes prérequises à suivre avant d’activer le chiffrement de disque sur le groupe de machines virtuelles identiques de cluster Linux Service Fabric.
* Étapes à suivre pour activer le chiffrement de disque sur le groupe de machines virtuelles identiques de cluster Linux Service Fabric.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Conditions préalables

* **Inscription automatique** : Pour être utilisée, la préversion du chiffrement de disque de groupe de machines virtuelles identiques nécessite une inscription automatique.
* Vous pouvez procéder vous-même à l’inscription de votre abonnement en exécutant les étapes suivantes : 
```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* Attendez environ 10 minutes pour que l’état soit « Inscrit ». Vous pouvez vérifier l’état en exécutant la commande suivante : 
```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```
* **Azure Key Vault** : Créez un coffre de clés dans le même abonnement et la même région que le groupe de machines virtuelles identiques, puis définissez la stratégie d’accès « EnabledForDiskEncryption » dans le coffre de clés à l’aide de son applet de commande PS. Vous pouvez également définir la stratégie à l’aide de l’interface utilisateur du coffre de clés dans le portail Azure : 
```powershell
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* Installer la dernière version [Azure CLI](/cli/azure/install-azure-cli) , qui a les nouvelles commandes de chiffrement.
* Installez la dernière version du kit [SDK Azure d’Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Les éléments suivants sont les machines virtuelles identiques ADE des applets de commande pour activer ([définir](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) chiffrement, récupérer ([obtenir](/powershell/module/az.compute/get-azvmssvmdiskencryption)) état de chiffrement et de suppression ([désactiver](/powershell/module/az.compute/disable-azvmssdiskencryption)) chiffrement sur la mise à l’échelle instance de l’ensemble. 

| Commande | Version |  Source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 ou version ultérieure | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 ou version ultérieure | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 ou version ultérieure | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 ou version ultérieure | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 ou version ultérieure | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 ou version ultérieure | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scénarios pris en charge pour le chiffrement de disque
* Le chiffrement de groupes de machines virtuelles identiques est pris en charge uniquement pour les groupes identiques créés avec des disques managés, il ne l’est pas pour les groupes identiques de disques natifs (ou non managés).
* Le chiffrement de groupes de machines virtuelles identiques est pris en charge pour un volume de données d’un groupe de machines virtuelles identiques Linux. Le chiffrement de disque de systèmes d’exploitation n’est PAS pris en charge dans la préversion actuelle pour Linux.
* Créer une nouvelle image de machine virtuelle de machines virtuelles identiques et les opérations de mise à niveau ne sont pas pris en charge dans la version préliminaire actuelle.


### <a name="create-new-linux-cluster-and-enable-disk-encryption"></a>Créer un cluster Linux et activer le chiffrement de disque

Utilisez les commandes suivantes pour créer le cluster et activer le chiffrement de disque à l’aide du modèle Azure Resource Manager et un certificat auto-signé.

### <a name="sign-in-to-azure"></a>Connexion à Azure  

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Utiliser le modèle personnalisé que vous possédez déjà 

Si vous avez besoin de créer un modèle personnalisé adapté à vos besoins, il est vivement recommandé de commencer avec l’un des modèles disponibles dans les [exemples de modèles Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) pour un cluster Linux. 

Si vous disposez déjà d’un modèle personnalisé, vérifiez bien que les trois paramètres liés au certificat dans le modèle et le fichier de paramètres portent les noms indiqués ci-après et que les valeurs sont null comme suit.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Étant donné que pour le groupe de machines virtuelles identiques Linux, seul le chiffrement des disques de données est pris en charge. Nous devons donc ajouter le disque de données à l’aide du modèle Azure Resource Manager. Mettez à jour votre modèle de provisionnement de disque de données comme indiqué ci-dessous :

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
```
 

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Voici la commande CLI équivalente pour faire de même. Modifiez les valeurs dans les instructions declare et remplacez-les par les valeurs appropriées. CLI prend en charge tous les autres paramètres que la commande PowerShell ci-dessus prend en charge.

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

#### <a name="linux-data-disk-mounting"></a>Montage de disque de données Linux
Avant de procéder au chiffrement sur un groupe de machines virtuelles identiques Linux, nous devons vérifier si le disque de données ajouté est correctement monté, ou non. Se connecter à la machine virtuelle de Cluster Linux et exécutez la commande LSBLK. La sortie doit indiquer ce disque de données ajouté dans la colonne du point de montage.


#### <a name="deploy-application-to-linux-service-fabric-cluster"></a>Déployer l’application sur un cluster Service Fabric Linux
Suivez les étapes et les instructions indiquées pour [déployer l’application sur votre cluster](service-fabric-quickstart-containers-linux.md).


#### <a name="enable-disk-encryption-for-service-fabric-linux-cluster-virtual-machine-scale-set-created-above"></a>Activer le chiffrement de disque pour le groupe de machines virtuelles identiques de cluster Service Fabric Linux créé précédemment
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

#### <a name="validate-if-disk-encryption-enabled-for-linux-virtual-machine-scale-set"></a>Validez si le chiffrement de disque est activé pour le groupe de machines virtuelles identiques Linux.
Obtenez l’état de l’ensemble d’un groupe de machines virtuelles identiques ou d’une machine virtuelle d’une instance du groupe identique. Consultez les commandes ci-dessous.
En outre un utilisateur peut se connecter à la machine virtuelle de Cluster Linux et exécuter la commande LSBLK. La sortie doit indiquer ce disque de données ajouté dans la colonne du point de montage et Crypt dans la colonne Type pour le disque de données ajouté.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Désactiver le chiffrement de disque pour le groupe de machines virtuelles identiques de cluster Service Fabric 
La désactivation du chiffrement de disque s’applique à l’ensemble du groupe de machines virtuelles identiques et non par instance. 

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Étapes suivantes
À ce stade, vous avez un cluster sécurisé avec l’activation/la désactivation du chiffrement de disque pour un groupe de machines virtuelles identiques de cluster Service Fabric Linux. À suivre, [Chiffrement de disque pour Windows](service-fabric-enable-azure-disk-encryption-windows.md) 
