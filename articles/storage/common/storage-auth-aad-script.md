---
title: Exécuter des commandes Azure CLI ou PowerShell avec les informations d’identification Azure AD pour accéder aux données d’objet blob ou file d’attente | Microsoft Docs
description: Azure CLI et PowerShell prend en charge la connexion avec informations d’identification Azure AD pour exécuter des commandes sur des données blob et files d’attente de stockage Azure. Un jeton d’accès est fourni pour la session et utilisé pour autoriser les opérations d’appel. Autorisations varient selon le rôle RBAC attribué au principal de sécurité Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/19/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 96be1e600c8d5895cc0eb5b058ce17f7265fa0a9
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149646"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Exécuter des commandes Azure CLI ou PowerShell avec les informations d’identification Azure AD pour accéder aux données d’objet blob ou file d’attente

Stockage Azure fournit des extensions pour Azure CLI et PowerShell qui vous permettent de vous connecter et exécuter des commandes de script avec les informations d’identification Azure Active Directory (Azure AD). Lorsque vous vous connectez à Azure CLI ou PowerShell avec les informations d’identification Azure AD, un jeton d’accès OAuth 2.0 est retourné. Ce jeton est automatiquement utilisé par l’interface CLI ou PowerShell pour autoriser les opérations de données suivantes sur le stockage Blob ou file d’attente. Pour les opérations prises en charge, vous n’avez plus besoin de fournir une clé de compte ou le jeton SAP avec la commande.

Vous pouvez affecter des autorisations aux données blob et file d’attente à un principal de sécurité Azure AD via le contrôle d’accès en fonction du rôle (RBAC). Pour plus d’informations sur les rôles RBAC dans le stockage Azure, consultez [gérer les droits d’accès aux données de stockage Azure avec RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Opérations prises en charge

Les extensions sont prises en charge pour les opérations sur les conteneurs et les files d’attente. Les opérations que vous pouvez appeler varie selon les autorisations accordées au principal de sécurité Azure AD avec lequel vous être connecté à Azure CLI ou PowerShell. Les autorisations sur les conteneurs ou les files d’attente Stockage Azure sont attribuées via le contrôle d’accès en fonction du rôle (RBAC). Par exemple, si vous êtes affecté le **lecteur des données Blob** rôle, vous pouvez exécuter des commandes de script qui lisent les données à partir d’un conteneur ou d’une file d’attente. Si vous êtes affecté le **contributeur aux données Blob** rôle, puis vous pouvez exécuter des commandes de script que vous lire, écrivant ou supprimer un conteneur ou file d’attente ou les données qu’ils contiennent. 

Pour plus d’informations sur les autorisations requises pour chaque opération Stockage Azure sur un conteneur ou une file d’attente, consultez [Autorisations pour appeler les opérations REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Appeler des commandes CLI à l’aide des informations d’identification Azure AD

Azure CLI prend en charge la `--auth-mode` paramètre pour les opérations de données blob et file d’attente :

- Définir le `--auth-mode` paramètre `login` pour vous connecter à l’aide d’un principal de sécurité Azure AD.
- Définissez le paramètre `--auth-mode` sur la valeur `key` héritée pour essayer de demander une clé de compte si aucun paramètre d’authentification n’est fourni pour le compte. 

L’exemple suivant montre comment créer un conteneur dans un compte de stockage à partir d’Azure CLI à l’aide de vos informations d’identification Azure AD. Pensez à remplacer les valeurs d’espace réservé figurant entre crochets par vos propres valeurs : 

1. Assurez-vous que vous avez installé la version d’Azure CLI version 2.0.46 du ou version ultérieure. Exécutez `az --version` pour vérifier la version installée.

1. Exécutez `az login` et s’authentifier dans la fenêtre du navigateur : 

    ```azurecli
    az login
    ```
    
1. Spécifiez votre abonnement souhaité. Créez un groupe de ressources à l’aide de la commande [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Créer un compte de stockage au sein de ce groupe de ressources à l’aide [créer de compte de stockage az](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create): 

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```
    
1. Avant de créer le conteneur, affecter le [contributeur aux données stockage Blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) rôle à vous-même. Même si vous êtes le propriétaire du compte, vous avez besoin d’autorisations explicites pour effectuer des opérations de données sur le compte de stockage. Pour plus d’informations sur l’affectation des rôles RBAC, consultez [accorder l’accès aux données blob et file d’attente Azure avec RBAC dans le portail Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Attributions de rôles RBAC peuvent prendre quelques minutes pour se propager.
    
1. Appelez le [créer de conteneur de stockage az](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) commande avec le `--auth-mode` paramètre défini sur `login` pour créer le conteneur à l’aide de vos informations d’identification Azure AD :

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

La variable d’environnement associée au paramètre `--auth-mode` est `AZURE_STORAGE_AUTH_MODE`. Vous pouvez spécifier la valeur appropriée dans la variable d’environnement afin d’éviter, notamment à chaque appel à une opération de données de stockage Azure.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Appeler des commandes PowerShell à l’aide des informations d’identification Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pour utiliser Azure PowerShell pour vous connecter et exécuter les opérations suivantes sur le stockage Azure à l’aide des informations d’identification Azure AD, créer un contexte de stockage pour faire référence au compte de stockage et notamment le `-UseConnectedAccount` paramètre.

L’exemple suivant montre comment créer un conteneur dans un compte de stockage à partir d’Azure PowerShell à l’aide de vos informations d’identification Azure AD. Pensez à remplacer les valeurs d’espace réservé figurant entre crochets par vos propres valeurs :

1. Connectez-vous à votre abonnement Azure avec le `Connect-AzAccount` et suivez l’à l’écran directions à entrer vos informations d’identification Azure AD : 

    ```powershell
    Connect-AzAccount
    ```
    
1. Créer un groupe de ressources Azure en appelant [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Créer un compte de stockage en appelant [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Obtenir le contexte du compte de stockage qui spécifie le nouveau compte de stockage en appelant [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Lorsqu’il agit sur un compte de stockage, vous pouvez référencer le contexte au lieu de passer à plusieurs reprises dans les informations d’identification. Inclure le `-UseConnectedAccount` paramètre pour appeler des opérations de données suivantes à l’aide de vos informations d’identification Azure AD :

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Avant de créer le conteneur, affecter le [contributeur aux données stockage Blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) rôle à vous-même. Même si vous êtes le propriétaire du compte, vous avez besoin d’autorisations explicites pour effectuer des opérations de données sur le compte de stockage. Pour plus d’informations sur l’affectation des rôles RBAC, consultez [accorder l’accès aux données blob et file d’attente Azure avec RBAC dans le portail Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Attributions de rôles RBAC peuvent prendre quelques minutes pour se propager.

1. Créer un conteneur en appelant [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Étant donné que cet appel utilise le contexte créé dans les étapes précédentes, le conteneur est créé à l’aide de vos informations d’identification Azure AD. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les rôles RBAC pour le stockage Azure, consultez [gérer les droits d’accès aux données de stockage avec RBAC](storage-auth-aad-rbac.md).
- Pour en savoir plus sur l’utilisation des identités gérées pour les ressources Azure avec le stockage Azure, consultez [authentifier l’accès aux objets BLOB et files d’attente avec Azure Active Directory et des identités gérées pour les ressources Azure](storage-auth-aad-msi.md).
- Pour apprendre à autoriser l’accès aux conteneurs et aux files d’attente à partir de vos applications de stockage, consultez [Utiliser Azure AD avec les applications de stockage](storage-auth-aad-app.md).
