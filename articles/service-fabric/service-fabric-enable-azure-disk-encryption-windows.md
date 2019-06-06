---
title: Activer le chiffrement de disque pour les clusters Azure Service Fabric Windows | Microsoft Docs
description: Cet article décrit comment activer le chiffrement de disque pour les nœuds de cluster Azure Service Fabric à l’aide d’Azure Key Vault dans Azure Resource Manager.
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
ms.openlocfilehash: c31fc43729bcb58c755959db0c8bc5185b8197f4
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66471418"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Activer le chiffrement de disque pour les nœuds de cluster Azure Service Fabric dans Windows 
> [!div class="op_single_selector"]
> * [Chiffrement de disque pour Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Chiffrement de disque pour Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Dans ce didacticiel, vous allez apprendre à activer le chiffrement de disque sur les nœuds de cluster Service Fabric dans Windows. Vous devez suivre ces étapes pour chacun des types de nœuds et machines virtuelles identiques. Pour chiffrer les nœuds, nous allons utiliser la fonctionnalité de chiffrement de disque Azure sur les machines virtuelles identiques.

Ce guide couvre les rubriques suivantes :

* Concepts clés à connaître lorsque l’activation du chiffrement disque sur l’échelle de machines virtuelles du cluster Service Fabric définit dans Windows.
* Étapes à suivre avant d’activer le chiffrement de disque sur Service Fabric nœuds de cluster dans Windows.
* Étapes à suivre pour activer le chiffrement de disque sur les nœuds de cluster Service Fabric dans Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Conditions préalables

**Inscription automatique** 

Version d’évaluation de chiffrement de disque pour les machines virtuelles identiques nécessite l’inscription automatique. Procédez comme suit : 

1. Tout d’abord, exécutez la commande suivante :
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Attendez environ 10 minutes jusqu'à ce que l’état lit *Registered*. Vous pouvez vérifier l’état en exécutant la commande suivante : 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. Créez un coffre de clés dans le même abonnement et la même région que le groupe identique, puis sélectionnez le **EnabledForDiskEncryption** stratégie sur le coffre de clés d’accès à l’aide de son applet de commande PowerShell. Vous pouvez également définir la stratégie à l’aide de l’interface utilisateur de coffre de clé dans le portail Azure avec la commande suivante :
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Installez la dernière version de la [Azure CLI](/cli/azure/install-azure-cli), qui a les nouvelles commandes de chiffrement.
3. Installez la dernière version de la [Kit de développement à partir d’Azure PowerShell](https://github.com/Azure/azure-powershell/releases) mise en production. Voici les machines virtuelles identiques Azure Disk Encryption des applets de commande pour activer ([définir](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) chiffrement, récupérer ([obtenir](/powershell/module/az.compute/get-azvmssvmdiskencryption)) état de chiffrement et remove ([désactiver](/powershell/module/az.compute/disable-azvmssdiskencryption)) le chiffrement sur l’échelle défini instance.

| Commande | Version |  Source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 ou ultérieure | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 ou ultérieure | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 ou ultérieure | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 ou ultérieure | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 ou ultérieure | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 ou ultérieure | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scénarios pris en charge pour le chiffrement de disque
* Chiffrement pour les machines virtuelles identiques est pris en charge uniquement pour les groupes identiques créés avec des disques gérés. Il ne l’est pas pour les groupes identiques de disques natifs (ou non managés).
* Le chiffrement est pris en charge pour le système d’exploitation et les volumes de données dans l’échelle de machine virtuelle définit dans Windows. Désactiver le chiffrement est également pris en charge pour le système d’exploitation et les volumes de données pour la mise à l’échelle de machine virtuelle définit dans Windows.
* Opérations de mise à niveau et de réinitialisation des machines virtuelles pour les machines virtuelles identiques ne sont pas pris en charge dans la version préliminaire actuelle.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Créer un nouveau cluster et activer le chiffrement de disque

Utilisez les commandes suivantes pour créer un cluster et activer le chiffrement de disque à l’aide d’un modèle Azure Resource Manager et d’un certificat auto-signé.

### <a name="sign-in-to-azure"></a>Connexion à Azure 
Connectez-vous avec les commandes suivantes :
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Utiliser le modèle personnalisé que vous possédez déjà 

Si vous avez besoin créer un modèle personnalisé pour répondre à vos besoins, nous vous recommandons de commencer avec un des modèles qui sont disponibles sur le [exemples de modèles de création de cluster Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) page. Pour [personnaliser votre modèle de cluster] [ customize-your-cluster-template] section, consultez les conseils suivants.

Si vous disposez déjà d’un modèle personnalisé, vérifiez que tous les trois paramètres d’associées aux certificats dans le modèle et le fichier de paramètres sont nommés comme suit et que les valeurs sont null comme suit :

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Déployer une application sur un cluster Service Fabric dans Windows
Pour déployer une application sur votre cluster, suivez les étapes et les instructions données à [déployer et supprimer des applications à l’aide de PowerShell](service-fabric-deploy-remove-applications.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Activer le chiffrement de disque pour les jeux de mise à l’échelle de machine virtuelle créée précédemment

Pour activer le chiffrement de disque pour l’échelle de machine virtuelle définit que vous avez créé à travers les étapes précédentes, exécutez-les les commandes suivantes :
 
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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Valider si le chiffrement de disque est activé pour les machines virtuelles identiques dans Windows
Obtenir l’état d’un jeu de mise à l’échelle de machine virtuelle ou n’importe quelle instance dans un groupe identique en exécutant les commandes suivantes.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


En outre, vous pouvez vous connecter à l’ensemble d’échelle de machine virtuelle et vérifiez que les disques sont chiffrés.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Désactiver le chiffrement de disque pour un machine virtuelle identique dans un cluster Service Fabric 
Désactiver le chiffrement de disque pour les machines virtuelles identiques en exécutant les commandes suivantes. Notez que la désactivation du chiffrement de disque s’applique à l’ensemble d’échelle de machine virtuelle complète et pas une instance individuelle.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Étapes suivantes
À ce stade, vous devez avoir un cluster sécurisé et savoir comment activer et désactiver le chiffrement de disque pour les nœuds de cluster Service Fabric et les machines virtuelles identiques. Pour obtenir des recommandations similaires sur les nœuds de cluster Service Fabric sous Linux, consultez [Disk Encryption pour Linux](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
