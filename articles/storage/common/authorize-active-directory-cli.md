---
title: Exécuter des commandes Azure CLI avec des informations d’identification Azure AD pour accéder à des données d’objet blob ou de file d’attente
titleSuffix: Azure Storage
description: Azure CLI prend en charge la connexion avec des informations d’identification Azure AD pour exécuter des commandes sur des données d’objet blob et de file d’attente du Stockage Azure. Un jeton d’accès est fourni pour la session et utilisé pour autoriser les opérations d’appel. Les autorisations dépendent du rôle RBAC attribué au principal de sécurité Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bfbb466b7679e841c9c0154dbe28de14574d4282
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75553262"
---
# <a name="run-azure-cli-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Exécuter des commandes Azure CLI avec des informations d’identification Azure AD pour accéder à des données d’objet blob ou de file d’attente

Le Stockage Azure fournit des extensions pour Azure CLI qui vous permettent de vous connecter et d’exécuter des commandes de script avec des informations d’identification Azure Active Directory (Azure AD). Quand vous vous connectez à Azure CLI avec des informations d’identification Azure AD, un jeton d’accès OAuth 2.0 est retourné. Ce jeton est utilisé automatiquement par Azure CLI pour autoriser les opérations de données suivantes sur le Stockage Blob ou File d’attente. Pour les opérations prises en charge, vous n’avez plus besoin de fournir une clé de compte ou le jeton SAP avec la commande.

Vous pouvez attribuer des autorisations aux données d’objet blob et de file d’attente à un principal de sécurité Azure AD via le contrôle d’accès en fonction du rôle (RBAC, role-based access control). Pour plus d’informations sur les rôles RBAC dans Stockage Azure, consultez [Gérer les droits d’accès aux données Stockage Azure avec RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Opérations prises en charge

Les extensions Stockage Azure sont prises en charge pour les opérations sur les données d’objet blob et de file d’attente. Les opérations que vous pouvez appeler dépendent des autorisations accordées au principal de sécurité Azure AD avec lequel vous vous connectez à Azure CLI. Les autorisations sur les conteneurs ou les files d’attente Stockage Azure sont attribuées à l’aide du contrôle d’accès en fonction du rôle (RBAC). Par exemple, si le rôle **Lecteur des données Blob** vous est attribué, vous pouvez exécuter des commandes de script qui lisent les données d’un conteneur ou d’une file d’attente. Si le rôle **Contributeur aux données Blob** vous est attribué, vous pouvez exécuter des commandes de script qui lisent, écrivent ou suppriment un conteneur ou une file d’attente, ou les données qu’ils contiennent.

Pour plus d’informations sur les autorisations requises pour chaque opération Stockage Azure sur un conteneur ou une file d’attente, consultez [Appeler des opérations de stockage avec des jetons OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-azure-cli-commands-using-azure-ad-credentials"></a>Appeler des commandes Azure CLI à l’aide d’informations d’identification Azure AD

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

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser Azure CLI pour affecter un rôle RBAC pour l’accès à des données blob et de file d’attente](storage-auth-aad-rbac-cli.md)
- [Autoriser l’accès à des données blob et de files d’attente avec des identités managées pour les ressources Azure](storage-auth-aad-msi.md)
