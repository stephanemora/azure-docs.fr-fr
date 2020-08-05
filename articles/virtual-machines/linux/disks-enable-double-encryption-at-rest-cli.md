---
title: Activez le double chiffrement au repos - Azure CLI - Disques managés
description: Activez le double chiffrement au repos pour les données de vos disques managés à l’aide d’Azure CLI.
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 25f4f99d2bd80034d2f337aa649417d2d06026be
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082526"
---
# <a name="azure-cli---enable-double-encryption-at-rest-on-your-managed-disks"></a>Azure CLI - Activer le double chiffrement au repos sur vos disques managés

Le Stockage sur disque Azure prend en charge le double chiffrement au repos pour les disques managés. Pour obtenir des informations conceptuelles sur le double chiffrement au repos ainsi que d’autres types de chiffrement de disque managé, consultez la section [Double chiffrement au repos](disk-encryption.md#double-encryption-at-rest) de notre article sur le chiffrement de disque.

## <a name="supported-regions"></a>Régions prises en charge

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="prerequisites"></a>Prérequis

Installez la dernière version d’[Azure CLI](/cli/azure/install-az-cli2) et connectez-vous à un compte Azure avec [az login](/cli/azure/reference-index).

## <a name="getting-started"></a>Prise en main

1. Créer une instance Azure Key Vault et une clé de chiffrement.

    Lorsque vous créez l’instance Key Vault, vous devez activer la suppression réversible et la protection de purge. La suppression réversible permet de s’assurer que Key Vault contient une clé supprimée pour une période de rétention donnée (90 jours par défaut). La protection de purge garantit qu’une clé supprimée ne peut pas être supprimée définitivement tant que la période de rétention n’est pas écoulée. Ces paramètres vous protègent contre la perte de données en raison d’une suppression accidentelle. Ces paramètres sont obligatoires lors de l’utilisation d’un coffre de clés Key Vault pour le chiffrement des disques managés.

    
    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    Créez un DiskEncryptionSet avec encryptionType défini sur EncryptionAtRestWithPlatformAndCustomerKeys. Utilisez l’API version **2020-05-01** dans le modèle Azure Resource Manager (ARM). 
    
        ```azurecli
        az group deployment create -g $rgName \
       --template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" \
        --parameters "diskEncryptionSetName=$diskEncryptionSetName" "encryptionType=EncryptionAtRestWithPlatformAndCustomerKeys" "keyVaultId=$keyVaultId" "keyVaultKeyUrl=$keyVaultKeyUrl" "region=$location"
        ```

1.    Accorder à la ressource DiskEncryptionSet l’accès au coffre de clés. 

        > [!NOTE]
        > La création de l’identité de votre DiskEncryptionSet dans votre annuaire Azure Active Directory peut prendre quelques minutes. Si vous recevez une erreur comme « Impossible de trouver l’objet Active Directory » lors de l’exécution de la commande suivante, attendez quelques minutes et réessayez.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)

        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé et configuré ces ressources, vous pouvez les utiliser pour sécuriser vos disques managés. Les liens suivants contiennent des exemples de scripts, chacun avec un scénario respectif, que vous pouvez utiliser pour sécuriser vos disques managés.

- [Exemples de modèles Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Activer les clés gérées par le client avec le chiffrement côté serveur – Exmples](disks-enable-customer-managed-keys-cli.md#examples)