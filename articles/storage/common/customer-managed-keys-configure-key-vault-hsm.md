---
title: Configurer le chiffrement avec des clés gérées par le client stockées dans un module HSM géré par Azure Key Vault Azure (préversion)
titleSuffix: Azure Storage
description: Apprenez à configurer le chiffrement du service Stockage Azure avec des clés gérées par le client stockées dans un module HSM géré par Azure Key Vault Azure (préversion) à l'aide d'Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: f340ac18cb74523d64f4dbf8d6ae1d6f4559582a
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111411878"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>Configurer le chiffrement avec des clés gérées par le client stockées dans un module HSM géré par Azure Key Vault Azure (préversion)

Le stockage Azure chiffre toutes les données dans un compte de stockage au repos. Par défaut, les données sont chiffrées avec des clés managées par Microsoft Pour un contrôle supplémentaire des clés de chiffrement, vous pouvez gérer vos propres clés. Les clés gérées par le client doivent être stockées dans Azure Key Vault ou dans un module de sécurité matériel (HSM) géré par Azure Key Vault Azure (préversion). Un module HSM géré par Azure Key Vault est un module HSM certifié FIPS 140-2 de niveau 3.

Cet article explique comment configurer le chiffrement avec des clés gérées par le client stockées dans un module HSM géré à l'aide d'Azure CLI. Pour savoir comment configurer le chiffrement avec des clés gérées par le client stockées dans un coffre de clés, consultez [Configurer le chiffrement avec des clés gérées par le client stockées dans Azure Key Vault](customer-managed-keys-configure-key-vault.md).

> [!IMPORTANT]
>
> Le chiffrement avec des clés gérées par le client stockées dans Azure Key Vault Managed HSM est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure disponibles en version bêta, en préversion ou qui ne sont pas encore en phase de disponibilité générale.
>
> Azure Key Vault et le module HSM géré par Azure Key Vault prennent en charge les mêmes API et interfaces de gestion pour la configuration.

## <a name="assign-an-identity-to-the-storage-account"></a>Affecter une identité au compte de stockage

Tout d'abord, attribuez une identité managée affectée par le système au compte de stockage. Cette identité managée vous sera utile pour autoriser le compte de stockage à accéder au module HSM géré. Pour en savoir plus sur les identités managées affectées par le système, consultez la section [Que sont les identités managées pour les ressources Azure ?](../../active-directory/managed-identities-azure-resources/overview.md).

Pour attribuer une identité managée à l’aide d’Azure CLI, appelez [az storage account update](/cli/azure/storage/account#az_storage_account_update). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>Attribuer un rôle au compte de stockage pour l'accès au module HSM géré

Ensuite, attribuez le rôle **Utilisateur de chiffrement du service cryptographique du module HSM géré** à l'identité managée du compte de stockage afin que celui-ci dispose d'autorisations sur le module HSM géré. Microsoft vous recommande d'étendre l'attribution de rôle au niveau de la clé individuelle afin d'octroyer le moins de privilèges possible à l'identité managée.

Pour créer l'attribution de rôle du compte de stockage, appelez [az key vault role assignment create](/cli/azure/role/assignment#az_role_assignment_create). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs.
  
```azurecli
storage_account_principal = $(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault role assignment create \
    --hsm-name <hsm-name> \
    --role "Managed HSM Crypto Service Encryption User" \
    --assignee $storage_account_principal \
    --scope /keys/<key-name>
```

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>Configurer le chiffrement avec une clé dans le module HSM géré

Enfin, configurez le chiffrement du service Stockage Azure avec des clés gérées par le client pour utiliser une clé stockée dans le module HSM géré. Les types de clés pris en charge incluent les clés RSA-HSM de tailles 2048, 3072 et 4096. Pour savoir comment créer une clé dans un HSM managé, consultez [Créer une clé HSM](../../key-vault/managed-hsm/key-management.md#create-an-hsm-key).

Installez Azure CLI 2.12.0 ou version ultérieure pour configurer le chiffrement de manière à utiliser une clé gérée par le client dans un module HSM géré. Pour plus d’informations, consultez la rubrique [Installation de l’interface de ligne de commande Azure (CLI)](/cli/azure/install-azure-cli).

Pour mettre à jour automatiquement la version d’une clé gérée par le client, omettez la version de la clé quand vous configurez le chiffrement avec les clés gérées par le client pour le compte de stockage. Pour mettre à jour les paramètres de chiffrement du compte de stockage, appelez [az storage account update](/cli/azure/storage/account#az_storage_account_update), comme illustré dans l’exemple suivant. Incluez le paramètre `--encryption-key-source parameter` et définissez-le sur `Microsoft.Keyvault` afin d'activer les clés gérées par le client pour le compte. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs.

```azurecli
hsmurl = $(az keyvault show \
    --hsm-name <hsm-name> \
    --query properties.hsmUri \
    --output tsv)

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Pour une mise à jour manuelle de la version d'une clé gérée par le client, incluez la version de la clé lorsque vous configurez le chiffrement pour le compte de stockage :

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Quand vous mettez à jour manuellement la version de clé, vous devez mettre à jour les paramètres de chiffrement du compte de stockage afin d’utiliser la nouvelle version. Tout d’abord, lancez une requête pour l’URI du coffre de clés en appelant [az keyvault show](/cli/azure/keyvault#az_keyvault_show)et pour la version de la clé en appelant [az keyvault key list-versions](/cli/azure/keyvault/key#az_keyvault_key_list_versions). Appelez ensuite [az storage account update](/cli/azure/storage/account#az_storage_account_update) pour mettre à jour les paramètres de chiffrement du compte de stockage afin d’utiliser la nouvelle version de la clé, comme indiqué dans l’exemple précédent.

## <a name="next-steps"></a>Étapes suivantes

- [Chiffrement du stockage Azure pour les données au repos](storage-service-encryption.md)
- [Clés gérées par le client pour le chiffrement du Stockage Azure](customer-managed-keys-overview.md)
