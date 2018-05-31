---
title: Chiffrer des disques dans des groupes identiques Azure avec Azure CLI | Microsoft Docs
description: Découvrez comment utiliser Azure CLI 2.0 pour chiffrer des instances de machine virtuelle et des disques attachés dans un groupe de machines virtuelles identiques Linux.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: iainfou
ms.openlocfilehash: 22d3c763317def137b4e0beb155f28585d7c6ae1
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32776412"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli-20-preview"></a>Chiffrer des disques de données attachés et de systèmes d’exploitation dans un groupe de machines virtuelles identiques avec Azure CLI 2.0 (préversion)

Conçue pour protéger les données au repos, Azure Disk Encryption (ADE) est une technologie de chiffrement standard prise en charge par les groupes de machines virtuelles identiques. Vous pouvez activer le chiffrement pour des groupes de machines virtuelles identiques Linux et Windows. Pour plus d’informations, consultez [Azure Disk Encryption pour Linux et Windows](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  Azure Disk Encryption pour les groupes de machines virtuelles identiques est actuellement en préversion publique, disponible dans toutes les régions publiques Azure.

Azure Disk Encryption est pris en charge :
- Pour les groupes identiques créés avec des disques managés, et non pris en charge pour les groupes identiques de disques natifs (ou non managés).
- Pour les volumes de données et de systèmes d’exploitation dans les groupes identiques Windows. La désactivation du chiffrement est prise en charge pour les volumes de données et de systèmes d’exploitation des groupes identiques Windows.
- Pour les volumes de données dans les groupes identiques Linux. Le chiffrement de disque de systèmes d’exploitation n’est PAS pris en charge dans la préversion actuelle pour les groupes identiques Linux.

Les opérations de mise à niveau et de réinitialisation des machines virtuelles d’un groupe identique ne sont pas prises en charge dans la préversion actuelle. L’utilisation de la préversion d’Azure Disk Encryption pour les groupes de machines virtuelles identiques est uniquement recommandée dans les environnements de test. Dans la préversion, n’activez pas le chiffrement de disque dans les environnements de production dans lesquels vous devez mettre à niveau une image de système d’exploitation dans un groupe identique chiffré.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.31 ou ultérieure pour suivre ce tutoriel. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="register-for-disk-encryption-preview"></a>S’inscrire à la fonctionnalité de chiffrement de disque en préversion

Pour utiliser la préversion d’Azure Disk Encryption pour les groupes de machines virtuelles identiques, vous devez inscrire votre abonnement avec [az feature register](/cli/azure/feature#az_feature_register). Effectuez uniquement les étapes suivantes quand vous utilisez la fonctionnalité de chiffrement de disque en préversion pour la première fois :

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

La demande d’inscription peut prendre jusqu’à 10 minutes pour aboutir. Pour vérifier l’état de l’inscription, utilisez [az feature show](/cli/azure/feature#az_feature_show). Quand `State` indique *Inscrit*, réinscrivez le fournisseur *Mirosoft.Compute* avec [az provider register](/cli/azure/provider#az_provider_register) :

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="create-a-scale-set"></a>Créer un groupe identique

Pour pouvoir créer un groupe identique, vous devez créer un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Créez à présent un groupe de machines virtuelles identiques avec [az vmss create](/cli/azure/vmss#az_vmss_create). L’exemple suivant crée un groupe identique nommé *myScaleSet* qui est définit pour une mise à jour automatique lorsque des modifications sont appliquées, et qui génère des clés SSH s’il n’en existe pas dans *~/.ssh/id_rsa*. Un disque de données de 32 Go est attaché à chaque instance de machine virtuelle, et [l’extension de script personnalisé](../virtual-machines/linux/extensions-customscript.md) Azure est utilisée pour préparer les disques de données avec [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) :

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

La création et la configuration des l’ensemble des ressources et des machines virtuelles du groupe identique prennent quelques minutes.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Créer un coffre de clés Azure activé pour le chiffrement de disque

Un coffre de clés Azure peut stocker des clés, des clés secrètes ou des mots de passe vous permettant de les implémenter en toute sécurité dans vos applications et services. Les clés de chiffrement sont stockées dans le coffre de clés Azure à l’aide d’une protection logicielle, mais vous pouvez importer ou générer vos clés dans des modules de sécurité matériels (HSM) certifiés conformes aux normes FIPS 140-2 de niveau 2. Ces clés de chiffrement servent à chiffrer et à déchiffrer les disques virtuels connectés à votre machine virtuelle. Vous gardez le contrôle de ces clés de chiffrement et pouvez effectuer un audit de leur utilisation.

Définissez un nom de coffre de clés (*keyvault_name*) unique. Créez ensuite un coffre de clés avec [az keyvault create](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) dans le même abonnement et la même région que le groupe identique, puis définissez la stratégie d’accès *--enabled-for-disk-encryption*.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Utiliser un coffre de clés existant

Cette étape est requise uniquement si vous souhaitez utiliser un coffre de clés existant avec le chiffrement de disque. Ignorez cette étape si vous avez créé un coffre de clés dans la section précédente.

Définissez un nom de coffre de clés (*keyvault_name*) unique. Mettez ensuite à jour votre coffre de clés avec [az keyvault update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update), puis définissez la stratégie d’accès *--enabled-for-disk-encryption*.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Activer le chiffrement

Pour chiffrer des instances de machine virtuelle dans un groupe identique, vous devez d’abord obtenir certaines informations sur l’ID de ressource du coffre de clés avec [az keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Les variables obtenues sont ensuite utilisées pour démarrer le processus de chiffrement avec [az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable) :

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

Le démarrage du processus de chiffrement peut prendre une ou deux minutes.

Si la stratégie de mise à niveau sur le groupe identique créé précédemment a la valeur *automatic*, les instances de machine virtuelle démarrent automatiquement le processus de chiffrement. Sur les groupes identiques où la stratégie de mise à niveau est en mode manuel, démarrez la stratégie de chiffrement sur les instances de machine virtuelle avec [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances).

## <a name="check-encryption-progress"></a>Vérifier la progression du chiffrement

Pour vérifier l’état du chiffrement de disque, utilisez [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show) :

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Une fois les instances de machine virtuelle chiffrées, le code d’état indique *EncryptionState/encrypted* comme dans l’exemple de sortie suivant :

```bash
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Désactiver le chiffrement

Si vous ne souhaitez plus utiliser les disques d’instances de machine virtuelle chiffrés, désactivez le chiffrement avec [az vmss encryption disable](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable), comme ceci :

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez utilisé Azure CLI 2.0 pour chiffrer un groupe de machines virtuelles identiques. Vous pouvez également utiliser [Azure PowerShell](virtual-machine-scale-sets-encrypt-disks-ps.md) ou des modèles pour [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) ou [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).

Un exemple de fichier de traitement complet de chiffrement de disque de données d’un groupe identique Linux est disponible [ici](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). Cet exemple crée un groupe de ressources, un groupe identique Linux, monte un disque de données de 5 Go et chiffre le groupe de machines virtuelles identiques.