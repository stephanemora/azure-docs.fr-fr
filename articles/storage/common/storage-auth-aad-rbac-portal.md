---
title: Utiliser le Portail Azure afin d’attribuer un rôle RBAC pour l’accès aux données
titleSuffix: Azure Storage
description: Découvrez comment utiliser le Portail Azure pour attribuer des autorisations à un principal de sécurité Azure Active Directory avec le contrôle d’accès en fonction du rôle (RBAC). Stockage Azure prend en charge les rôles RBAC intégrés et personnalisés pour l’authentification via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: e1544303ee7b792a00f7afb57fe62b7b86a300f8
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74891950"
---
# <a name="use-the-azure-portal-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Utiliser le Portail Azure afin d’attribuer un rôle RBAC pour l’accès aux données de blob et de file d’attente

Azure Active Directory (Azure AD) autorise les droits d’accès aux ressources sécurisées via [RBAC (contrôle d’accès en fonction du rôle)](../../role-based-access-control/overview.md). Stockage Azure définit un ensemble de rôles RBAC intégrés qui englobent les ensembles communs d’autorisations permettant d’accéder aux données blob ou de file d’attente.

Lorsqu’un rôle RBAC est attribué à un principal de sécurité Azure AD, Azure octroie l’accès à ces ressources pour ce principal de sécurité. L’accès peut être limité au niveau de l’abonnement, du groupe de ressources, du compte de stockage ou d’un conteneur ou d’une file d’attente individuelle. Un principal de sécurité Azure AD peut correspondre à un utilisateur, à un groupe, à un principal de service d’application ou à une [identité managée pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Cet article explique comment utiliser le portail Azure pour attribuer des rôles RBAC. Le portail Azure fournit une interface simple pour attribuer des rôles RBAC et gérer l’accès à vos ressources de stockage. Vous pouvez également attribuer des rôles RBAC pour les ressources d’objets blob et de files d’attente à l’aide des outils de ligne de commande Azure ou des API de gestion Stockage Azure. Pour plus d’informations sur les rôles RBAC pour les ressources de stockage, consultez [Authorize access to Azure blobs and queues using Azure Active Directory](storage-auth-aad.md) (Autoriser l’accès aux objets blob Azure et files d’attente à l’aide d’Azure Active Directory). 

## <a name="rbac-roles-for-blobs-and-queues"></a>Rôles RBAC pour objets blob et files d’attente

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Déterminer l’étendue de la ressource 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Attribuer des rôles RBAC à l’aide du portail Azure

Après avoir déterminé l’étendue appropriée pour une attribution de rôle, accédez à cette ressource dans le portail Azure. Affichez les paramètres **Contrôle d’accès (IAM)** pour la ressource et suivez ces instructions pour gérer les attributions de rôle :

1. Attribuez le rôle RBAC Stockage Azure approprié pour accorder l’accès à un principal de sécurité Azure AD.

1. Attribuez le rôle [Lecteur](../../role-based-access-control/built-in-roles.md#reader) Azure Resource Manager aux utilisateurs qui ont besoin d’accéder aux conteneurs ou files d’attente via le portail Azure à l’aide de leurs informations d’identification Azure AD. 

Les sections suivantes décrivent chacune de ces étapes plus en détail.

> [!NOTE]
> En tant que propriétaire de votre compte de stockage Azure, aucune autorisation d’accès aux données ne vous est automatiquement attribuée. Vous devez vous attribuer explicitement un rôle RBAC pour le Stockage Azure. Vous pouvez l’attribuer au niveau de votre abonnement, votre groupe de ressources ou votre compte de stockage, ou au niveau d’un conteneur ou d’une file d’attente.
> 
> Vous ne pouvez pas attribuer de rôle dans l’étendue d’un conteneur ou d’une file d’attente d’attente si votre compte de stockage a un espace de noms hiérarchique activé.

### <a name="assign-a-built-in-rbac-role"></a>Attribuer un rôle RBAC intégré

Avant d’attribuer un rôle à un principal de sécurité, veillez à tenir compte de l’étendue des autorisations que vous accordez. Passez en revue la section [Déterminer l’étendue de la ressource](#determine-resource-scope) pour décider de l’étendue appropriée.

La procédure illustrée ici attribue un rôle limité à un conteneur, mais vous pouvez suivre les mêmes étapes pour attribuer un rôle limité à une file d’attente : 

1. Dans le [Portail Azure](https://portal.azure.com), accédez à votre compte de stockage, puis affichez l’**Aperçu** du compte.
1. Sous Services, sélectionnez **Blobs**. 
1. Recherchez le conteneur pour lequel vous souhaitez attribuer un rôle, puis affichez les paramètres de ce conteneur. 
1. Sélectionnez **Contrôle d’accès (IAM)** pour afficher les paramètres de contrôle d’accès du conteneur. Sélectionnez l’onglet **Attributions de rôles** pour afficher la liste des attributions de rôles.

    ![Capture d’écran montrant les paramètres de contrôle d’accès du conteneur](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. Cliquez sur le bouton **Ajouter une attribution de rôle** pour ajouter un nouveau rôle.
1. Dans la fenêtre **Ajouter une attribution de rôle**, sélectionnez le rôle Stockage Azure que vous souhaitez attribuer. Recherchez ensuite le principal de sécurité auquel vous souhaitez attribuer ce rôle.

    ![Capture d’écran montrant comment attribuer un rôle RBAC](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Cliquez sur **Enregistrer**. L’identité à laquelle vous avez attribué le rôle apparaît sous ce dernier. Par exemple, l’image suivante montre que l’utilisateur ajouté a désormais des autorisations d’accès en lecture pour toutes les données du conteneur nommé *sample-container*.

    ![Capture d’écran montrant la liste des utilisateurs auxquels un rôle a été attribué](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

Vous pouvez suivre des étapes similaires pour attribuer un rôle dans l’étendue du compte de stockage, du groupe de ressources ou de l’abonnement.

### <a name="assign-the-reader-role-for-portal-access"></a>Attribuer le rôle Lecteur pour accéder au portail

Lorsque vous attribuez un rôle intégré ou personnalisé pour Stockage Azure à un principal de sécurité, vous accordez des autorisations à ce principal de sécurité pour effectuer des opérations sur les données dans votre compte de stockage. Les rôles **Lecteur de données** intégrés fournissent des autorisations de lecture pour les données dans un conteneur ou une file d’attente, tandis que les rôles **Contributeur aux données** intégrés permettent de lire, d’écrire et de supprimer des autorisations dans un conteneur ou une file d’attente. L’étendue des autorisations se limite à la ressource spécifiée.  

Par exemple, si vous attribuez le rôle **Contributeur aux données Blob du stockage** à l’utilisatrice Mary au niveau d’un conteneur nommé **exemple-container**, puis Mary se voit attribuer un accès en lecture, écriture et suppression à tous les objets blob dans ce conteneur.

Toutefois, si Mary souhaite afficher un objet blob dans le portail Azure, le rôle **Contributeur aux données Blob du stockage** ne fournit pas les autorisations suffisantes pour naviguer dans le portail et atteindre l’objet blob dans le but de le consulter. D’autres autorisations Azure AD sont requises pour naviguer dans le portail et afficher les autres ressources qui y sont visibles.

Si vos utilisateurs doivent être en mesure d’accéder aux objets blob dans le portail Azure, attribuez-leur un rôle RBAC supplémentaire, le rôle [Lecteur](../../role-based-access-control/built-in-roles.md#reader), à ces utilisateurs, au niveau du compte de stockage ou au-dessus. Le rôle **Lecteur** est un rôle d’Azure Resource Manager qui permet aux utilisateurs d’afficher les ressources de compte de stockage, mais pas de les modifier. Il ne fournit pas d’autorisations en lecture pour les données dans Stockage Azure, mais uniquement pour les ressources de gestion de compte.

Suivez ces étapes pour attribuer le rôle **Lecteur** afin qu’un utilisateur puisse accéder aux objets blob à partir du portail Azure. Dans cet exemple, l’attribution est étendue au compte de stockage :

1. Dans le [Portail Azure](https://portal.azure.com), accédez à votre compte de stockage.
1. Sélectionnez **Contrôle d’accès (IAM)** pour afficher les paramètres de contrôle d’accès du compte de stockage. Sélectionnez l’onglet **Attributions de rôles** pour afficher la liste des attributions de rôles.
1. Dans la fenêtre **Ajouter une attribution de rôle**, sélectionnez le rôle **Lecteur**. 
1. Dans le champ **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou principal du service Azure AD**.
1. Recherchez le principal de sécurité auquel vous souhaitez attribuer le rôle.
1. Enregistrez l’attribution de rôle.

> [!NOTE]
> L’attribution du rôle Lecteur est uniquement nécessaire pour les utilisateurs qui doivent accéder aux objets blob ou files d’attente à l’aide du portail Azure. 

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les rôles RBAC pour les ressources de stockage, consultez [Authorize access to Azure blobs and queues using Azure Active Directory](storage-auth-aad.md) (Autoriser l’accès aux objets blob Azure et files d’attente à l’aide d’Azure Active Directory). 
- Pour en savoir plus sur RBAC, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle (RBAC) ?](../../role-based-access-control/overview.md).
- Pour apprendre à attribuer et gérer les rôles RBAC avec Azure PowerShell, Azure CLI ou l’API REST, consultez les articles suivants :
    - [Gérer le contrôle d’accès en fonction du rôle (RBAC) avec Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Gérer le contrôle d’accès en fonction du rôle (RBAC) avec Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Gérer le contrôle d’accès en fonction du rôle (RBAC) avec l’API REST](../../role-based-access-control/role-assignments-rest.md)
- Pour apprendre à autoriser l’accès aux conteneurs et files d’attente à partir de vos applications de stockage, consultez [Utiliser Azure AD avec les applications de stockage Azure](storage-auth-aad-app.md).
