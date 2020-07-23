---
title: Activer le chiffrement de bout en bout à l’aide du chiffrement sur l’hôte - Azure CLI - Disques managés
description: Utilisez le chiffrement sur l’hôte pour activer le chiffrement de bout en bout sur vos disques managés Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/10/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 705f9f3055d40d23c9ec5e24cfccfc0c96e114a5
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86235658"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-cli"></a>Activer le chiffrement de bout en bout à l’aide du chiffrement sur l’hôte - Azure CLI

Quand vous activez le chiffrement sur l’hôte, les données stockées sur l’hôte de machine virtuelle sont chiffrées au repos et les flux sont chiffrés dans le service de stockage. Pour obtenir des informations conceptuelles sur le chiffrement sur l’hôte ainsi que sur d’autres types de chiffrement de disques managés, consultez [Chiffrement sur l’hôte : chiffrement de bout en bout pour vos données de machine virtuelle](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Restrictions

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Régions prises en charge

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Tailles des machines virtuelles prises en charge

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Vous pouvez également rechercher les tailles de machine virtuelle programmatiquement. Pour savoir comment les récupérer programmatiquement, reportez-vous à la section [Rechercher les tailles de machine virtuelle prises en charge](#finding-supported-vm-sizes).

## <a name="prerequisites"></a>Prérequis

Pour pouvoir utiliser le chiffrement sur l’hôte pour vos machines virtuelles ou groupes de machines virtuelles identiques, vous devez activer la fonctionnalité sur votre abonnement. Envoyez un e-mail à encryptionAtHost@microsoft.com avec vos ID d’abonnement pour que la fonctionnalité soit activée pour vos abonnements.

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Créer un coffre de clés Azure Key Vault et un DiskEncryptionSet

Une fois la fonctionnalité activée, vous devez configurer un coffre de clés Azure Key Vault et un DiskEncryptionSet, si ce n’est déjà fait.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="enable-encryption-at-host-for-disks-attached-to-vm-and-virtual-machine-scale-sets"></a>Activer le chiffrement sur l’hôte pour les disques attachés à la machine virtuelle et aux groupes de machines virtuelles identiques

Vous pouvez activer le chiffrement sur l’hôte en définissant une nouvelle propriété EncryptionAtHost sous le profil securityProfile des machines virtuelles ou des groupes de machines virtuelles identiques à l’aide de la version d’API **2020-06-01** et ultérieures.

`"securityProfile": { "encryptionAtHost": "true" }`

## <a name="example-scripts"></a>Exemples de scripts

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-customer-managed-keys"></a>Activer le chiffrement sur l’hôte pour les disques attachés à une machine virtuelle avec des clés gérées par le client

Créez une machine virtuelle avec des disques managés à l’aide de l’URI de ressource du DiskEncryptionSet que vous avez créé.

Remplacez `<yourPassword>`, `<yourVMName>`, `<yourVMSize>`, `<yourDESName>`, `<yoursubscriptionID>`, `<yourResourceGroupName>` et `<yourRegion>`, puis exécutez le script.

```azurecli
az group deployment create -g <yourResourceGroupName> \
--template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithCMK.json" \
--parameters "virtualMachineName=<yourVMName>" "adminPassword=<yourPassword>" "vmSize=<yourVMSize>" "diskEncryptionSetId=/subscriptions/<yoursubscriptionID>/resourceGroups/<yourResourceGroupName>/providers/Microsoft.Compute/diskEncryptionSets/<yourDESName>" "region=<yourRegion>"
```

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-platform-managed-keys"></a>Activer le chiffrement sur l’hôte pour les disques attachés à une machine virtuelle avec des clés managées par la plateforme

Remplacez `<yourPassword>`, `<yourVMName>`, `<yourVMSize>`, `<yourResourceGroupName>` et `<yourRegion>`, puis exécutez le script.

```azurecli
az group deployment create -g <yourResourceGroupName> \
--template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithPMK.json" \
--parameters "virtualMachineName=<yourVMName>" "adminPassword=<yourPassword>" "vmSize=<yourVMSize>" "region=<yourRegion>"
```

## <a name="finding-supported-vm-sizes"></a>Recherche des tailles de machine virtuelle prises en charge

Les tailles de machine virtuelle héritées ne sont pas prises en charge. Vous pouvez trouver la liste des tailles de machines virtuelles prises en charge en :

Appelant l’[API des références SKU de ressource](https://docs.microsoft.com/rest/api/compute/resourceskus/list) et en vérifiant que la capacité `EncryptionAtHostSupported` est définie sur **True**.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

Ou en appelant l’applet de commande PowerShell [Get-AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0).

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé et configuré ces ressources, vous pouvez les utiliser pour sécuriser vos disques managés. Le lien suivant contient des exemples de scripts, chacun avec un scénario respectif, que vous pouvez utiliser pour sécuriser vos disques managés.

[Exemples de modèles Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
