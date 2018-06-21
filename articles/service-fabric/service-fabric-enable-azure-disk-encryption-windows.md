---
title: Activer le chiffrement de disque pour les clusters Windows Service Fabric | Microsoft Docs
description: Cet article décrit comment activer le chiffrement de disque pour les nœuds de cluster Service Fabric dans Azure en utilisant Azure Resource Manager et Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: v-viban
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/23/2018
ms.author: v-viban
ms.openlocfilehash: 0b84d270cc50888822b8463df2b95aedaa34ee9a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659646"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Activer le chiffrement de disque pour les nœuds de cluster Windows Service Fabric 
> [!div class="op_single_selector"]
> * [Chiffrement de disque pour Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Chiffrement de disque pour Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Suivez les étapes ci-dessous pour activer le chiffrement de disque sur les nœuds de cluster Windows Service Fabric. Vous devez effectuer les opérations suivantes pour chacun des types de nœud/groupes de machines virtuelles identiques. Pour chiffrer les nœuds, nous allons utiliser les fonctionnalités d’Azure Disk Encryption sur les groupes de machines virtuelles identiques.

Ce guide couvre les procédures suivantes :

* Concepts clés que vous devez connaître pour activer le chiffrement de disque sur le groupe de machines virtuelles identiques de cluster Windows Service Fabric.
* Étapes prérequises à suivre avant d’activer le chiffrement de disque sur le groupe de machines virtuelles identiques de cluster Windows Service Fabric.
* Étapes à suivre pour activer le chiffrement de disque sur le groupe de machines virtuelles identiques de cluster Windows Service Fabric.


## <a name="prerequisites"></a>Prérequis
1. **Inscription automatique** : Pour être utilisée, la préversion du chiffrement de disque de groupe de machines virtuelles identiques nécessite une inscription automatique.
2. Vous pouvez procéder à l’inscription automatique de votre abonnement en exécutant les étapes suivantes : 
```Powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
3. Attendez environ 10 minutes pour que l’état soit « Inscrit ». Vous pouvez vérifier l’état en exécutant la commande suivante : 
```Powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```
4. **Azure Key Vault** : Créez un coffre de clés dans le même abonnement et la même région que le groupe identique, puis définissez la stratégie d’accès « EnabledForDiskEncryption » dans le coffre de clés à l’aide de son applet de commande PS. Vous pouvez également définir la stratégie à l’aide de l’interface utilisateur du coffre de clés dans le portail Azure : 
```Powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
5. Installez la dernière version d’[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest), qui contient les nouvelles commandes de chiffrement.
6. Installez la dernière version du kit [SDK Azure d’Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Voici les applets de commande ADE de groupe de machines virtuelles identiques pour activer ([Set](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1)) le chiffrement, récupérer ([Get](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) l’état du chiffrement et supprimer ([désactiver](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) le chiffrement sur une instance de groupe identique.

| Commande | Version |  Source  |
| ------------- |-------------| ------------|
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 ou ultérieur | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryptionStatus   | 3.4.0 ou ultérieur | AzureRM.Compute |
| Disable-AzureRmVmssDiskEncryption   | 3.4.0 ou ultérieur | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryption   | 3.4.0 ou ultérieur | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryption   | 3.4.0 ou ultérieur | AzureRM.Compute |
| Set-AzureRmVmssDiskEncryptionExtension   | 3.4.0 ou ultérieur | AzureRM.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scénarios pris en charge pour le chiffrement de disque
* Le chiffrement de groupes de machines virtuelles identiques est pris en charge uniquement pour les groupes identiques créés avec des disques managés, il ne l’est pas pour les groupes identiques de disques natifs (ou non managés).
* Le chiffrement de groupes de machines virtuelles identiques est pris en charge pour les volumes de données et de systèmes d’exploitation des groupes identiques Windows. La désactivation du chiffrement est prise en charge pour les volumes de données et de systèmes d’exploitation des groupes identiques Windows.
* Les opérations de mise à niveau et de réimageage des machines virtuelles de groupes identiques ne sont pas prises en charge dans la préversion actuelle.


### <a name="create-new-cluster-and-enable-disk-encryption"></a>Créer un cluster et activer le chiffrement de disque

Utilisez les commandes suivantes pour créer un cluster et activer le chiffrement de disque à l’aide du modèle Azure Resource Manager et d’un certificat auto-signé.

### <a name="log-in-to-azure"></a>Connexion à Azure 

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Utiliser le modèle personnalisé que vous possédez déjà 

Si vous avez besoin de créer un modèle personnalisé adapté à vos besoins, il est vivement recommandé de commencer par l’un des modèles disponibles dans les [exemples de modèles Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Suivez les instructions et explications ci-dessous pour [personnaliser votre modèle de cluster][customize-your-cluster-template].

Si vous disposez déjà d’un modèle personnalisé, vérifiez une nouvelle fois que les trois paramètres du modèle qui sont liés au certificat et le fichier de paramètres portent les noms indiqués ci-après et que les valeurs sont null comme suit.

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


```Powershell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```


```CLI

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
Suivez les étapes et les instructions pour [déployer l’application sur votre cluster](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications)


#### <a name="enable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set-created-above"></a>Activer le chiffrement de disque pour le groupe de machines virtuelles identiques de cluster Service Fabric créé précédemment
 
```Powershell

$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```CLI

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```


#### <a name="validate-if-disk-encryption-enabled-for-windows-virtual-machine-scale-set"></a>Validez si le chiffrement de disque est activé pour le groupe de machines virtuelles identiques Windows.
Obtenez l’état d’un groupe de machines virtuelles identiques entier ou d’une instance du groupe. Consultez les commandes ci-dessous.
De plus, l’utilisateur peut se connecter à une machine virtuelle du groupe identique et vérifier que les lecteurs sont chiffrés.

```Powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```CLI

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Désactiver le chiffrement de disque pour le groupe de machines virtuelles identiques de cluster Service Fabric 
La désactivation du chiffrement de disque s’applique à l’ensemble du groupe de machines virtuelles identiques et non par instance. 

```Powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Étapes suivantes
À ce stade, vous avez un cluster sécurisé avec l’activation/désactivation du chiffrement de disque pour un groupe de machines virtuelles identiques de cluster Service Fabric. À suivre, [Chiffrement de disque pour Linux](service-fabric-enable-azure-disk-encryption-linux.md) 

