---
title: Créer un compte de stockage d'objets blob de blocs - Stockage Azure | Microsoft Docs
description: Explique comment créer un compte BlockBlobStorage Azure présentant des caractéristiques de performances Premium.
author: tamram
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 05/10/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 0e312cf49ee54020d0feb8d8e2f4c7a9bb77215a
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89074523"
---
# <a name="create-a-blockblobstorage-account"></a>Créer un compte BlockBlobStorage

Le genre de compte BlockBlobStorage vous permet de créer des objets blob de blocs présentant des caractéristiques de performances Premium. Ce type de compte de stockage est optimisé pour les charges de travail aux taux de transaction élevés ou nécessitant des délais d'accès très courts. Cet article explique comment créer un compte BlockBlobStorage à l’aide du portail Azure, d’Azure CLI ou d’Azure PowerShell.

> [!NOTE]
> La fonctionnalité d’espace de noms hiérarchique dans un compte de stockage d’objets blob de blocs est en préversion publique. Elle est disponible dans les régions USA Est, USA Est 2, USA Centre, USA Centre Sud, USA Ouest 2, Royaume-Uni Sud, Canada Centre et Australie Est. Pour savoir quelles sont les limitations, consultez [Fonctionnalités de stockage blob disponibles dans Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md) et [Problèmes connus](data-lake-storage-known-issues.md). Pour vous inscrire à la préversion, voir [ce formulaire](https://aka.ms/adlspremiumonboard).

Pour plus d’informations sur les comptes BlockBlobStorage, voir [Vue d’ensemble des comptes de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Aucun.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Cet article de guide pratique nécessite le module Azure PowerShell Az version 1.2.0 ou ultérieure. Exécutez `Get-Module -ListAvailable Az` pour rechercher votre version actuelle. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Vous pouvez vous connecter à Azure et exécuter des commandes Azure CLI de l’une des deux façons suivantes :

- Vous pouvez exécuter des commandes CLI à partir du Portail Azure, dans Azure Cloud Shell.
- Vous pouvez installer l’interface CLI et exécuter des commandes CLI localement.

### <a name="use-azure-cloud-shell"></a>Utiliser Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes Bash gratuit, que vous pouvez exécuter directement dans le portail Azure. L’interface Azure CLI est préinstallée et configurée pour être utilisée avec votre compte. Cliquez sur le bouton **Cloud Shell** du menu situé dans la section supérieure droite de la fenêtre du Portail Azure :

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Ce bouton lance un interpréteur de commandes interactif que vous pouvez utiliser pour exécuter les étapes décrites dans cet article de procédure :

[![Capture d’écran représentant la fenêtre Cloud Shell du portail](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Installer la CLI localement

Vous pouvez également installer et utiliser Azure CLI localement. Pour les besoins de cet article de guide pratique, vous devez utiliser Azure CLI version 2.0.46 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli). 

---

## <a name="sign-in-to-azure"></a>Connexion à Azure

# <a name="portal"></a>[Portail](#tab/azure-portal)

Connectez-vous au [portail Azure](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Connectez-vous à votre abonnement Azure avec la commande `Connect-AzAccount` et suivez les instructions à l’écran pour l’authentification.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour lancer Azure Cloud Shell, connectez-vous au [Portail Azure](https://portal.azure.com).

Pour vous connecter à votre installation locale de l’interface CLI, exécutez la commande [az login](/cli/azure/reference-index#az-login) :

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>Créer un compte BlockBlobStorage

## <a name="portal"></a>[Portail](#tab/azure-portal)
Pour créer un compte BlockBlobStorage sur le portail Azure, procédez comme suit :

1. Sur le portail Azure, sélectionnez **Tous les services** > la catégorie **Stockage** > **Comptes de stockage**.

2. Sous **Comptes de stockage**, sélectionnez **Ajouter**.

3. Dans le champ **Abonnement**, sélectionnez l'abonnement dans lequel vous souhaitez créer le compte de stockage.

4. Dans le champ **Groupe de ressources**, sélectionnez un groupe de ressources existant ou choisissez **Créer**, puis entrez un nom pour le nouveau groupe de ressources.

5. Dans le champ **Nom du compte de stockage**, entrez un nom pour le compte. Respectez les recommandations suivantes :

   - Le nom doit être unique dans tout Azure.
   - Le nom doit comporter 3 à 24 caractères.
   - Le nom ne peut contenir que des chiffres et des lettres minuscules.

6. Dans le champ **Emplacement**, sélectionnez l'emplacement du compte de stockage, ou utilisez l'emplacement par défaut.

7. Pour les autres paramètres, configurez ce qui suit :

   |Champ     |Valeur  |
   |---------|---------|
   |**Performances**    |  Sélectionnez **Premium**.   |
   |**Type de compte**    | Sélectionnez **BlockBlobStorage**.      |
   |**Réplication**    |  Conservez le paramètre par défaut **Stockage localement redondant (LRS)** .      |

   ![Affiche l'interface utilisateur permettant de créer un compte de stockage d'objets blob de blocs sur le portail](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

8. Sélectionnez l’onglet **Avancé**.

9. Si vous souhaitez optimiser votre compte de stockage pour l’analyse des données, définissez **Espace de noms hiérarchique** sur **Activé**. Dans le cas contraire, laissez la valeur par défaut de cette option.

   Pour en savoir plus, consultez [Présentation d’Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).

   > [!NOTE]
   > La fonctionnalité d’espace de noms hiérarchique dans un compte de stockage d’objets blob de blocs est en préversion publique. Elle est disponible dans les régions USA Est, USA Est 2, USA Centre, USA Centre Sud, USA Ouest 2, Royaume-Uni Sud, Canada Centre et Australie Est. Pour savoir quelles sont les limitations, consultez [Fonctionnalités de stockage blob disponibles dans Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md) et [Problèmes connus](data-lake-storage-known-issues.md). Pour vous inscrire à la préversion, voir [ce formulaire](https://aka.ms/adlspremiumonboard).

8. Cliquez sur **Vérifier + créer** pour passer en revue les paramètres de votre compte de stockage.

9. Sélectionnez **Create** (Créer).

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Ouvrez une session Windows PowerShell avec élévation de privilèges (Exécuter en tant qu'administrateur).

2. Exécutez la commande suivante pour vérifier que la dernière version du module `Az` PowerShell est installée.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

3. Ouvrez une nouvelle console PowerShell et connectez-vous avec votre compte Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

4. Si nécessaire, créez un nouveau groupe de ressources. Remplacez les valeurs entre guillemets et exécutez la commande suivante.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

5. Créez le compte BlockBlobStorage. Remplacez les valeurs entre guillemets et exécutez la commande suivante.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```
   Si vous souhaitez optimiser votre compte de stockage pour l’analyse des données, ajoutez `-EnableHierarchicalNamespace $True` à la commande. Pour en savoir plus, consultez [Présentation d’Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).

   > [!NOTE]
   > La fonctionnalité d’espace de noms hiérarchique dans un compte de stockage d’objets blob de blocs est en préversion publique. Elle est disponible dans les régions USA Est, USA Est 2, USA Centre, USA Centre Sud, USA Ouest 2, Royaume-Uni Sud, Canada Centre et Australie Est. Pour savoir quelles sont les limitations, consultez [Fonctionnalités de stockage blob disponibles dans Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md) et [Problèmes connus](data-lake-storage-known-issues.md). Pour vous inscrire à la préversion, voir [ce formulaire](https://aka.ms/adlspremiumonboard).

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour créer un compte d'objets blob de blocs à l'aide d'Azure CLI, vous devez commencer par installer Azure CLI v. 2.0.46 ou version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

1. Connectez-vous à votre abonnement Azure.

   ```azurecli
   az login
   ```

2. Si nécessaire, créez un nouveau groupe de ressources. Remplacez les valeurs entre crochets (y compris les crochets) et exécutez la commande suivante.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

3. Créez le compte BlockBlobStorage. Remplacez les valeurs entre crochets (y compris les crochets) et exécutez la commande suivante.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

   Si vous souhaitez optimiser votre compte de stockage pour l’analyse des données, ajoutez `--hierarchical-namespace true` à la commande. Pour en savoir plus, consultez [Présentation d’Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).

   > [!NOTE]
   > La fonctionnalité d’espace de noms hiérarchique dans un compte de stockage d’objets blob de blocs est en préversion publique. Elle est disponible dans les régions USA Est, USA Est 2, USA Centre, USA Centre Sud, USA Ouest 2, Royaume-Uni Sud, Canada Centre et Australie Est. Pour savoir quelles sont les limitations, consultez [Fonctionnalités de stockage blob disponibles dans Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md) et [Problèmes connus](data-lake-storage-known-issues.md). Pour vous inscrire à la préversion, voir [ce formulaire](https://aka.ms/adlspremiumonboard).
   
---

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les comptes de stockage, consultez [Vue d’ensemble des comptes de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
