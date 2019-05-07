---
title: Création d’un compte de stockage blob de bloc - stockage Azure | Microsoft Docs
description: Montre comment créer un compte de stockage des objets blob de blocs Azure avec les caractéristiques de performances premium.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9d8fb8f5f470dc47088efb30b7f823a0b8c624c8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141014"
---
# <a name="create-a-block-blob-storage-account"></a>Créer un compte de stockage d’objet blob de blocs

Le type de compte de stockage blob bloc vous permet de créer des objets BLOB de blocs avec des caractéristiques de performances premium. Ce type de compte de stockage est optimisé pour les charges de travail avec des taux élevé de transactions ou qui nécessitent des temps d’accès très rapides. Cet article explique comment créer un compte de stockage des objets blob de bloc à l’aide du portail Azure, Azure CLI ou Azure PowerShell.

Pour plus d’informations sur les comptes de stockage blob de bloc, consultez [vue d’ensemble du compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="create-account-in-the-azure-portal"></a>Créer un compte dans le portail Azure

Pour créer un compte de stockage des objets blob de bloc dans le portail Azure, procédez comme suit :

1. Dans le portail Azure, sélectionnez **tous les services** > la **stockage** catégorie > **comptes de stockage**.

1. Sous **comptes de stockage**, sélectionnez **ajouter**.

1. Dans le **abonnement** champ, sélectionnez l’abonnement dans lequel créer le compte de stockage.

1. Dans le **groupe de ressources** champ, sélectionnez un groupe de ressources existant ou **créer**, puis entrez un nom pour le nouveau groupe de ressources.

1. Dans le **nom de compte de stockage** , entrez un nom pour le compte. Notez les instructions suivantes :

   - Le nom doit être unique dans Azure.
   - Le nom doit comprendre entre 3 et 24 caractères.
   - Le nom peut inclure uniquement des lettres minuscules et chiffres.

1. Dans le **emplacement** champ, sélectionnez un emplacement pour le compte de stockage ou utilisez l’emplacement par défaut.

1. Pour le reste des paramètres, configurez les éléments suivants :

   |Champ     |Valeur  |
   |---------|---------|
   |**Performances**    |  Sélectionnez **Premium**.   |
   |**Type de compte**    | Sélectionnez **BlockBlobStorage**.      |
   |**Réplication**    |  Laissez le paramètre par défaut **stockage localement redondant (LRS)**.      |

   ![Affiche l’interface utilisateur pour créer un compte de stockage des objets blob de bloc du portail](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Sélectionnez **examiner + créer** pour passer en revue les paramètres de compte de stockage.

1. Sélectionnez **Créer**.

## <a name="create-account-using-azure-powershell"></a>Créer le compte à l’aide d’Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Ouvrez une session Windows PowerShell avec élévation de privilèges (exécuter en tant qu’administrateur).

1. Exécutez la commande suivante pour vérifier la dernière version de la `Az` module PowerShell est installé.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Ouvrez une nouvelle console PowerShell et connectez-vous avec votre compte Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Si nécessaire, créez un nouveau groupe de ressources. Remplacez les valeurs dans les propositions et exécutez la commande suivante.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Créer le compte de stockage des objets blob de bloc. Remplacez les valeurs dans les propositions et exécutez la commande suivante.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="create-account-using-azure-cli"></a>Créer le compte à l’aide d’Azure CLI

Pour créer un compte d’objets blob de bloc à l’aide de l’interface CLI, vous devez d’abord installer Azure CLI v. version 2.0.46 du ou une version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

1. Connectez-vous à votre abonnement Azure.

   ```azurecli
   az login
   ```

1. Si nécessaire, créez un nouveau groupe de ressources. Remplacez les valeurs entre crochets (y compris les crochets) et exécutez la commande suivante.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Créer le compte de stockage des objets blob de bloc. Remplacez les valeurs entre crochets (y compris les crochets) et exécutez la commande suivante.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les comptes de stockage, consultez [Vue d’ensemble des comptes de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
