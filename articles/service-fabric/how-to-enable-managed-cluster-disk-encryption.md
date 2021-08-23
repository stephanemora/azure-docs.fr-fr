---
title: Activer le chiffrement de disque pour les nœuds de cluster managé Service Fabric
description: Découvrez comment activer le chiffrement de disque pour les nœuds de cluster managé dans Windows à l’aide d’un modèle ARM.
ms.topic: how-to
ms.date: 5/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b10c5fbade903ec9eb5786fc33eaaddff212956
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284124"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-nodes"></a>Activer le chiffrement de disque pour les nœuds de cluster managé Service Fabric

Dans ce guide, vous allez apprendre à activer le chiffrement de disque sur les nœuds de cluster managé Service Fabric dans Windows, à l’aide de la fonctionnalité [Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md) pour les [groupes de machines virtuelles identiques](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md) avec des modèles Azure Resource Manager (ARM).

## <a name="register-for-azure-disk-encryption"></a>S’inscrire auprès d’Azure Disk Encryption

La préversion du chiffrement de disque des groupes de machines virtuelles identiques nécessite une inscription automatique. Exécutez la commande suivante :

```powershell
Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
```

Vérifiez l’état de l’inscription en exécutant :

```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
```

Lorsque l’état passe à *Enregistré*, vous êtes prêt à continuer.

## <a name="provision-a-key-vault-for-disk-encryption"></a>Provisionner un coffre de clés pour le chiffrement de disque

Azure Disk Encryption exige Azure Key Vault pour contrôler et gérer les clés et les secrets de chiffrement de disque. Votre coffre de clés et le cluster managé Service Fabric doivent résider dans le même abonnement et la même région Azure. Tant que ces exigences sont respectées, vous pouvez utiliser un coffre de clés nouveau ou existant en l’activant pour le chiffrement de disque.

### <a name="create-key-vault-with-disk-encryption-enabled"></a>Créer un coffre de clés avec le chiffrement de disque activé

Exécutez les commandes suivantes qui permettent de créer un coffre de clés pour le chiffrement de disque. Assurez-vous que la région de votre coffre de clés est [prise en charge pour les clusters managés Service Fabric](./faq-managed-cluster.yml#what-regions-are-supported-) et qu’il s’agit de la même région que votre cluster.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

New-AzResourceGroup -Name $resourceGroupName -Location eastus2 
New-AzKeyVault -ResourceGroupName $resourceGroupName -Name $keyvaultName -Location eastus2 -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

az keyvault create --resource-group $resourceGroupName --name $keyvaultName --enabled-for-disk-encryption
```

---

### <a name="update-existing-key-vault-to-enable-disk-encryption"></a>Mettre à jour un coffre de clés existant pour activer le chiffrement de disque

Exécutez les commandes permettant d’activer le chiffrement de disque pour un coffre de clés existant.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# ps 

Set-AzKeyVaultAccessPolicy -ResourceGroupName $resourceGroupName -VaultName $keyvaultName -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az keyvault update --name keyvaultName --enabled-for-disk-encryption 
```

---

## <a name="update-the-template-and-parameters-files-for-disk-encryption"></a>Mettre à jour le fichier de paramètres et le modèle pour le chiffrement de disque

L’étape suivante vous guide tout au long des modifications nécessaires du modèle pour activer le chiffrement de disque sur un [cluster managé existant](tutorial-managed-cluster-deploy.md). Vous pouvez aussi déployer un nouveau cluster managé Service Fabric avec le chiffrement de disque activé, à l’aide de ce modèle : https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption

1. Ajoutez les paramètres suivants au modèle, en substituant votre propre abonnement, nom de groupe de ressources et nom de coffre de clés sous `keyVaultResourceId` :

```json
"parameters": { 
…
    "keyVaultResourceId": { 
        "type": "string", 
        "defaultValue": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>", 
        "metadata": { 
            "description": "Full resource id of the Key Vault used for disk encryption." 
        } 
    },
    "volumeType": { 
        "type": "string", 
        "defaultValue": "All", 
        "metadata": { 
            "description": "Type of the volume OS or Data to perform encryption operation" 
        }
    }
}, 
```

2. Ensuite, ajoutez l’extension de machine virtuelle `AzureDiskEncryption` aux types de nœuds du cluster managé dans le modèle :

```json
"properties": { 
    "vmExtensions": [ 
        { 
            "name": "AzureDiskEncryption", 
            "properties": { 
                "publisher": "Microsoft.Azure.Security", 
                "type": "AzureDiskEncryption", 
                "typeHandlerVersion": "2.2", 
                "autoUpgradeMinorVersion": true, 
                "settings": {                     
                    "EncryptionOperation": "EnableEncryption", 
                    "KeyVaultURL": "[reference(parameters('keyVaultResourceId'),'2016-10-01').vaultUri]", 
                    "KeyVaultResourceId": "[parameters('keyVaultResourceID')]",
                    "VolumeType": "[parameters('volumeType')]" 
                } 
            } 
        } 
    ] 
} 
```

3. Enfin, mettez à jour le fichier des paramètres, en substituant votre propre abonnement, groupe de ressources et nom de coffre de clés dans *keyVaultResourceId* :

```json
"parameters": { 
    … 
    "keyVaultResourceId": { 
        "value": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>" 
    },   
    "volumeType": { 
        "value": "All" 
    }    
} 
```

## <a name="deploy-and-verify-the-changes"></a>Déployer et vérifier les modifications

Lorsque vous êtes prêt, déployez les modifications pour activer le chiffrement de disque sur votre cluster managé.

```powershell
$clusterName = "<clustername>" 

New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\template_diskEncryption.json -TemplateParameterFile \.parameters_diskEncryption.json -Debug -Verbose 
```

Vous pouvez vérifier l’état de chiffrement du disque sur un groupe identique sous-jacent du type de nœud au moyen de la commande `Get-AzVmssDiskEncryption`. Vous devez tout d’abord rechercher le nom du groupe de ressources de prise en charge de votre cluster managé (contenant le réseau virtuel sous-jacent, l’équilibreur de charge, l’adresse IP publique, le groupe de sécurité réseau, le ou les groupes identiques et les comptes de stockage). Veillez à modifier `VmssName` pour le nom du type de nœud de cluster que vous souhaitez vérifier (comme spécifié dans votre modèle de déploiement).

```powershell
$VmssName = "NT1"
$supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName).ClusterId
Get-AzVmssDiskEncryption -ResourceGroupName $supportResourceGroupName -VMScaleSetName $VmssName
```

Le résultat doit ressembler à ceci :

```console
ResourceGroupName            : SFC_########-####-####-####-############
VmScaleSetName               : NT1
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="next-steps"></a>Étapes suivantes

[Exemple : Cluster managé Service Fabric de référence Standard, 1 type de nœud avec chiffrement de disque activé](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[Azure Disk Encryption pour les machines virtuelles Windows](../virtual-machines/windows/disk-encryption-overview.md)

[Chiffrer des groupes de machines virtuelles identiques avec Azure Resource Manager](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)