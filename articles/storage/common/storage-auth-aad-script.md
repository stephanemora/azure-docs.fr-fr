---
title: Exécuter des commandes Azure CLI ou PowerShell avec des informations d’identification Azure AD pour accéder aux données d’objet blob ou de file d’attente
titleSuffix: Azure Storage
description: Azure CLI et PowerShell prennent en charge la connexion avec des informations d’identification Azure AD pour exécuter des commandes sur les données d’objet blob et de file d’attente de Stockage Azure. Un jeton d’accès est fourni pour la session et utilisé pour autoriser les opérations d’appel. Les autorisations dépendent du rôle RBAC attribué au principal de sécurité Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 07abb9d604c14a5c78a088cb07f57088b84552a6
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74891862"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Exécuter des commandes Azure CLI ou PowerShell avec des informations d’identification Azure AD pour accéder aux données d’objet blob ou de file d’attente

Stockage Azure fournit des extensions pour Azure CLI et PowerShell qui vous permettent de vous connecter et d’exécuter des commandes de script avec des informations d’identification Azure Active Directory (Azure AD). Lorsque vous vous connectez à Azure CLI ou PowerShell avec les informations d’identification Azure AD, un jeton d’accès OAuth 2.0 est retourné. Ce jeton est utilisé automatiquement par CLI ou PowerShell pour autoriser les opérations de données suivantes sur le Stockage Blob ou File d’attente. Pour les opérations prises en charge, vous n’avez plus besoin de fournir une clé de compte ou le jeton SAP avec la commande.

Vous pouvez attribuer des autorisations aux données d’objet blob et de file d’attente à un principal de sécurité Azure AD via le contrôle d’accès en fonction du rôle (RBAC, role-based access control). Pour plus d’informations sur les rôles RBAC dans Stockage Azure, consultez [Gérer les droits d’accès aux données Stockage Azure avec RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Opérations prises en charge

Les extensions sont prises en charge pour les opérations sur les conteneurs et les files d’attente. Les opérations que vous pouvez appeler dépendent des autorisations accordées au principal de sécurité Azure AD avec lequel vous vous connectez à Azure CLI ou PowerShell. Les autorisations sur les conteneurs ou les files d’attente Stockage Azure sont attribuées via le contrôle d’accès en fonction du rôle (RBAC). Par exemple, si le rôle **Lecteur des données Blob** vous est attribué, vous pouvez exécuter des commandes de script qui lisent les données d’un conteneur ou d’une file d’attente. Si le rôle **Contributeur aux données Blob** vous est attribué, vous pouvez exécuter des commandes de script qui lisent, écrivent ou suppriment un conteneur ou une file d’attente, ou les données qu’ils contiennent. 

Pour plus d’informations sur les autorisations requises pour chaque opération Stockage Azure sur un conteneur ou une file d’attente, consultez [Appeler des opérations de stockage avec des jetons OAuth](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Appeler des commandes CLI à l’aide des informations d’identification Azure AD

Azure CLI prend en charge le paramètre `--auth-mode` pour les opérations de données d’objet blob et de file d’attente :

- Définissez le paramètre `--auth-mode` sur `login` pour vous connecter à l’aide d’un principal de sécurité Azure AD.
- Définissez le paramètre `--auth-mode` sur la valeur `key` héritée pour essayer de demander une clé de compte si aucun paramètre d’authentification n’est fourni pour le compte. 

L’exemple suivant montre comment créer un conteneur dans un nouveau compte de stockage à partir d’Azure CLI à l’aide de vos informations d’identification Azure AD. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs : 

1. Assurez-vous qu’Azure CLI version 2.0.46 ou ultérieure est installé. Exécutez `az --version` pour vérifier la version installée.

1. Exécutez `az login` et authentifiez-vous dans la fenêtre du navigateur : 

    ```azurecli
    az login
    ```

1. Spécifiez l’abonnement souhaité. Créez un groupe de ressources à l’aide de la commande [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Créez un compte de stockage au sein de ce groupe de ressources à l’aide de la commande [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) :

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

1. Avant de créer le conteneur, attribuez-vous le rôle [Contributeur aux données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor). Même si vous êtes le propriétaire du compte, vous avez besoin d’autorisations explicites pour effectuer des opérations de données sur le compte de stockage. Pour plus d’informations sur l’attribution de rôles RBAC, consultez [Octroyer l’accès aux données blob et de file d’attente Azure avec RBAC dans le Portail Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > La propagation des attributions de rôles RBAC peut prendre plusieurs minutes.

1. Appelez la commande [az storage container create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) avec le paramètre `--auth-mode` défini sur `login` pour créer le conteneur à l’aide de vos informations d’identification Azure AD :

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

La variable d’environnement associée au paramètre `--auth-mode` est `AZURE_STORAGE_AUTH_MODE`. Vous pouvez spécifier la valeur appropriée dans la variable d’environnement afin d’éviter de l’inclure à chaque appel à une opération de données de Stockage Azure.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Appeler des commandes PowerShell à l’aide des informations d’identification Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pour utiliser Azure PowerShell pour vous connecter et exécuter les opérations suivantes sur le service Stockage Azure à l’aide des informations d’identification Azure AD, créez un contexte de stockage pour faire référence au compte de stockage en incluant le paramètre `-UseConnectedAccount`.

L’exemple suivant montre comment créer un conteneur dans un nouveau compte de stockage à partir d’Azure PowerShell à l’aide de vos informations d’identification Azure AD. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

1. Connectez-vous à votre compte Azure à l’aide de la commande [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) :

    ```powershell
    Connect-AzAccount
    ```

    Pour plus d’informations sur la connexion à Azure avec PowerShell, consultez [Se connecter avec Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Créez un groupe de ressources Azure en appelant [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Créez un compte de stockage en appelant [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Obtenez le contexte du compte de stockage qui spécifie le nouveau compte de stockage en appelant [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Si un compte de stockage est utilisé, vous pouvez référencer le contexte au lieu d’entrer les informations d’identification à plusieurs reprises. Incluez le paramètre `-UseConnectedAccount` pour appeler toutes les opérations de données suivantes à l’aide de vos informations d’identification Azure AD :

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Avant de créer le conteneur, attribuez-vous le rôle [Contributeur aux données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor). Même si vous êtes le propriétaire du compte, vous avez besoin d’autorisations explicites pour effectuer des opérations de données sur le compte de stockage. Pour plus d’informations sur l’attribution de rôles RBAC, consultez [Octroyer l’accès aux données blob et de file d’attente Azure avec RBAC dans le Portail Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > La propagation des attributions de rôles RBAC peut prendre plusieurs minutes.

1. Créez un conteneur en appelant [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Étant donné que cet appel utilise le contexte créé lors des étapes précédentes, le conteneur est créé à l’aide de vos informations d’identification Azure AD. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les rôles RBAC relatifs au Stockage Azure, consultez [Gérer les droits d’accès aux données de stockage avec RBAC](storage-auth-aad-rbac.md).
- Pour plus d’informations sur l’utilisation des identités managées pour les ressources Azure avec le Stockage Azure, consultez [Authentifier l’accès aux objets BLOB et files d’attente avec Azure Active Directory et des identités gérées pour les ressources Azure](storage-auth-aad-msi.md).
- Pour apprendre à autoriser l’accès aux conteneurs et aux files d’attente à partir de vos applications de stockage, consultez [Utiliser Azure AD avec les applications de stockage](storage-auth-aad-app.md).
