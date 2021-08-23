---
title: Choisir comment autoriser l’accès à des données de files d’attente dans le portail Azure
titleSuffix: Azure Storage
description: Lorsque vous accédez aux données de file d’attente à l’aide du Portail Azure, le portail fait des requêtes à Azure Storage en arrière-plan. Ces requêtes au stockage Azure peuvent être authentifiées et autorisées à l’aide de votre compte Azure AD ou de la clé d’accès au compte de stockage.
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozguns
ms.date: 06/08/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: contperf-fy21q1
ms.openlocfilehash: 35f4e76a437c5ea5926a95b199e433c6e5b1eb76
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111901256"
---
# <a name="choose-how-to-authorize-access-to-queue-data-in-the-azure-portal"></a>Choisir comment autoriser l’accès à des données de files d’attente dans le portail Azure

Lorsque vous accédez aux données de file d’attente à l’aide du [Portail Azure](https://portal.azure.com), le portail fait des requêtes à Azure Storage en arrière-plan. Une requête au stockage Azure peut être autorisée à l’aide de votre compte Azure AD ou de la clé d’accès au compte de stockage. Le portail indique quelle méthode vous utilisez et vous permet de basculer entre les deux si vous disposez des autorisations appropriées.

## <a name="permissions-needed-to-access-queue-data"></a>Autorisations nécessaires pour accéder aux données de file d’attente

Selon la façon dont vous souhaitez autoriser l’accès aux données de la file d’attente dans le portail Azure, vous devrez disposer d’autorisations particulières. Dans la plupart des cas, ces autorisations sont fournies via le contrôle d’accès en fonction du rôle Azure (Azure RBAC). Pour plus d’informations sur Azure RBAC, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle Azure (Azure RBAC) ?](../../role-based-access-control/overview.md)

### <a name="use-the-account-access-key"></a>Utiliser la clé d'accès au compte

Pour accéder aux données de file d’attente avec la clé d’accès au compte, vous devez disposer d’un rôle Azure qui vous est attribué et qui inclut l’action Azure RBAC **Microsoft.Storage/storageAccounts/listkeys/action**. Ce rôle Azure peut être intégré ou il peut s’agir d’un rôle personnalisé. Les rôles intégrés qui prennent en charge **Microsoft.Storage/storageAccounts/listkeys/action** incluent les suivants, dans l’ordre croissant des autorisations :

- Le rôle [Lecteur et accès aux données](../../role-based-access-control/built-in-roles.md#reader-and-data-access)
- Le rôle [Contributeur de compte de stockage](../../role-based-access-control/built-in-roles.md#storage-account-contributor)
- Le rôle [Contributeur](../../role-based-access-control/built-in-roles.md#contributor) d’Azure Resource Manager
- Le rôle [Propriétaire](../../role-based-access-control/built-in-roles.md#owner) d’Azure Resource Manager

Lorsque vous tentez d’accéder aux données de file d’attente dans le Portail Microsoft Azure, le portail commence par vérifier si un rôle RBAC vous a été attribué avec **Microsoft.Storage/storageAccounts/listkeys/action**. Si un rôle avec cette action vous a été attribué, le portail utilise la clé de compte pour l’accès aux données de file d’attente. Si un rôle avec cette action ne vous a pas été attribué, le portail tente d’accéder aux données à l’aide de votre compte Azure AD.

> [!IMPORTANT]
> Quand un compte de stockage est verrouillé à l’aide d’un verrou **ReadOnly** Azure Resource Manager, l’opération [Répertorier les clés](/rest/api/storagerp/storageaccounts/listkeys) n’est pas autorisée pour ce compte de stockage. **Répertorier les clés** est une opération POST, et toutes les opérations POST sont empêchées lorsqu’un verrou **ReadOnly** est configuré pour le compte. Pour cette raison, lorsque le compte est verrouillé avec un verrou **ReadOnly**, les utilisateurs doivent utiliser des informations d’identification Azure AD pour accéder aux données mises en file d’attente dans le portail. Pour plus d’informations sur l’accès aux données mises en file d’attente dans le portail avec Azure AD, consultez [Utiliser votre compte Azure AD](#use-your-azure-ad-account).

> [!NOTE]
> Les rôles d’administrateur d’abonnement classique **Administrateur de service** et **Co-administrateur** incluent l’équivalent du rôle [`Owner`](../../role-based-access-control/built-in-roles.md#owner) d’Azure Resource Manager. Le rôle **Propriétaire** inclut toutes les actions, y compris **Microsoft.Storage/storageAccounts/listkeys/action**, pour qu’un utilisateur avec l’un de ces rôles d’administration puisse accéder également aux données de file d’attente avec la clé de compte. Pour plus d’informations, consultez [Rôles d’administrateur d’abonnement classique, rôles Azure et rôles d’administrateur Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Utiliser votre compte Azure AD

Pour accéder aux données de file d’attente à partir du Portail Microsoft Azure à l’aide de votre compte Azure AD, vous devez remplir ces deux conditions :

- Un rôle intégré ou personnalisé qui fournit l’accès aux données de file d’attente vous a été attribué.
- Le rôle [Lecteur](../../role-based-access-control/built-in-roles.md#reader) Azure Resource Manager vous a été attribué, au moins, limité au niveau du compte de stockage ou à un niveau supérieur. Le rôle **Lecteur** octroie les autorisations les plus restreintes, mais l’utilisation d’un autre rôle Azure Resource Manager accordant l’accès aux ressources de gestion de compte de stockage est également acceptable.

Le rôle **Lecteur** est un rôle d’Azure Resource Manager qui permet aux utilisateurs d’afficher les ressources de compte de stockage, mais pas de les modifier. Il ne fournit pas d’autorisations en lecture pour les données dans Stockage Azure, mais uniquement pour les ressources de gestion de compte. Le rôle **Lecteur** est nécessaire pour que les utilisateurs puissent accéder aux files d’attente du portail Azure.

Pour plus d’informations sur les rôles intégrés qui prennent en charge l’accès aux données de la file d’attente, consultez [Rôles Azure pour les files d’attente](assign-azure-role-data-access.md#azure-roles-for-queues).

Les rôles personnalisés peuvent prendre en charge différentes combinaisons des mêmes autorisations fournies par les rôles intégrés. Pour plus d’informations sur la création de rôles Azure personnalisés, consultez [Rôles personnalisés Azure](../../role-based-access-control/custom-roles.md) et [Présentation des définitions de rôles pour les ressources Azure](../../role-based-access-control/role-definitions.md).

> [!NOTE]
> La préversion d’Explorateur Stockage dans le portail Azure ne prend pas en charge l’utilisation d’informations d’identification Azure AD pour visualiser et modifier des données de file d’attente. Explorateur Stockage dans le portail Azure utilise toujours les clés de compte pour accéder aux données. Pour utiliser Explorateur Stockage dans le portail Azure, un rôle qui comprend **Microsoft.Storage/storageAccounts/listkeys/action** doit vous être attribué.

## <a name="navigate-to-queues-in-the-azure-portal"></a>Accéder aux files d’attente dans le Portail Azure

Pour afficher les données de file d’attente sur le portail, accédez à la **Vue d’ensemble** pour votre compte de stockage, puis cliquez sur les liens pour **Files d’attente**. Vous pouvez également naviguer jusqu’à la section **Service de file d’attente** dans le menu.

:::image type="content" source="media/authorize-data-operations-portal/queue-access-portal.png" alt-text="Capture d’écran montrant comment accéder aux données de file d'attente dans le portail Azure":::

## <a name="determine-the-current-authentication-method"></a>Déterminer la méthode d’authentification actuelle

Lorsque vous accédez à une file d’attente, le portail Azure indique si vous utilisez actuellement la clé d’accès au compte ou votre compte Azure AD pour vous authentifier.

### <a name="authenticate-with-the-account-access-key"></a>S'authentifier à l'aide de la clé d'accès au compte

Si vous vous authentifiez à l’aide de la clé d’accès au compte, vous verrez la méthode **Clé d’accès** spécifiée comme méthode d’authentification dans le portail :

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="Capture d’écran montrant l’utilisateur qui accède actuellement aux files d’attente à l’aide de la clé de compte":::

Pour passer à l’utilisation du compte Azure AD, cliquez sur le lien mis en surbrillance dans l’image. Si vous disposez des autorisations appropriées via les rôles Azure qui vous sont attribués, vous pourrez poursuivre. Toutefois, si vous n’avez pas les autorisations appropriées, vous verrez un message d’erreur semblable à celui-ci :

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Erreur affichée si le compte Azure AD ne prend pas en charge l’accès":::

Notez qu’aucune file d'attente n’apparaît dans la liste si votre compte Azure AD ne dispose pas des autorisations pour les afficher. Cliquez sur le lien **Basculer sur clé d’accès** pour utiliser à nouveau la clé d’accès pour l’authentification.

### <a name="authenticate-with-your-azure-ad-account"></a>S'authentifier à l'aide du compte Azure AD

Si vous vous authentifiez à l’aide de votre compte Azure AD, vous verrez la méthode **Clé d’accès** spécifiée comme méthode d’authentification dans le portail :

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Capture d’écran montrant l’utilisateur qui accède actuellement aux files d’attente à l’aide du compte Azure AD":::

Pour passer à l’utilisation d’une clé d’accès au compte, cliquez sur le lien mis en surbrillance dans l’image. Si vous avez accès à la clé de compte, vous pourrez poursuivre. Toutefois, si vous n’avez pas accès à la clé de compte, le Portail Azure affiche un message d’erreur.

Les files d’attente ne sont pas répertoriées dans le portail si vous n’avez pas accès aux clés de compte. Cliquez sur le lien **Basculer sur Compte d’utilisateur Azure AD** pour vous authentifier à nouveau.

## <a name="next-steps"></a>Étapes suivantes

- [Authentifier l’accès aux objets blob et aux files d’attente Azure à l’aide d’Azure Active Directory](../common/storage-auth-aad.md)
- [Attribuer un rôle Azure pour l’accès aux données de file d’attente](assign-azure-role-data-access.md)