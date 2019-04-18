---
title: Utiliser le portail Azure pour accéder aux objets blob ou file d’attente de données - stockage Azure
description: Lorsque vous accéder aux objets blob ou les données de file d’attente à l’aide du portail Azure, le portail fait des demandes à Azure Storage en arrière-plan. Ces requêtes dans stockage Azure peuvent être authentifiés et autorisé à l’aide de votre compte Azure AD ou la clé d’accès de compte stockage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: b56cbfbc9dfde8b1a7d43d55ee85c34fde385902
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58846379"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Utiliser le portail Azure pour accéder aux données blob ou file d’attente

Lorsque vous accédez à objets blob ou les données de la file d’attente à l’aide du [Azure portal](https://portal.azure.com), le portail facilite les demandes vers le stockage Azure en arrière-plan. Ces requêtes dans stockage Azure peuvent être authentifiés et autorisé à l’aide de votre compte Azure AD ou la clé d’accès de compte stockage. Le portail indique quelle méthode d’authentification que vous utilisez et vous permet de basculer entre les deux si vous disposez des autorisations appropriées.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Autorisations nécessaires pour accéder aux données blob ou file d’attente

Selon la façon dont vous souhaitez authentifier l’accès des données blob ou file d’attente dans le portail Azure, vous aurez besoin des autorisations spécifiques. Dans la plupart des cas, ces autorisations sont fournies via le contrôle d’accès en fonction du rôle (RBAC). Pour plus d’informations sur RBAC, consultez [quel est le contrôle d’accès en fonction du rôle (RBAC) ?](../../role-based-access-control/overview.md).

### <a name="account-access-key"></a>Clé d’accès de compte

Pour accéder aux données blob et file d’attente avec la clé d’accès de compte, vous devez disposer d’un rôle RBAC qui vous sont assigné et qui inclut l’action RBAC **Microsoft.Storage/storageAccounts/listkeys/action**. Ce rôle RBAC peut être intégré ou un rôle personnalisé. Rôles intégrés qui prennent en charge **Microsoft.Storage/storageAccounts/listkeys/action** incluent :

- Le Gestionnaire de ressources Azure [propriétaire](../../role-based-access-control/built-in-roles.md#owner) rôle
- Le Gestionnaire de ressources Azure [contributeur](../../role-based-access-control/built-in-roles.md#contributor) rôle
- Le [collaborateur de compte de stockage](../../role-based-access-control/built-in-roles.md#storage-account-contributor) rôle

Lorsque vous tentez d’accéder aux données d’objet blob ou file d’attente dans le portail Azure, le portail vérifie d’abord si vous avez été attribué un rôle avec **Microsoft.Storage/storageAccounts/listkeys/action**. Si vous avez été affecté à un rôle avec cette action, le portail utilise la clé de compte pour accéder aux données blob et file d’attente. Si vous n’avez pas été affecté un rôle avec cette action, le portail tente d’accéder aux données à l’aide de votre compte Azure AD.

> [!NOTE]
> Les rôles d’administrateur d’abonnements classic administrateur de Service et Coadministrateur incluent l’équivalent d’Azure Resource Manager [propriétaire](../../role-based-access-control/built-in-roles.md#owner) rôle. Le **propriétaire** rôle inclut toutes les actions, y compris le **Microsoft.Storage/storageAccounts/listkeys/action**, pour un utilisateur avec l’un de ces rôles d’administration puisse accéder également aux données blob et file d’attente avec le clé de compte. Pour plus d’informations, consultez [rôles d’administrateur classique abonnement](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Compte Azure AD

Pour accéder aux données d’objet blob ou file d’attente à partir du portail Azure à l’aide de votre compte Azure AD, il se peut que les deux instructions suivantes doivent être remplies pour vous :

- Vous avez été assigné à Azure Resource Manager [lecteur](../../role-based-access-control/built-in-roles.md#reader) rôle, au minimum, avec étendue au niveau du compte de stockage ou une version ultérieure. Le **lecteur** rôle accorde les autorisations plus limitées, mais un autre rôle d’Azure Resource Manager qui accorde l’accès aux ressources de gestion de compte de stockage est également acceptable.
- Vous avez été affecté soit un rôle intégré ou personnalisé qui fournit l’accès aux objets BLOB ou les données de la file d’attente.

Le **lecteur** attribution de rôle ou une autre attribution de rôle Azure Resource Manager est nécessaire pour que l’utilisateur peut afficher et parcourir les ressources de gestion de compte de stockage dans le portail Azure. Les rôles RBAC qui accordent l’accès aux données d’objet blob ou file d’attente n’accordent pas d’accès aux ressources de gestion de compte de stockage. Pour accéder aux données d’objet blob ou file d’attente dans le portail, l’utilisateur a besoin d’autorisations pour parcourir les ressources de compte de stockage. Pour plus d’informations sur cette exigence, consultez [affecter le rôle de lecteur pour accéder au portail](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

Les rôles intégrés qui prennent en charge les accès à vos données d’objet blob ou file d’attente sont les suivantes :

- [Propriétaire des données de stockage Blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Pour POSIX contrôle d’accès pour le stockage Azure Data Lake Gen2 (version préliminaire).
- [Contributeur aux données stockage Blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Autorisations en lecture/écriture/suppression pour les objets BLOB.
- [Lecteur de données de stockage Blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Autorisations en lecture seule pour les objets BLOB.
- [Contributeur de données de file d’attente de stockage](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Autorisations en lecture/écriture/suppression des files d’attente.
- [Lecteur de données de file d’attente de stockage](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): Autorisations en lecture seule pour les files d’attente.
    
Rôles personnalisés peuvent prendre en charge différentes combinaisons des mêmes autorisations fournies par les rôles intégrés. Pour plus d’informations sur la création des rôles RBAC personnalisés, consultez [rôles personnalisés pour les ressources Azure](../../role-based-access-control/custom-roles.md) et [comprendre les définitions de rôles pour les ressources Azure](../../role-based-access-control/role-definitions.md).

> [!NOTE]
> Liste des files d’attente avec un rôle d’administrateur d’abonnement classique n’est pas pris en charge. Pour répertorier les files d’attente, un utilisateur doit avoir affecté les Azure Resource Manager **lecteur** rôle, le **lecteur de données de file d’attente de stockage** rôle, ou le **contributeur de données de file d’attente de stockage** rôle.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Accédez à des objets BLOB ou les files d’attente dans le portail

Pour afficher les données d’objet blob ou file d’attente dans le portail, accédez à la **vue d’ensemble** pour votre compte de stockage, puis cliquez sur les liens pour **Blobs** ou **files d’attente**. Vous pouvez également naviguer jusqu'à la **service Blob** et **service file d’attente** sections dans le menu. 

![Accédez aux données d’objet blob ou file d’attente dans le portail Azure](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Déterminer la méthode d’authentification actuel

Lorsque vous accédez à un conteneur ou une file d’attente, le portail Azure indique si vous utilisez actuellement la clé d’accès de compte ou votre compte Azure AD pour s’authentifier.

Les exemples de cette section montrent l’accès à un conteneur et ses objets BLOB, mais le portail affiche le même message lors de l’accès à une file d’attente et de ses messages, ou de liste de files d’attente.

### <a name="account-access-key"></a>Clé d’accès de compte

Si vous vous authentifiez à l’aide de la clé d’accès de compte, vous verrez **clé d’accès** spécifié en tant que la méthode d’authentification dans le portail :

![Actuellement l’accès aux données de conteneur avec la clé de compte](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Pour passer à l’aide de compte Azure AD, cliquez sur le lien mis en surbrillance dans l’image. Si vous avez les autorisations appropriées via les rôles RBAC qui vous sont assignés, vous serez en mesure de continuer. Toutefois, si vous n’avez pas les autorisations appropriées, vous verrez un message d’erreur semblable à celui-ci :

![Erreur affichée si compte Azure AD ne prend pas en charge les accès](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Notez qu’aucun objet BLOB n’apparaître dans la liste si votre compte Azure AD ne dispose pas des autorisations pour les afficher. Cliquez sur le **basculer vers la clé d’accès** lien à utiliser la clé d’accès pour l’authentification à nouveau.

### <a name="azure-ad-account"></a>Compte Azure AD

Si vous vous authentifiez à l’aide de votre compte Azure AD, vous verrez **compte d’utilisateur Azure AD** spécifié en tant que la méthode d’authentification dans le portail :

![Actuellement l’accès aux données de conteneur avec un compte Azure AD](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Pour passer à l’aide de la clé d’accès de compte, cliquez sur le lien mis en surbrillance dans l’image. Si vous avez accès à la clé de compte, vous serez en mesure de continuer. Toutefois, si vous n’avez pas accès à la clé de compte, vous verrez un message d’erreur semblable à celui-ci :

![Erreur affichée si vous n’avez pas d’accès à la clé de compte](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Notez qu’aucun objet BLOB n’apparaître dans la liste si vous n’avez pas accès aux clés du compte. Cliquez sur le **basculer vers le compte d’utilisateur Azure AD** lien à utiliser votre compte Azure AD pour authentifier à nouveau.

## <a name="next-steps"></a>Étapes suivantes

- [Authentifier l’accès aux objets BLOB Azure et files d’attente à l’aide d’Azure Active Directory](storage-auth-aad.md)
- [Accorder l’accès à des conteneurs Azure et files d’attente avec RBAC dans le portail Azure](storage-auth-aad-rbac-portal.md)
- [Accorder l’accès à des données d’objet blob et file d’attente Azure avec RBAC à l’aide d’Azure CLI](storage-auth-aad-rbac-cli.md)
- [Accorder l’accès à des données d’objet blob et file d’attente Azure avec RBAC à l’aide de PowerShell](storage-auth-aad-rbac-powershell.md)
