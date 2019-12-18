---
title: Utiliser Azure CLI pour configurer des clés gérées par le client
titleSuffix: Azure Storage
description: Découvrez comment utiliser Azure CLI afin de configurer des clés gérées par le client avec Azure Key Vault pour le chiffrement du stockage Azure. Les clés gérées par le client vous permettent de créer, faire pivoter, désactiver et révoquer des contrôles d’accès.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 9b9ec315954f5916339bb006cb020acc28886839
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895321"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Configurer des clés gérées par le client avec Azure Key Vault à l’aide d’Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Cet article explique comment configurer un coffre de clés Azure avec des clés gérées par le client à l’aide d’Azure CLI. Pour en savoir plus sur la création d’un coffre de clés à l’aide d’Azure CLI, consultez [Démarrage rapide : Définir et récupérer un secret depuis Azure Key Vault à l’aide d’Azure CLI](../../key-vault/quick-create-cli.md).

> [!IMPORTANT]
> Pour utiliser des clés gérées par le client avec le chiffrement Stockage Azure, deux propriétés doivent être configurées sur le coffre de clés, **Suppression réversible** et **Ne pas vider**. Ces propriétés ne sont pas activées par défaut. Pour activer ces propriétés, utilisez PowerShell ou Azure CLI.
> Seules les clés RSA et la taille de clé 2048 sont prises en charge.

## <a name="assign-an-identity-to-the-storage-account"></a>Affecter une identité au compte de stockage

Pour activer des clés gérées par le client pour votre compte de stockage, affectez tout d’abord à votre compte de stockage une identité managée affectée au système. Cette identité managée vous sera utile pour autoriser le compte de stockage à accéder au coffre de clés.

Pour attribuer une identité managée à l’aide d’Azure CLI, appelez [az storage account update](/cli/azure/storage/account#az-storage-account-update). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Pour plus d’informations sur la configuration d’identités managées affectées à un système avec Azure CLI, consultez [Configurer des identités managées pour ressources Azure sur une machine virtuelle en utilisant Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Créer un coffre de clés

Le coffre de clés que vous utilisez pour stocker des clés gérées par le client pour le chiffrement du stockage Azure doit disposer de deux paramètres de protection de clés, **Suppression réversible** et **Ne pas vider**. Pour créer un coffre de clés à l’aide de PowerShell ou Azure CLI avec ces paramètres sont activés, exécutez les commandes suivantes. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs. 

Pour créer un coffre de clés avec Azure CLI, appelez [az keyvault create](/cli/azure/keyvault#az-keyvault-create). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>Configurer la stratégie d’accès au coffre de clés

Configurez ensuite la stratégie d’accès au coffre de clés de sorte que le compte de stockage dispose des autorisations nécessaires pour y accéder. À cette étape, vous utiliserez l’identité managée que vous avez précédemment affectée au compte de stockage.

Pour définir la stratégie d’accès au coffre de clés, appelez [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
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

Créez ensuite une clé dans le coffre de clés. Pour créer une clé, appelez [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurer le chiffrement avec les clés gérées par le client

Par défaut, le chiffrement du stockage Azure utilise des clés gérées par Microsoft. À cette étape, configurez votre compte de stockage Azure pour utiliser les clés gérées par le client et spécifiez la clé à lui associer.

Pour mettre à jour les paramètres de chiffrement du compte de stockage, appelez [az storage account update](/cli/azure/storage/account#az-storage-account-update). Cet exemple exécute également une requête pour l’URI du coffre de clés et la dernière version de la clé, ces deux valeurs étant nécessaires pour associer la clé au compte de stockage. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update 
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>Mettre à jour la version de la clé

Lors de la création d’une nouvelle version d’une clé, vous devez mettre à jour le compte de stockage afin qu’il utilise cette nouvelle version. Tout d’abord, lancez une requête pour l’URI du coffre de clés en appelant [az keyvault show](/cli/azure/keyvault#az-keyvault-show)et pour la version de la clé en appelant [az keyvault key list-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Appelez ensuite [az storage account update](/cli/azure/storage/account#az-storage-account-update) pour mettre à jour les paramètres de chiffrement du compte de stockage afin d’utiliser la nouvelle version de la clé, comme indiqué dans la section précédente.

## <a name="next-steps"></a>Étapes suivantes

- [Chiffrement du stockage Azure pour les données au repos](storage-service-encryption.md) 
- [Qu’est-ce qu’Azure Key Vault ?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
