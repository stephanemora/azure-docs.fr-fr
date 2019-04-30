---
title: Utiliser Azure CLI pour gérer les droits d’accès Azure AD à des données d’objet blob et file d’attente avec RBAC - stockage Azure
description: Utiliser Azure CLI pour attribuer l’accès aux conteneurs et les files d’attente avec le contrôle d’accès en fonction du rôle (RBAC). Stockage Azure prend en charge les rôles RBAC intégrés et personnalisés pour l’authentification via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: de8cb99ae5db93c2438a9ea982ad1c6c9324b47f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483597"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-azure-cli"></a>Accorder l’accès à des données d’objet blob et file d’attente Azure avec RBAC à l’aide d’Azure CLI

Azure Active Directory (Azure AD) autorise les droits d’accès aux ressources sécurisées via [RBAC (contrôle d’accès en fonction du rôle)](../../role-based-access-control/overview.md). Stockage Azure définit un ensemble de rôles RBAC intégrés qui englobent des ensembles communs d’autorisations permettant d’accéder aux données d’objet blob ou file d’attente. 

Lorsqu’un rôle RBAC est attribué à un principal de sécurité Azure AD, Azure accorde l’accès à ces ressources pour cette entité de sécurité. L’accès peut être limité au niveau de l’abonnement, du groupe de ressources, du compte de stockage ou d’un conteneur ou d’une file d’attente individuelle. Un principal de sécurité Azure AD peut être un utilisateur, un groupe, un principal de service d’application, ou un [identité managée pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Cet article décrit comment utiliser Azure CLI pour répertorier les rôles RBAC intégrés et les attribuer aux utilisateurs. Pour plus d’informations sur l’utilisation d’Azure CLI, consultez [les Interface de ligne de commande (CLI) Azure](https://docs.microsoft.com/cli/azure).

## <a name="rbac-roles-for-blobs-and-queues"></a>Rôles RBAC pour objets blob et files d’attente

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Déterminer la portée de la ressource 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Répertorier les rôles RBAC disponibles

Pour répertorier les rôles RBAC intégrés disponibles avec Azure CLI, utilisez le [liste des définitions de rôle az](/cli/azure/role/definition#az-role-definition-list) commande :

```azurecli-interactive
az role definition list --out table
```

Vous verrez les rôles intégrés de données Azure Storage répertoriées, ainsi que d’autres rôles intégrés pour Azure :

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-user"></a>Attribuer un rôle RBAC à un utilisateur

Pour affecter un rôle RBAC à un utilisateur, utilisez la [créer d’attribution de rôle az](/cli/azure/role/assignment#az-role-assignment-create) commande. Le format de la commande peut varier en sur l’étendue de l’assignation. Les exemples suivants montrent comment affecter un rôle à un utilisateur dans différentes portées.

### <a name="container-scope"></a>Étendue du conteneur

Pour affecter un rôle consacré à un conteneur, spécifiez une chaîne contenant l’étendue du conteneur pour le `--scope` paramètre. L’étendue d’un conteneur est sous la forme :

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

L’exemple suivant affecte la **contributeur aux données stockage Blob** rôle à un utilisateur, limité à un conteneur nommé *exemple-container*. Veillez à remplacer les exemples de valeurs et les valeurs d’espace réservé entre crochets par vos propres valeurs : 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Étendue de la file d’attente

Pour affecter un rôle consacré à une file d’attente, spécifiez une chaîne contenant l’étendue de la file d’attente pour le `--scope` paramètre. L’étendue d’une file d’attente est sous la forme :

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

L’exemple suivant affecte la **contributeur de données de file d’attente de stockage** rôle à un utilisateur, une portée limité à une file d’attente nommée *exemple-file d’attente*. Veillez à remplacer les exemples de valeurs et les valeurs d’espace réservé entre crochets par vos propres valeurs : 

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Portée de compte de stockage

Pour affecter un rôle consacré au compte de stockage, spécifiez l’étendue de la ressource de compte de stockage pour le `--scope` paramètre. L’étendue d’un compte de stockage est sous la forme :

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

L’exemple suivant montre comment affecter le **lecteur de données de stockage Blob** rôle à un utilisateur au niveau du compte de stockage. Veillez à remplacer les valeurs d’exemple par vos propres valeurs : 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/storagesamples"
```

### <a name="resource-group-scope"></a>Groupe de ressources

Pour affecter un rôle consacré au groupe de ressources, spécifiez le nom de groupe de ressources ou l’ID pour le `--resource-group` paramètre. L’exemple suivant affecte la **lecteur de données de file d’attente de stockage** rôle à un utilisateur au niveau du groupe de ressources. Veillez à remplacer les exemples de valeurs et les valeurs des espaces réservés entre crochets par vos propres valeurs : 

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group sample-resource-group
```

### <a name="subscription-scope"></a>Étendue de l’abonnement

Pour affecter un rôle consacré à l’abonnement, spécifiez l’étendue pour l’abonnement pour le `--scope` paramètre. L’étendue d’un abonnement est sous la forme :

```
/subscriptions/<subscription>
```

L’exemple suivant montre comment affecter le **lecteur de données de stockage Blob** rôle à un utilisateur au niveau du compte de stockage. Veillez à remplacer les valeurs d’exemple par vos propres valeurs : 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>"
```

## <a name="next-steps"></a>Étapes suivantes

- [Gérer l’accès aux ressources Azure à l’aide du contrôle RBAC et d’Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Accorder l’accès à des données d’objet blob et file d’attente Azure avec RBAC à l’aide d’Azure PowerShell](storage-auth-aad-rbac-powershell.md)
- [Accorder l’accès aux données blob et file d’attente Azure avec RBAC dans le portail Azure](storage-auth-aad-rbac-portal.md)
