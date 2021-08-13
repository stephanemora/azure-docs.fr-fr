---
title: Activer le chiffrement d’infrastructure pour le chiffrement double des données
titleSuffix: Azure Storage
description: Les clients qui doivent s’assurer que leurs données sont sécurisées peuvent également activer le chiffrement AES 256 bits au niveau de l’infrastructure Stockage Azure. Lorsque le chiffrement d’infrastructure est activé, les données d’un compte de stockage ou d’une étendue de chiffrement sont chiffrées deux fois avec deux algorithmes de chiffrement et deux clés différents.
services: storage
author: tamram
ms.service: storage
ms.date: 06/01/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 5c87ed5222b5bb95660b6e63e259a2b5e14b7b47
ms.sourcegitcommit: eb20dcc97827ef255cb4ab2131a39b8cebe21258
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111372671"
---
# <a name="enable-infrastructure-encryption-for-double-encryption-of-data"></a>Activer le chiffrement d’infrastructure pour le chiffrement double des données

Stockage Azure chiffre automatiquement toutes les données d’un compte de stockage au niveau du service à l’aide du chiffrement AES 256 bits, un des chiffrements par blocs les plus puissants actuellement disponibles, et est conforme à la norme FIPS 140-2. Les clients qui doivent s’assurer que leurs données sont sécurisées peuvent également activer le chiffrement AES 256 bits au niveau de l’infrastructure de Stockage Azure pour le chiffrement double. Le double chiffrement des données Stockage Azure permet d’éviter un scénario impliquant une possible compromission d’un algorithme ou d’une clé de chiffrement. Dans un tel scénario, la couche de chiffrement supplémentaire continue de protéger vos données.

Le chiffrement d’infrastructure peut être activé pour l’ensemble du compte de stockage ou pour une étendue de chiffrement au sein d’un compte. Lorsque le chiffrement d’infrastructure est activé pour un compte de stockage ou une étendue de chiffrement, les données sont chiffrées deux fois, une fois au niveau du service et une fois au niveau de l’infrastructure, avec deux algorithmes de chiffrement et deux clés différents.

Le chiffrement au niveau du service prend en charge l’utilisation de clés gérées par Microsoft ou de clés gérées par le client avec Azure Key Vault ou un module de sécurité matériel (HSM) géré par Azure Key Vault (préversion). Le chiffrement au niveau de l’infrastructure s’appuie sur des clés gérées par Microsoft et utilise systématiquement une clé distincte. Pour plus d’informations sur la gestion des clés avec chiffrement Stockage Azure, consultez [À propos de la gestion des clés de chiffrement](storage-service-encryption.md#about-encryption-key-management).

Pour chiffrer doublement vos données, vous devez d’abord créer un compte de stockage ou une étendue de chiffrement qui sont configurés pour le chiffrement d’infrastructure. Cet article décrit comment activer le chiffrement d’infrastructure.

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Créer un compte avec le chiffrement d’infrastructure activé

Pour activer le chiffrement d’infrastructure pour un compte de stockage, vous devez configurer l’utilisation du chiffrement d’infrastructure au moment où vous créez le compte. Le chiffrement d’infrastructure ne peut pas être activé ou désactivé une fois le compte créé. Le compte de stockage doit être de type universel v2.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour utiliser PowerShell afin de créer un compte de stockage avec le chiffrement d’infrastructure activé, procédez comme suit :

1. Dans le portail Azure, accédez à la page **Comptes de stockage**.
1. Choisissez le bouton **ajouter un** pour ajouter un compte de stockage v2 à usage général.
1. Sous l’onglet **avancé**, localisez le chiffrement d’**Infrastructure**, puis sélectionnez **Activé**.
1. Sélectionnez **Vérifier + créer** pour achever la création du compte de stockage.

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="Capture d’écran montrant comment activer le chiffrement de l’infrastructure lors de la création d’un compte":::

Pour vérifier que le chiffrement d’infrastructure est activé pour un compte de stockage avec le portail Azure, procédez comme suit :

1. Accédez à votre compte de stockage dans le portail Azure.
1. Sous **Paramètres**, choisissez **Chiffrement**.

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="Capture d’écran montrant comment vérifier que le chiffrement d’infrastructure est activé pour un compte":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour utiliser PowerShell afin de créer un compte de stockage avec le chiffrement d’infrastructure activé, vérifiez que vous avez installé le [module PowerShell Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) (version 2.2.0 ou ultérieure). Pour plus d’informations, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/install-az-ps).

Ensuite, créez un compte de stockage à usage général v2 en appelant la commande [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Incluez l’option `-RequireInfrastructureEncryption` pour activer le chiffrement d'infrastructure.

L’exemple suivant montre comment créer un compte de stockage à usage général v2 configuré pour le stockage géoredondant avec accès en lecture (RA-GRS) et chiffrement d’infrastructure activé pour le double chiffrement des données. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

Pour vérifier que le chiffrement d’infrastructure est activé pour un compte de stockage, appelez la commande [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount). Cette commande retourne un ensemble de propriétés du compte de stockage et leurs valeurs. Récupérez le champ `RequireInfrastructureEncryption` dans la propriété `Encryption` et vérifiez qu’il est défini sur `True`.

L'exemple de code suivant récupère la valeur de la propriété `RequireInfrastructureEncryption`. N’oubliez pas de remplacer les valeurs des espaces réservés entre crochets par vos propres valeurs :

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour utiliser Azure CLI afin créer un compte de stockage avec le chiffrement d’infrastructure activé, vérifiez que Azure CLI version 2.8.0 ou ultérieure est installé. Pour plus d’informations, consultez la rubrique [Installation de l’interface de ligne de commande Azure (CLI)](/cli/azure/install-azure-cli).

Ensuite, créez un compte de stockage à usage générale v2 en appelant la commande [az storage account create](/cli/azure/storage/account#az_storage_account_create) et incluez `--require-infrastructure-encryption option` pour activer le chiffrement d’infrastructure.

L’exemple suivant montre comment créer un compte de stockage à usage général v2 configuré pour le stockage géoredondant avec accès en lecture (RA-GRS) et chiffrement d’infrastructure activé pour le double chiffrement des données. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

Pour vérifier que le chiffrement d’infrastructure est activé pour un compte de stockage, appelez la commande [az storage account show](/cli/azure/storage/account#az-storage-account-show). Cette commande retourne un ensemble de propriétés du compte de stockage et leurs valeurs. Recherchez le champ `requireInfrastructureEncryption` dans la propriété `encryption` et vérifiez qu’il est défini sur `true`.

L'exemple de code suivant récupère la valeur de la propriété `requireInfrastructureEncryption`. N’oubliez pas de remplacer les valeurs des espaces réservés entre crochets par vos propres valeurs :

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Modèle](#tab/template)

L’exemple JSON suivant crée un compte de stockage à usage général v2 configuré pour le stockage géoredondant avec accès en lecture (RA-GRS) et chiffrement d’infrastructure activé pour le double chiffrement des données. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

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
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

Azure Policy fournit une stratégie intégrée pour exiger que le chiffrement de l’infrastructure soit activé pour un compte de stockage. Pour plus d’informations, consultez la section **Stockage** dans [définitions de stratégies intégrées Azure Policy](../../governance/policy/samples/built-in-policies.md#storage).


## <a name="create-an-encryption-scope-with-infrastructure-encryption-enabled"></a>Créer une étendue de chiffrement avec le chiffrement d’infrastructure activé

Si le chiffrement d’infrastructure est activé pour un compte, toute étendue de chiffrement créée sur ce compte utilise automatiquement le chiffrement d’infrastructure. Si le chiffrement d’infrastructure n’est pas activé au niveau du compte, vous avez la possibilité de l’activer pour une étendue de chiffrement au moment de la création de l’étendue. Le paramètre de chiffrement d’infrastructure d’une étendue de chiffrement ne peut pas être modifié après la création de l’étendue. Pour plus d’informations, consultez [Créer une étendue de chiffrement](../blobs/encryption-scope-manage.md#create-an-encryption-scope).

## <a name="next-steps"></a>Étapes suivantes

- [Chiffrement du stockage Azure pour les données au repos](storage-service-encryption.md)
- [Clés gérées par le client pour le chiffrement du Stockage Azure](customer-managed-keys-overview.md)
- [Étendues de chiffrement pour le stockage d’objets blob](../blobs/encryption-scope-overview.md)
