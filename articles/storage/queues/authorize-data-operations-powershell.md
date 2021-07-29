---
title: Exécuter des commandes PowerShell avec des informations d’identification Azure AD pour accéder aux données en file d’attente
titleSuffix: Azure Storage
description: PowerShell prend en charge la connexion à l’aide d’informations d’identification Azure AD pour exécuter des commandes sur les données du Stockage File d’attente Azure. Un jeton d’accès est fourni pour la session et utilisé pour autoriser les opérations d’appel. Les autorisations dépendent du rôle Azure attribué au principal de sécurité Azure AD.
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozgun
ms.date: 02/10/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 862c576ae86cd7d91a1bd73243caa0a68e3689c1
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111900536"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-queue-data"></a>Exécuter des commandes PowerShell avec des informations d’identification Azure AD pour accéder aux données en file d’attente

Stockage Azure fournit des extensions pour PowerShell qui vous permettent de vous connecter et d’exécuter des commandes de script avec des informations d’identification Azure Active Directory (Azure AD). Lorsque vous vous connectez à PowerShell avec les informations d’identification Azure AD, un jeton d’accès OAuth 2.0 est retourné. Ce jeton est utilisé automatiquement par PowerShell pour autoriser les opérations de données suivantes sur le Stockage File d’attente. Pour les opérations prises en charge, vous n’avez plus besoin de fournir une clé de compte ou le jeton SAP avec la commande.

Vous pouvez attribuer des autorisations sur les données de file d’attente à un principal de sécurité Azure AD via le contrôle d’accès en fonction du rôle Azure (Azure RBAC). Pour plus d’informations sur les rôles Azure dans Stockage Azure, consultez [Gérer les droits d’accès aux données Stockage Azure avec Azure RBAC](assign-azure-role-data-access.md).

## <a name="supported-operations"></a>Opérations prises en charge

Les extensions Stockage Azure sont prises en charge pour les opérations sur les données en file d’attente. Les opérations que vous pouvez appeler dépendent des autorisations accordées au principal de sécurité Azure AD avec lequel vous vous connectez à PowerShell. Les autorisations sur les files d’attente sont attribuées via Azure RBAC. Par exemple, si le rôle **Lecteur des données en file d’attente** vous a été attribué, vous pouvez exécuter des commandes de script qui lisent les données d’une file d’attente. Si le rôle **Contributeur aux données en file d’attente** vous a été attribué, vous pouvez exécuter des commandes de script qui lisent, écrivent ou suppriment une file d’attente ou les données qu’elle contient.

Pour plus d’informations sur les autorisations requises pour chaque opération Stockage Azure sur une file d’attente, consultez [Appeler des opérations de stockage avec des jetons OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).

> [!IMPORTANT]
> Quand un compte de stockage est verrouillé à l’aide d’un verrou **ReadOnly** Azure Resource Manager, l’opération [List Keys](/rest/api/storagerp/storageaccounts/listkeys) n’est pas autorisée pour ce compte de stockage. **List Keys** est une opération POST, et toutes les opérations POST sont empêchées lorsqu’un verrou **ReadOnly** est configuré pour le compte. Pour cette raison, lorsque le compte est verrouillé avec un verrou **ReadOnly**, les utilisateurs qui ne détiennent pas déjà les clés de compte doivent utiliser des informations d’identification Azure AD pour accéder aux données de file d’attente. Dans PowerShell, ajoutez le paramètre `-UseConnectedAccount` pour créer un objet **AzureStorageContext** à l’aide de vos informations d’identification Azure AD.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Appeler des commandes PowerShell à l’aide des informations d’identification Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pour utiliser Azure PowerShell pour vous connecter et exécuter les opérations suivantes sur le service Stockage Azure à l’aide des informations d’identification Azure AD, créez un contexte de stockage pour faire référence au compte de stockage en incluant le paramètre `-UseConnectedAccount`.

L’exemple suivant montre comment créer une file d’attente dans un nouveau compte de stockage à partir d’Azure PowerShell à l’aide de vos informations d’identification Azure AD. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

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

1. Avant de créer la file d’attente, attribuez-vous le rôle [Contributeur aux données en file d’attente du stockage](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor). Même si vous êtes le propriétaire du compte, vous avez besoin d’autorisations explicites pour effectuer des opérations de données sur le compte de stockage. Pour plus d’informations sur l’attribution de rôles Azure, consultez [Attribuer un rôle Azure pour l’accès aux données de file d’attente](assign-azure-role-data-access.md).

    > [!IMPORTANT]
    > La propagation des attributions de rôles Azure peut prendre plusieurs minutes.

1. Créez une file d’attente en appelant [New-AzStorageQueue](/powershell/module/az.storage/new-azstoragequeue). Comme cet appel utilise le contexte créé dans les étapes précédentes, la file d’attente est créée à l’aide de vos informations d’identification Azure AD.

    ```powershell
    $queueName = "sample-queue"
    New-AzStorageQueue -Name $queueName -Context $ctx
    ```

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer un rôle Azure pour l’accès aux données de file d’attente](assign-azure-role-data-access.md)
- [Autoriser l’accès à des données blob et de files d’attente avec des identités managées pour les ressources Azure](../common/storage-auth-aad-msi.md)
