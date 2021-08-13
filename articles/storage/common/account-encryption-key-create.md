---
title: Créer un compte qui prend en charge les clés gérées par le client pour les tables et les files d’attente
titleSuffix: Azure Storage
description: Découvrez comment créer un compte de stockage prenant en charge la configuration des clés gérées par le client pour les tables et les files d’attente. Utilisez Azure CLI ou un modèle Azure Resource Manager pour créer un compte de stockage qui s’appuie sur la clé de chiffrement du compte pour le chiffrement de Stockage Azure. Vous pouvez ensuite configurer des clés gérées par le client pour le compte.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/09/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: cf646fe61e3fa00407cf2ff3f47f872167c00aa9
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111903894"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Créer un compte qui prend en charge les clés gérées par le client pour les tables et les files d’attente

Le stockage Azure chiffre toutes les données dans un compte de stockage au repos. Par défaut, Stockage File d’attente et Stockage Table utilisent une clé qui est limitée au service et gérée par Microsoft. Vous pouvez également choisir d’utiliser des clés gérées par le client pour chiffrer les données des files d’attente ou des tables. Pour utiliser des clés gérées par le client avec des files d’attente et des tables, vous devez d’abord créer un compte de stockage qui utilise une clé de chiffrement limitée au compte et non pas au service. Après avoir créé un compte qui utilise la clé de chiffrement de compte pour les données des files d’attente et des tables, vous pouvez configurer des clés gérées par le client pour ce compte de stockage.

Cet article explique comment créer un compte de stockage qui s’appuie sur une clé limitée au compte. Une fois le compte créé, Microsoft utilise la clé de compte pour chiffrer les données dans le compte et il gère la clé. Vous pouvez par la suite configurer des clés gérées par le client pour le compte pour tirer parti de ces avantages, notamment la capacité à fournir vos propres clés, de mettre à jour la version de la clé, d’effectuer la rotation des clés et de révoquer les contrôles d’accès.

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Créer un compte qui utilise la clé de chiffrement de compte

Vous devez configurer un nouveau compte de stockage pour utiliser la clé de chiffrement de compte pour les files d’attente et les tables au moment où vous créez le compte de stockage. L’étendue de la clé de chiffrement ne peut pas être changée après la création du compte.

Le compte de stockage doit être de type universel v2. Vous pouvez créer le compte de stockage et le configurer pour qu’il s’appuie sur la clé de chiffrement de compte en utilisant le portail Azure, PowerShell, Azure CLI ou un modèle Azure Resource Manager.

Pour plus d’informations sur la création d’un compte de stockage, consultez la section [Créer un compte de stockage](storage-account-create.md).

> [!NOTE]
> Seul Stockage File d’attente et Stockage Table peuvent être configurés en option pour chiffrer les données avec la clé de chiffrement de compte lors de la création du compte de stockage. Stockage Blob et Azure Files utilisent toujours la clé de chiffrement de compte pour chiffrer les données.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour créer un compte de stockage qui s’appuie sur la clé de chiffrement de compte à l’aide du portail Azure, suivez ces étapes :

1. Dans le menu du portail de gauche, sélectionnez **Comptes de stockage** pour afficher la liste de vos comptes de stockage.
1. Sur la page **Comptes de stockage**, sélectionnez **Nouveau**.
1. Renseignez les champs sous l’onglet **De base**.
1. Dans l’onglet Avancé, recherchez la section **Tables et files d’attente**, puis sélectionnez **Activer la prise en charge des clés gérées par le client**.

    :::image type="content" source="media/account-encryption-key-create/enable-cmk-tables-queues.png" alt-text="Capture d’écran montrant comment activer les clés gérées par le client pour les files d’attente et les tables lors de la création d’un compte":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour utiliser PowerShell afin de créer un compte de stockage qui s’appuie sur la clé de chiffrement de compte, vérifiez que vous avez installé le module Azure PowerShell (version 3.4.0 ou ultérieure). Pour plus d’informations, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps).

Ensuite, créez un compte de stockage universel v2 en appelant la commande [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) avec les paramètres appropriés :

- Incluez l’option `-EncryptionKeyTypeForQueue` et définissez sa valeur sur `Account` pour utiliser la clé de chiffrement de compte afin de chiffrer les données dans Stockage File d’attente.
- Incluez l’option `-EncryptionKeyTypeForTable` et définissez sa valeur sur `Account` pour utiliser la clé de chiffrement de compte afin de chiffrer les données dans Stockage Table.

L’exemple suivant montre comment créer un compte de stockage universel v2 configuré pour le stockage géoredondant avec accès en lecture (RA-GRS) et utilisant la clé de chiffrement de compte pour chiffrer les données à la fois pour Stockage File d’attente et pour Stockage Table. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour utiliser Azure CLI afin créer un compte de stockage qui s’appuie sur la clé de chiffrement de compte, vérifié que Azure CLI version 2.0.80 ou ultérieure est installé. Pour plus d’informations, consultez la rubrique [Installation de l’interface de ligne de commande Azure (CLI)](/cli/azure/install-azure-cli).

Ensuite, créez un compte de stockage universel v2 en appelant la commande [az storage account create](/cli/azure/storage/account#az_storage_account_create) avec les paramètres appropriés :

- Incluez l’option `--encryption-key-type-for-queue` et définissez sa valeur sur `Account` pour utiliser la clé de chiffrement de compte afin de chiffrer les données dans Stockage File d’attente.
- Incluez l’option `--encryption-key-type-for-table` et définissez sa valeur sur `Account` pour utiliser la clé de chiffrement de compte afin de chiffrer les données dans Stockage Table.

L’exemple suivant montre comment créer un compte de stockage universel v2 configuré pour le stockage géoredondant avec accès en lecture (RA-GRS) et utilisant la clé de chiffrement de compte pour chiffrer les données à la fois pour Stockage File d’attente et pour Stockage Table. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Modèle](#tab/template)

L’exemple JSON suivant crée un compte de stockage universel v2 configuré pour le stockage géoredondant avec accès en lecture (RA-GRS) et utilisant la clé de chiffrement de compte pour chiffrer les données à la fois pour Stockage File d’attente et pour Stockage Table. N’oubliez pas de remplacer les valeurs des espaces réservés entre crochets par vos propres valeurs :

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
            "name": "[parameters('Standard_RAGRS')]"
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

Après avoir créé un compte qui s’appuie sur la clé de chiffrement de compte, vous pouvez configurer les clés gérées par le client qui sont stockées dans Azure Key Vault ou dans un module de sécurité matériel (HSM). Pour découvrir comment stocker des clés gérées par le client dans un coffre de clés, consultez [Configurer le chiffrement avec des clés gérées par le client stockées dans Azure Key Vault](customer-managed-keys-configure-key-vault.md). Pour découvrir comment stocker des clés gérées par le client dans un HSM managé, consultez [Configurer le chiffrement avec des clés gérées par le client stockées dans Azure Key Vault Managed HSM](customer-managed-keys-configure-key-vault-hsm.md).

## <a name="verify-the-account-encryption-key"></a>Vérifier la clé de chiffrement de compte

Après avoir créé le compte, vous pouvez vérifier que le compte de stockage utilise une clé de chiffrement étendue au compte, à l’aide du portail Azure, de PowerShell ou d’Azure CLI.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour vérifier qu’un service d’un compte de stockage utilise une clé de chiffrement étendue au compte en utilisant le portail Azure, suivez ces étapes :

1. Accédez à votre nouveau compte de stockage dans le portail Azure.
1. Dans la section **Sécurité + réseau**, sélectionnez **Chiffrement**.
1. Si le compte de stockage a été créé pour s’appuyer sur la clé de chiffrement de compte, dans l’onglet **Chiffrement** vous voyez que les clés gérées par le client peuvent être activées pour l’ensemble des quatre services de stockage Azure : objets blob, fichiers, tables et files d’attente.

    :::image type="content" source="media/account-encryption-key-create/verify-cmk-tables-queues.png" alt-text="Capture d’écran montrant comment vérifier la dépendance du compte de stockage par rapport à la clé de chiffrement de compte":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour vérifier qu’un service dans un compte de stockage utilise la clé de chiffrement de compte, à l’aide de PowerShell appelez la commande [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount). Cette commande retourne un ensemble de propriétés du compte de stockage et leurs valeurs. Recherchez le champ `KeyType` pour chaque service dans la propriété `Encryption` et vérifiez qu’il est défini sur `Account`.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour vérifier qu’un service d’un compte de stockage utilise la clé de chiffrement de compte, avec l’interface Azure CLI appelez la commande [az storage account show](/cli/azure/storage/account#az_storage_account_show). Cette commande retourne un ensemble de propriétés du compte de stockage et leurs valeurs. Recherchez le champ `keyType` pour chaque service dans la propriété de chiffrement et vérifiez qu’il est défini sur `Account`.

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Modèle](#tab/template)

N/A

---

Une fois que vous avez vérifié l’utilisation par le compte de stockage d’une clé de chiffrement étendue au compte, vous pouvez activer les clés gérées par le client pour le compte. Les quatre services du stockage Azure&mdash;objets blob, fichiers, tables et files d’attente&mdash; utiliseront ensuite la clé gérée par le client pour le chiffrement.

## <a name="pricing-and-billing"></a>Tarification et facturation

Un compte de stockage créé pour utiliser une clé de chiffrement limitée au compte est facturé pour la capacité de stockage et les transactions Table à un tarif différent de celui d’un compte qui utilise la clé par défaut limitée au service. Pour plus d’informations, consultez [Tarification Stockage Table Azure](https://azure.microsoft.com/pricing/details/storage/tables/).

## <a name="next-steps"></a>Étapes suivantes

- [Chiffrement du stockage Azure pour les données au repos](storage-service-encryption.md)
- [Clés gérées par le client pour le chiffrement du Stockage Azure](customer-managed-keys-overview.md)
- [Configurer le chiffrement avec des clés gérées par le client stockées dans Azure Key Vault](customer-managed-keys-configure-key-vault.md)
- [Configurer le chiffrement avec des clés gérées par le client stockées dans Azure Key Vault Managed HSM](customer-managed-keys-configure-key-vault-hsm.md)