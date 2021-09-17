---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2021
ms.author: rogarana
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 23b6c4dddd839351e7eff49b6dea4c42fe6452d8
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122825029"
---
1. Vérifiez que vous avez installé la dernière version [d’Azure CLI](/cli/azure/install-az-cli2) et que vous êtes connecté à un compte Azure avec [az login](/cli/azure/reference-index).

1. Créer une instance Azure Key Vault et une clé de chiffrement.

    Quand vous créez l’instance Key Vault, vous devez activer la protection de purge. La protection de purge garantit qu’une clé supprimée ne peut pas être supprimée définitivement tant que la période de rétention n’est pas écoulée. Ces paramètres vous protègent contre la perte de données en raison d’une suppression accidentelle. Ces paramètres sont obligatoires lors de l’utilisation d’un coffre de clés Key Vault pour le chiffrement des disques managés.

    > [!IMPORTANT]
    > N’utilisez pas de casse mixte pour la région. Si vous le faites, vous risquez de rencontrer des problèmes lors de l’attribution de disques supplémentaires à la ressource dans le Portail Azure.

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName \
    -g $rgName \
    -l $location \
    --enable-purge-protection true 
                       
    az keyvault key create --vault-name $keyVaultName \
    -n $keyName \
    --protection software
    ```

1.    Créer une instance de DiskEncryptionSet. Vous pouvez affecter la valeur true à Enable-Auto-Key-rotation pour activer la rotation automatique de la clé. Lorsque vous activez la rotation automatique, le système met automatiquement à jour tous les disques managés, les captures instantanées et les images référençant le jeu de chiffrement de disque pour utiliser la nouvelle version de la clé dans un délai d’une heure.
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName 
        -l $location \
        -g $rgName \
        --source-vault $keyVaultId \
        --key-url $keyVaultKeyUrl \
        --enable-auto-key-rotation false
        ```

1.    Accorder à la ressource DiskEncryptionSet l’accès au coffre de clés. 

        > [!NOTE]
        > La création de l’identité de votre DiskEncryptionSet dans votre annuaire Azure Active Directory peut prendre quelques minutes. Si vous recevez une erreur comme « Impossible de trouver l’objet Active Directory » lors de l’exécution de la commande suivante, attendez quelques minutes et réessayez.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName \
        -g $rgName \
        --object-id $desIdentity \
        --key-permissions wrapkey unwrapkey get
        ```
