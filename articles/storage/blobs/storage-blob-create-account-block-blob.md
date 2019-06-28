---
title: Créer un compte de stockage d'objets blob de blocs - Stockage Azure | Microsoft Docs
description: Explique comment créer un compte de stockage d'objets blob de blocs Azure présentant des caractéristiques de performances premium.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9d8fb8f5f470dc47088efb30b7f823a0b8c624c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65141014"
---
# <a name="create-a-block-blob-storage-account"></a>Créer un compte de stockage d’objet blob de blocs

Le type de compte de stockage d'objets blob de blocs vous permet de créer des objets blob de blocs présentant des caractéristiques de performances premium. Ce type de compte de stockage est optimisé pour les charges de travail aux taux de transaction élevés ou nécessitant des délais d'accès très courts. Cet article explique comment créer un compte de stockage d'objets blob de blocs à l'aide du portail Azure, d'Azure CLI ou d'Azure PowerShell.

Pour plus d'informations sur les comptes de stockage d'objets blob de blocs, consultez [Vue d'ensemble des comptes de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="create-account-in-the-azure-portal"></a>Créer un compte sur le portail Azure

Pour créer un compte de stockage d'objets blob de blocs sur le portail Azure, procédez comme suit :

1. Sur le portail Azure, sélectionnez **Tous les services** > la catégorie **Stockage** > **Comptes de stockage**.

1. Sous **Comptes de stockage**, sélectionnez **Ajouter**.

1. Dans le champ **Abonnement**, sélectionnez l'abonnement dans lequel vous souhaitez créer le compte de stockage.

1. Dans le champ **Groupe de ressources**, sélectionnez un groupe de ressources existant ou choisissez **Créer**, puis entrez un nom pour le nouveau groupe de ressources.

1. Dans le champ **Nom du compte de stockage**, entrez un nom pour le compte. Respectez les recommandations suivantes :

   - Le nom doit être unique dans tout Azure.
   - Le nom doit comporter 3 à 24 caractères.
   - Le nom ne peut contenir que des chiffres et des lettres minuscules.

1. Dans le champ **Emplacement**, sélectionnez l'emplacement du compte de stockage, ou utilisez l'emplacement par défaut.

1. Pour les autres paramètres, configurez ce qui suit :

   |Champ     |Valeur  |
   |---------|---------|
   |**Performances**    |  Sélectionnez **Premium**.   |
   |**Type de compte**    | Sélectionnez **BlockBlobStorage**.      |
   |**Réplication**    |  Conservez le paramètre par défaut **Stockage localement redondant (LRS)** .      |

   ![Affiche l'interface utilisateur permettant de créer un compte de stockage d'objets blob de blocs sur le portail](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Cliquez sur **Vérifier + créer** pour passer en revue les paramètres de votre compte de stockage.

1. Sélectionnez **Créer**.

## <a name="create-account-using-azure-powershell"></a>Créer un compte à l'aide d'Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Ouvrez une session Windows PowerShell avec élévation de privilèges (Exécuter en tant qu'administrateur).

1. Exécutez la commande suivante pour vérifier que la dernière version du module `Az` PowerShell est installée.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Ouvrez une nouvelle console PowerShell et connectez-vous avec votre compte Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Si nécessaire, créez un nouveau groupe de ressources. Remplacez les valeurs entre guillemets et exécutez la commande suivante.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Créez le compte de stockage d'objets blob de blocs. Remplacez les valeurs entre guillemets et exécutez la commande suivante.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="create-account-using-azure-cli"></a>Créer un compte à l'aide d'Azure CLI

Pour créer un compte d'objets blob de blocs à l'aide d'Azure CLI, vous devez commencer par installer Azure CLI v. 2.0.46 ou version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

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

1. Créez le compte de stockage d'objets blob de blocs. Remplacez les valeurs entre crochets (y compris les crochets) et exécutez la commande suivante.

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
