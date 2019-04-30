---
title: Configurer les clés de gérée par le client pour le chiffrement de stockage Azure à partir d’Azure CLI
description: Découvrez comment utiliser Azure CLI pour configurer les clés de gérée par le client pour le chiffrement de stockage Azure. Clés gérées par le client permettent de créer, faire pivoter, désactiver et révoquer des contrôles d’accès.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: dc9f660ddf7cd003ac113725a9bf7b66189807b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61593591"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Configurer les clés de gérée par le client pour le chiffrement de stockage Azure à partir d’Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Cet article explique comment configurer un coffre de clés avec des clés gérées par le client à l’aide d’Azure CLI.

## <a name="assign-an-identity-to-the-storage-account"></a>Affecter une identité pour le compte de stockage

Pour activer les clés gérées par le client pour votre compte de stockage, vous devez tout d’abord affecter une identité gérée attribué par le système au compte de stockage. Vous utiliserez cette identité gérée pour accorder les autorisations de compte de stockage pour accéder au coffre de clés.

Pour attribuer une identité gérée à l’aide d’Azure CLI, appelez [mise à jour de compte de stockage az](/cli/azure/storage/account#az-storage-account-update). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Pour plus d’informations sur la configuration des identités gérées attribué par le système avec Azure CLI, consultez [configurer managed identités pour les ressources Azure sur une machine virtuelle Azure à l’aide d’Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Créer un coffre de clés

Le coffre de clés que vous utilisez pour stocker les clés gérées par le client pour le chiffrement du stockage Azure doit avoir deux paramètres de protection de clé est activées, **suppression réversible** et **ne pas vider**. Pour créer un coffre de clés à l’aide de PowerShell ou Azure CLI avec ces paramètres sont activés, exécutez les commandes suivantes. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs. 

Pour créer un coffre de clés à l’aide d’Azure CLI, appelez [créer az keyvault](/cli/azure/keyvault#az-keyvault-create). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>Configurer la stratégie d’accès de coffre de clés

Ensuite, configurez la stratégie d’accès pour le coffre de clés afin que le compte de stockage dispose des autorisations pour y accéder. Dans cette étape, vous allez utiliser l’identité gérée que vous avez précédemment affecté au compte de stockage.

Pour définir la stratégie d’accès pour le coffre de clés, appelez [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    -name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Créer une clé

Ensuite, créez une clé dans le coffre de clés. Pour créer une clé, appelez [créer de clé de coffre de clés az](/cli/azure/keyvault/key#az-keyvault-key-create). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurer le chiffrement avec des clés gérées par le client

Par défaut, le chiffrement du stockage Azure utilise des clés gérées par Microsoft. Configurer votre compte de stockage Azure pour les clés gérées par le client et spécifier la clé à associer au compte de stockage.

Pour mettre à jour les paramètres de chiffrement du compte de stockage, appelez [mise à jour de compte de stockage az](/cli/azure/storage/account#az-storage-account-update). Cet exemple exécute également des requêtes pour le coffre de clés URI et la version de la clé, les deux les valeurs qui sont nécessaires pour associer la clé du compte de stockage. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update 
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>Mise à jour la version de la clé

Lorsque vous créez une nouvelle version d’une clé, vous devez mettre à jour le compte de stockage pour utiliser la nouvelle version. Tout d’abord, pour le coffre de clés URI de requête en appelant [show de coffre de clés az](/cli/azure/keyvault#az-keyvault-show)et pour la version de la clé en appelant [az keyvault clé liste versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Appelez ensuite [mise à jour de compte de stockage az](/cli/azure/storage/account#az-storage-account-update) pour mettre à jour les paramètres de chiffrement du compte de stockage pour utiliser la nouvelle version de la clé, comme indiqué dans la section précédente.

## <a name="next-steps"></a>Étapes suivantes

- [Chiffrement du stockage Azure pour les données au repos](storage-service-encryption.md) 
- [Qu’est Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
