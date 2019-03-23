---
title: Utiliser le portail Azure pour gérer les droits d’accès Azure AD aux conteneurs et les files d’attente avec RBAC - stockage Azure | Microsoft Docs
description: Utilisez le contrôle d’accès en fonction du rôle (RBAC) à partir du portail Azure pour attribuer l’accès aux conteneurs et les files d’attente pour les principaux de sécurité. Stockage Azure prend en charge les rôles RBAC intégrés et personnalisés pour l’authentification via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 66e6cff40d71842ef19f99d7c96219af83fc9b4e
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368236"
---
# <a name="grant-access-to-azure-containers-and-queues-with-rbac-in-the-azure-portal"></a>Accorder l’accès à des conteneurs Azure et files d’attente avec RBAC dans le portail Azure

Azure Active Directory (Azure AD) autorise les droits d’accès aux ressources sécurisées via [RBAC (contrôle d’accès en fonction du rôle)](../../role-based-access-control/overview.md). Le Stockage Azure définit un ensemble de rôles RBAC intégrés qui englobent les ensembles communs d’autorisations permettant d’accéder aux conteneurs ou aux files d’attente. 

Lorsqu’un rôle RBAC est attribué à un principal de sécurité Azure AD, Azure accorde l’accès à ces ressources pour cette entité de sécurité. L’accès peut être limité au niveau de l’abonnement, du groupe de ressources, du compte de stockage ou d’un conteneur ou d’une file d’attente individuelle. Un principal de sécurité Azure AD peut être un utilisateur, un groupe, un principal de service d’application, ou un [identité managée pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Cet article décrit comment utiliser le portail Azure pour attribuer des rôles RBAC. Le portail Azure fournit une interface simple pour affecter des rôles RBAC et gestion de l’accès à vos ressources de stockage. Vous pouvez également affecter des rôles RBAC pour les ressources de blob et file d’attente à l’aide des outils de ligne de commande Azure ou les API de gestion de stockage Azure. Pour plus d’informations sur les rôles RBAC pour les ressources de stockage, consultez [authentifier l’accès à Azure d’objets BLOB et files d’attente à l’aide d’Azure Active Directory](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>Rôles RBAC pour objets blob et files d’attente

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Déterminer la portée de la ressource 

Avant de vous attribuez un rôle RBAC à un principal de sécurité, déterminez l’étendue d’accès que l’entité de sécurité doit avoir. Les méthodes conseillées préconisent qu’il est toujours préférable d’accorder la plus petite portée possible.

La liste suivante décrit les niveaux à laquelle vous pouvez étendre l’accès aux ressources d’objets blob et file d’attente Azure, en commençant par la plus petite portée :

- **Un conteneur spécifique.** Dans cette étendue, une entité de sécurité a accès à tous les objets BLOB dans le conteneur, ainsi que les propriétés du conteneur et les métadonnées.
- **Une file d’attente individuel.** Dans cette étendue, une entité de sécurité a accès à des messages dans la file d’attente, ainsi que les propriétés de la file d’attente et les métadonnées.
- **Le compte de stockage.** À ce niveau, un principal de sécurité a accès à tous les conteneurs et leurs objets BLOB, ou toutes les files d’attente et leurs messages.
- **Le groupe de ressources.** Dans cette étendue, une entité de sécurité a accès à tous les conteneurs ou des files d’attente dans tous les comptes de stockage dans le groupe de ressources.
- **L’abonnement.** Dans cette étendue, une entité de sécurité a accès à tous les conteneurs ou des files d’attente dans tous les comptes de stockage dans tous les groupes de ressources dans l’abonnement.

Lorsque vous avez déterminé l’étendue souhaitée pour une attribution de rôle, accédez à la ressource appropriée dans le portail Azure. Afficher le **contrôle d’accès (IAM)** paramètres pour la ressource et suivez les instructions dans les sections suivantes pour gérer les attributions de rôle.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Attribuer des rôles RBAC à l’aide du portail Azure

Accorder l’accès aux ressources d’objets blob et file d’attente avec les informations d’identification Azure AD dans le portail Azure implique les étapes suivantes : 

1. Attribuez le rôle RBAC de stockage Azure approprié pour accorder l’accès aux conteneurs ou des files d’attente. Pour un accès en lecture, affecter le **lecteur des données Blob** ou **lecteur de données de file d’attente** rôle. Pour l’accès en lecture, écriture et delete, affecter le **contributeur aux données Blob** ou **contributeur de données de file d’attente** rôle. Vous pouvez également affecter un rôle personnalisé.

1. Affecter le Gestionnaire de ressources Azure [lecteur](../../role-based-access-control/built-in-roles.md#reader) rôle aux utilisateurs qui doivent accéder aux conteneurs ou des files d’attente via le portail Azure à l’aide de leurs informations d’identification Azure AD. 

Les sections suivantes décrivent chacune de ces étapes plus en détail.

### <a name="assign-a-built-in-rbac-role"></a>Attribuer un rôle RBAC intégré

Avant d’affecter un rôle à un principal de sécurité, veillez à prendre en compte l’étendue des autorisations que vous accordez. Examinez le [déterminer la portée de la ressource](#determine-resource-scope) section pour déterminer l’étendue appropriée.

La procédure illustrée ici attribue un rôle limité à un conteneur, mais vous pouvez suivre les mêmes étapes pour attribuer un rôle limité à une file d’attente : 

1. Dans le [Portail Azure](https://portal.azure.com), accédez à votre compte de stockage, puis affichez la **Vue d’ensemble** du compte.
1. Sous Services, sélectionnez **Blobs**. 
1. Recherchez le conteneur pour lequel vous souhaitez attribuer un rôle, puis affichez les paramètres de ce conteneur. 
1. Sélectionnez **Contrôle d’accès (IAM)** pour afficher les paramètres de contrôle d’accès du conteneur. Sélectionnez l’onglet **Attributions de rôles** pour afficher la liste des attributions de rôles.

    ![Capture d’écran montrant les paramètres de contrôle d’accès conteneur](media/storage-auth-aad-rbac/portal-access-control-container.png)

1. Cliquez sur le bouton **Ajouter une attribution de rôle** pour ajouter un nouveau rôle.
1. Dans le **ajouter une attribution de rôle** fenêtre, sélectionnez le rôle de stockage Azure que vous souhaitez affecter. Puis rechercher pour localiser l’entité de sécurité auquel vous souhaitez attribuer ce rôle.

    ![Capture d’écran montrant comment attribuer un rôle RBAC](media/storage-auth-aad-rbac/add-rbac-role.png)

1. Cliquez sur **Enregistrer**. L’identité à laquelle vous avez attribué le rôle apparaît sous ce dernier. Par exemple, l’image suivante montre que l’utilisateur ajouté a désormais des autorisations d’accès en lecture pour toutes les données du conteneur nommé *sample-container*.

    ![Capture d’écran affichant la liste des utilisateurs affectés à un rôle](media/storage-auth-aad-rbac/container-scoped-role.png)

Vous pouvez suivre des étapes similaires pour affecter un rôle consacré au compte de stockage, un groupe de ressources ou un abonnement.

> [!NOTE]
> En tant que propriétaire de votre compte de stockage Azure, aucune autorisation d’accès aux données ne vous est automatiquement attribuée. Vous devez vous attribuer explicitement un rôle RBAC pour le Stockage Azure. Vous pouvez l’attribuer au niveau de votre abonnement, votre groupe de ressources ou votre compte de stockage, ou au niveau d’un conteneur ou d’une file d’attente.
> 
> Vous ne pouvez pas affecter un rôle consacré à un conteneur ou d’une file d’attente si votre compte de stockage a un espace de noms hiérarchique activé.

### <a name="assign-the-reader-role-for-portal-access"></a>Affecter le rôle de lecteur pour accéder au portail

Lorsque vous affectez un rôle intégré ou personnalisé pour le stockage Azure à un principal de sécurité, vous accordez des autorisations à ce principal de sécurité pour effectuer des opérations sur les données dans votre compte de stockage. Intégrés **lecteur de données** rôles fournissent des autorisations de lecture pour les données dans un conteneur ou d’une file d’attente, tout en intégrés **contributeur aux données** les rôles permettent de lire, écrire et supprimer des autorisations à un conteneur ou file d’attente. Autorisations se limitent à la ressource spécifiée.  

Par exemple, si vous assignez le **contributeur aux données stockage Blob** rôle à l’utilisateur Marie au niveau d’un conteneur nommé **exemple-container**puis Mary de lecture, écriture et supprimer l’accès à tous les objets BLOB dans ce conteneur.

Toutefois, si Mary souhaite afficher un objet blob dans le portail Azure, puis le **contributeur aux données stockage Blob** rôle par lui-même ne fournira pas d’autorisations suffisantes pour naviguer dans le portail pour l’objet blob pour la consulter. Autres autorisations Azure AD sont requis pour naviguer dans le portail et afficher les autres ressources qui sont visibles il.

Si vos utilisateurs doivent être en mesure d’accéder aux objets BLOB dans le portail Azure, puis attribuez-leur un rôle RBAC supplémentaire, le [lecteur](../../role-based-access-control/built-in-roles.md#reader) rôle, à ces utilisateurs, au niveau du compte de stockage ou une version ultérieure. Le **lecteur** rôle est un rôle d’Azure Resource Manager qui permet aux utilisateurs d’afficher les ressources de compte de stockage, mais pas les modifier. Il ne fournit pas d’autorisations en lecture aux données dans le stockage Azure, mais uniquement aux ressources de gestion de compte.

Suivez ces étapes pour affecter le **lecteur** rôle afin qu’un utilisateur peut accéder aux objets BLOB à partir du portail Azure. Dans cet exemple, l’attribution est étendue pour le compte de stockage :

1. Dans le [Portail Azure](https://portal.azure.com), accédez à votre compte de stockage.
1. Sélectionnez **contrôle d’accès (IAM)** pour afficher les paramètres de contrôle d’accès pour le compte de stockage. Sélectionnez l’onglet **Attributions de rôles** pour afficher la liste des attributions de rôles.
1. Dans le **ajouter une attribution de rôle** fenêtre, sélectionnez le **lecteur** rôle. 
1. À partir de la **attribuer l’accès à** champ, sélectionnez **utilisateur, groupe ou principal du service Azure AD**.
1. Recherche pour localiser le principal de sécurité auquel vous souhaitez affecter le rôle.
1. Enregistrez l’attribution de rôle.

> [!NOTE]
> Attribution du rôle de lecteur est nécessaire uniquement pour les utilisateurs qui doivent accéder aux objets BLOB ou les files d’attente à l’aide du portail Azure. 

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur RBAC, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle (RBAC) ?](../../role-based-access-control/overview.md).
- Pour apprendre à attribuer et gérer les rôles RBAC avec Azure PowerShell, Azure CLI ou l’API REST, consultez les articles suivants :
    - [Gérer le contrôle d’accès en fonction du rôle (RBAC) avec Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Gérer le contrôle d’accès en fonction du rôle (RBAC) avec Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Gérer le contrôle d’accès en fonction du rôle (RBAC) avec l’API REST](../../role-based-access-control/role-assignments-rest.md)
- Pour apprendre à autoriser l’accès aux conteneurs et files d’attente à partir de vos applications de stockage, consultez [Utiliser Azure AD avec les applications de stockage Azure](storage-auth-aad-app.md).
