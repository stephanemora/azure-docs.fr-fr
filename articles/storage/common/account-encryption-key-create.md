---
title: Créer un compte qui prend en charge les clés gérées par le client pour les tables et les files d’attente
titleSuffix: Azure Storage
description: Découvrez comment créer un compte de stockage prenant en charge la configuration des clés gérées par le client pour les tables et les files d’attente. Utilisez Azure CLI ou un modèle Azure Resource Manager pour créer un compte de stockage qui s’appuie sur la clé de chiffrement du compte pour le chiffrement de Stockage Azure. Vous pouvez ensuite configurer des clés gérées par le client pour le compte.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 8cf1f8ecb68e31f93c19d93d6ebc4f8ef37724e7
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028440"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Créer un compte qui prend en charge les clés gérées par le client pour les tables et les files d’attente

Le stockage Azure chiffre toutes les données dans un compte de stockage au repos. Par défaut, Stockage File d’attente et Stockage Table utilisent une clé qui est limitée au service et gérée par Microsoft. Vous pouvez également choisir d’utiliser des clés gérées par le client pour chiffrer les données des files d’attente ou des tables. Pour utiliser des clés gérées par le client avec des files d’attente et des tables, vous devez d’abord créer un compte de stockage qui utilise une clé de chiffrement limitée au compte et non pas au service. Après avoir créé un compte qui utilise la clé de chiffrement de compte pour les données des files d’attente et des tables, vous pouvez configurer des clés gérées par le client avec Azure Key Vault pour ce compte de stockage.

Cet article explique comment créer un compte de stockage qui s’appuie sur une clé limitée au compte. Une fois le compte créé, Microsoft utilise la clé de compte pour chiffrer les données dans le compte et il gère la clé. Vous pouvez par la suite configurer des clés gérées par le client pour le compte pour tirer parti de ces avantages, notamment la capacité à fournir vos propres clés, de mettre à jour la version de la clé, d’effectuer la rotation des clés et de révoquer les contrôles d’accès.

## <a name="about-the-feature"></a>À propos de la fonctionnalité

Pour créer un compte de stockage qui s’appuie sur la clé de chiffrement du compte pour Stockage File d’attente et Stockage Table, vous devez d’abord vous inscrire pour utiliser cette fonctionnalité avec Azure. En raison d’une capacité limitée, notez que plusieurs mois peuvent être nécessaires avant que les demandes d’accès soient approuvées.

Vous pouvez créer un compte de stockage qui s’appuie sur la clé de chiffrement du compte pour Stockage File d’attente et Stockage Table dans les régions suivantes :

- USA Est
- États-Unis - partie centrale méridionale
- USA Ouest 2  

### <a name="register-to-use-the-account-encryption-key"></a>S’inscrire pour utiliser la clé de chiffrement de compte

Pour vous inscrire avec Azure CLI, appelez la commande [az feature register](/cli/azure/feature#az-feature-register).

Pour s’inscrire pour utiliser la clé de chiffrement de compte avec Stockage File d’attente :

```azurecli
az feature register --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForQueues
```

Pour s’inscrire pour utiliser la clé de chiffrement de compte avec Stockage Table :

```azurecli
az feature register --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForTables
```

### <a name="check-the-status-of-your-registration"></a>Vérifier l’état de votre inscription

Pour vérifier l’état de votre inscription pour Stockage File d’attente :

```azurecli
az feature show --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForQueues
```

Pour vérifier l’état de votre inscription pour Stockage Table :

```azurecli
az feature show --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForTables
```

### <a name="re-register-the-azure-storage-resource-provider"></a>Réinscrire le fournisseur de ressources Stockage Azure

Une fois votre inscription approuvée, vous devez réinscrire le fournisseur de ressources Stockage Azure. Appelez la commande [az provider register](/cli/azure/provider#az-provider-register) :

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Créer un compte qui utilise la clé de chiffrement de compte

Vous devez configurer un nouveau compte de stockage pour utiliser la clé de chiffrement de compte pour les files d’attente et les tables au moment où vous créez le compte de stockage. L’étendue de la clé de chiffrement ne peut pas être changée après la création du compte.

Le compte de stockage doit être de type universel v2. Vous pouvez créer le compte de stockage et le configurer pour qu’il s’appuie sur la clé de chiffrement de compte en utilisant Azure CLI ou un modèle Azure Resource Manager.

> [!NOTE]
> Seul Stockage File d’attente et Stockage Table peuvent être configurés en option pour chiffrer les données avec la clé de chiffrement de compte lors de la création du compte de stockage. Stockage Blob et Azure Files utilisent toujours la clé de chiffrement de compte pour chiffrer les données.

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour utiliser Azure CLI afin créer un compte de stockage qui s’appuie sur la clé de chiffrement de compte, vérifié que Azure CLI version 2.0.80 ou ultérieure est installé. Pour plus d’informations, consultez la rubrique [Installation de l’interface de ligne de commande Azure (CLI)](/cli/azure/install-azure-cli).

Ensuite, créez un compte de stockage universel v2 en appelant la commande [az storage account create](/cli/azure/storage/account#az-storage-account-create) avec les paramètres appropriés :

- Incluez l’option `--encryption-key-type-for-queue` et définissez sa valeur sur `Account` pour utiliser la clé de chiffrement de compte afin de chiffrer les données dans Stockage File d’attente.
- Incluez l’option `--encryption-key-type-for-table` et définissez sa valeur sur `Account` pour utiliser la clé de chiffrement de compte afin de chiffrer les données dans Stockage Table.

L’exemple suivant montre comment créer un compte de stockage universel v2 configuré pour LRS et qui utilise la clé de chiffrement de compte pour chiffrer les données à la fois pour Stockage File d’attente et pour Stockage Table. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_LRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

### <a name="templatetabtemplate"></a>[Modèle](#tab/template)

L’exemple JSON suivant crée un compte de stockage universel v2 configuré pour LRS et qui utilise la clé de chiffrement de compte pour chiffrer les données à la fois pour Stockage File d’attente et pour Stockage Table. N’oubliez pas de remplacer les valeurs des espaces réservés entre crochets par vos propres valeurs :

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_LRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

Une fois que vous avez créé un compte qui s’appuie sur la clé de chiffrement de compte, consultez l’un des articles suivants pour configurer les clés gérées par le client avec Azure Key Vault :

- [Configurer des clés gérées par le client avec Azure Key Vault en utilisant le portail Azure](storage-encryption-keys-portal.md)
- [Configurer des clés gérées par le client avec Azure Key Vault en utilisant PowerShell](storage-encryption-keys-powershell.md)
- [Configurer des clés gérées par le client avec Azure Key Vault en utilisant Azure CLI](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>Vérifier la clé de chiffrement de compte

Pour vérifier qu’un service dans un compte de stockage utilise la clé de chiffrement de compte, appelez la commande Azure CLI [az storage account](/cli/azure/storage/account#az-storage-account-show). Cette commande retourne un ensemble de propriétés du compte de stockage et leurs valeurs. Recherchez le champ `keyType` pour chaque service dans la propriété de chiffrement et vérifiez qu’il est défini sur `Account`.

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

## <a name="next-steps"></a>Étapes suivantes

- [Chiffrement du stockage Azure pour les données au repos](storage-service-encryption.md) 
- [Qu’est-ce qu’Azure Key Vault ?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
