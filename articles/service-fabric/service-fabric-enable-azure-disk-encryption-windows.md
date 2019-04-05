---
title: Activer le chiffrement de disque pour les clusters Azure Service Fabric Windows | Microsoft Docs
description: Cet article décrit comment activer le chiffrement de disque pour les nœuds de cluster Service Fabric dans Azure en utilisant Azure Resource Manager et Azure Key Vault.
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
ms.openlocfilehash: 2e9c41409c1f528947e3bef281e9a3c34da39e9b
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59050440"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Activer le chiffrement de disque pour les nœuds de cluster Windows Service Fabric 
> [!div class="op_single_selector"]
> * [Chiffrement de disque pour Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Disk Encryption pour Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Suivez les étapes ci-dessous pour activer le chiffrement de disque sur les nœuds de cluster Windows Service Fabric. Vous devez effectuer les opérations suivantes pour chacun des types de nœud/groupes de machines virtuelles identiques. Pour chiffrer les nœuds, nous allons utiliser les fonctionnalités d’Azure Disk Encryption sur les groupes de machines virtuelles identiques.

Ce guide couvre les procédures suivantes :

* Concepts clés que vous devez connaître pour activer le chiffrement de disque sur le groupe de machines virtuelles identiques de cluster Windows Service Fabric.
* Étapes prérequises à suivre avant d’activer le chiffrement de disque sur le groupe de machines virtuelles identiques de cluster Windows Service Fabric.
* Étapes à suivre pour activer le chiffrement de disque sur le groupe de machines virtuelles identiques de cluster Windows Service Fabric.

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
* **Azure Key Vault** : Créez un coffre de clés dans le même abonnement et la même région que le groupe identique, puis définissez la stratégie d’accès « EnabledForDiskEncryption » dans le coffre de clés à l’aide de son applet de commande PS. Vous pouvez également définir la stratégie à l’aide de l’interface utilisateur du coffre de clés dans le portail Azure : 
```powershell
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* Installer la dernière version [Azure CLI](/cli/azure/install-azure-cli) , qui a les nouvelles commandes de chiffrement.
* Installez la dernière version du kit [SDK Azure d’Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Voici les applets de commande ADE de groupe de machines virtuelles identiques pour activer ([Set](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) le chiffrement, récupérer ([Get](/powershell/module/az.compute/get-azvmssvmdiskencryption)) l’état du chiffrement et supprimer ([désactiver](/powershell/module/az.compute/disable-azvmssdiskencryption)) le chiffrement sur une instance de groupe identique.

| Commande | Version |  Source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 ou version ultérieure | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 ou version ultérieure | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 ou version ultérieure | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 ou version ultérieure | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 ou version ultérieure | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 ou version ultérieure | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scénarios pris en charge pour le chiffrement de disque
* Le chiffrement de groupes de machines virtuelles identiques est pris en charge uniquement pour les groupes identiques créés avec des disques managés ; il ne l’est pas pour les groupes identiques de disques natifs (ou non managés).
* Chiffrement mise à l’échelle de machine virtuelle est prise en charge pour les volumes de données et de système d’exploitation pour Windows machines virtuelles identiques. La désactivation du chiffrement est prise en charge pour les volumes de données et de systèmes d’exploitation des groupes identiques Windows.
* Créer une nouvelle image de machine virtuelle de machines virtuelles identiques et les opérations de mise à niveau ne sont pas pris en charge dans la version préliminaire actuelle.


### <a name="create-new-cluster-and-enable-disk-encryption"></a>Créer un cluster et activer le chiffrement de disque

Utilisez les commandes suivantes pour créer le cluster et activer le chiffrement de disque à l’aide du modèle Azure Resource Manager et un certificat auto-signé.

### <a name="sign-in-to-azure"></a>Connexion à Azure 

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Utiliser le modèle personnalisé que vous possédez déjà 

Si vous avez besoin de créer un modèle personnalisé adapté à vos besoins, il est vivement recommandé de commencer par l’un des modèles disponibles dans les [exemples de modèles Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Suivez les instructions et explications ci-dessous pour [personnaliser votre modèle de cluster][customize-your-cluster-template].

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

#### <a name="deploy-application-to-windows-service-fabric-cluster"></a>Déployer l’application sur un cluster Windows Service Fabric
Suivez les étapes et les instructions pour [déployer l’application sur votre cluster](service-fabric-deploy-remove-applications.md)


#### <a name="enable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set-created-above"></a>Activer le chiffrement de disque pour le groupe de machines virtuelles identiques de cluster Service Fabric créé précédemment
 
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


#### <a name="validate-if-disk-encryption-enabled-for-windows-virtual-machine-scale-set"></a>Validez si le chiffrement de disque est activé pour le groupe de machines virtuelles identiques Windows.
Obtenez l’état d’un groupe de machines virtuelles identiques entier ou d’une instance du groupe. Consultez les commandes ci-dessous.
En outre l’utilisateur peut se connecter à la machine virtuelle dans un ensemble d’échelle et assurez-vous que les disques sont chiffrés

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

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Étapes suivantes
À ce stade, vous avez un cluster sécurisé avec l’activation/désactivation du chiffrement de disque pour un groupe de machines virtuelles identiques de cluster Service Fabric. À suivre, [Chiffrement de disque pour Linux](service-fabric-enable-azure-disk-encryption-linux.md) 

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
