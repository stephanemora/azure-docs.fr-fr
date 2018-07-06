---
title: Utiliser RBAC pour gérer les droits d’accès aux conteneurs et files d’attente du stockage Azure (préversion) | Microsoft Docs
description: Utilisez RBA (contrôle d’accès en fonction du rôle) pour attribuer aux utilisateurs, aux groupes, aux principaux de service d’application ou aux identités Managed Service Identity des rôles qui leur permettent d’accéder aux données du Stockage Azure. Le Stockage Azure prend en charge les rôles intégrés et personnalisés pour les droits d’accès aux conteneurs et aux files d’attente.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/29/2018
ms.author: tamram
ms.openlocfilehash: 241808e0a7bde1d2c53cd0af1de677275c169214
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082209"
---
# <a name="manage-access-rights-to-azure-storage-data-with-rbac-preview"></a>Gérer les droits d’accès aux données du Stockage Azure avec RBAC (préversion)

Azure Active Directory (Azure AD) autorise les droits d’accès aux ressources sécurisées via [RBAC (contrôle d’accès en fonction du rôle)](https://docs.microsoft.com/azure/role-based-access-control/overview). Le Stockage Azure définit un ensemble de rôles RBAC intégrés qui englobent les ensembles communs d’autorisations permettant d’accéder aux conteneurs ou aux files d’attente. Quand un rôle RBAC est attribué à une identité Azure AD, celle-ci est autorisée à accéder aux ressources correspondantes, en fonction de l’étendue spécifiée. L’accès peut être limité au niveau de l’abonnement, du groupe de ressources, du compte de stockage ou d’un conteneur ou d’une file d’attente individuelle. Vous pouvez attribuer des droits d’accès aux ressources de stockage Azure à l’aide du Portail Azure, des outils en ligne de commande Azure et des API de gestion Azure. 

Une identité Azure AD peut correspondre à un utilisateur, un groupe ou un principal du service d’application, ou éventuellement à une *identité Managed Service Identity*. Un principal de sécurité peut correspondre à un utilisateur, un groupe ou un principal du service d’application. Une [identité Managed Service Identity](../../active-directory/managed-service-identity/overview.md) est une identité administrée automatiquement, qui permet de s’authentifier à partir d’applications s’exécutant dans Machines virtuelles Azure, des applications de fonction, des groupes de machines virtuelles identiques, etc. Pour une vue d’ensemble de l’identité dans Azure AD, consultez [Comprendre les solutions d’identité Azure](https://docs.microsoft.com/en-us/azure/active-directory/understand-azure-identity-solutions).

## <a name="rbac-roles-for-azure-storage"></a>Rôles RBAC pour le Stockage Azure

Le Stockage Azure prend en charge les rôles RBAC intégrés et personnalisés. Le Stockage Azure propose l’utilisation de ces rôles RBAC intégrés avec Azure AD :

- [Contributeur aux données blob du stockage (préversion)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview)
- [Lecteur des données blob du stockage (préversion)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)
- [Contributeur aux données en file d’attente du stockage (préversion)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)
- [Lecteur des données en file d’attente du stockage (préversion)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-queue-data-reader-preview)

Pour plus d’informations sur la définition des rôles intégrés pour le Stockage Azure, consultez [Comprendre les définitions de rôles](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#management-and-data-operations-preview).

Vous pouvez également définir des rôles personnalisés à utiliser avec des conteneurs et des files d’attente. Pour plus d’informations, consultez [Créer des rôles personnalisés pour le contrôle d’accès en fonction du rôle Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles.md). 

> [!IMPORTANT]
> Cette préversion est destinée uniquement à une utilisation hors production. Les contrats SLA (contrats de niveau de service) de production ne sont pas disponibles tant que l’intégration d’Azure AD pour le Stockage Azure n’est pas officiellement disponible de manière générale. Si l’intégration d’Azure AD n’est pas encore prise en charge pour votre scénario, continuez à utiliser l’autorisation basée sur une clé partagée ou les jetons SAP dans vos applications. Pour plus d’informations sur la préversion, consultez [Authentifier l’accès au Stockage Azure à l’aide d’Azure Active Directory (préversion)](storage-auth-aad.md).
>
> Durant la préversion, les attributions de rôles RBAC peuvent prendre jusqu’à cinq minutes pour se propager.

## <a name="assign-a-role-to-a-security-principal"></a>Attribuer un rôle à un principal de sécurité

Attribuez un rôle RBAC à une identité Azure pour accorder des autorisations aux conteneurs ou files d’attente de votre compte de stockage. Vous pouvez limiter l’attribution de rôle au compte de stockage, ou à un conteneur ou une file d’attente spécifique. Le tableau suivant récapitule les droits d’accès accordés par les rôles intégrés, en fonction de l’étendue : 

|                                 |     Contributeur aux données blob                                                 |     Lecteur de données blob                                                |     Contributeur aux données en file d’attente                                  |     Lecteur de données en file d’attente                                 |
|---------------------------------|------------------------------------------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------|----------------------------------------------------------|
|    Limité à l’abonnement       |    Accès en lecture/écriture à tous les conteneurs et objets blob de l’abonnement       |    Accès en lecture à tous les conteneurs et objets blob de l’abonnement       |    Accès en lecture/écriture à toutes les files d’attente de l’abonnement       |    Accès en lecture à toutes les files d’attente de l’abonnement         |
|    Limité au groupe de ressources     |    Accès en lecture/écriture à tous les conteneurs et objets blob du groupe de ressources     |    Accès en lecture à tous les conteneurs et objets blob du groupe de ressources     |    Accès en lecture/écriture à toutes les files d’attente du groupe de ressources     |    Accès en lecture à toutes les files d’attente du groupe de ressources     |
|    Limité au compte de stockage    |    Accès en lecture/écriture à tous les conteneurs et objets blob du compte de stockage    |    Accès en lecture à tous les conteneurs et objets blob du compte de stockage    |    Accès en lecture/écriture à toutes les files d’attente du compte de stockage    |    Accès en lecture à toutes les files d’attente du compte de stockage    |
|    Limité au conteneur/à la file d’attente    |    Accès en lecture/écriture au conteneur spécifié et à ses objets blob              |    Accès en lecture au conteneur spécifié et à ses objets blob              |    Accès en lecture/écriture à la file d’attente spécifiée                  |    Accès en lecture à la file d’attente spécifiée                    |

> [!NOTE]
> En tant que propriétaire de votre compte de stockage Azure, aucune autorisation d’accès aux données ne vous est automatiquement attribuée. Vous devez vous attribuer explicitement un rôle RBAC pour le Stockage Azure. Vous pouvez l’attribuer au niveau de votre abonnement, votre groupe de ressources ou votre compte de stockage, ou au niveau d’un conteneur ou d’une file d’attente.

Pour plus d’informations sur les autorisations nécessaires pour appeler des opérations relatives au Stockage Azure, consultez [Autorisations d’appel d’opérations REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

Les sections suivantes montrent comment attribuer un rôle limité au compte de stockage ou à un conteneur individuel.

### <a name="assign-a-role-scoped-to-the-storage-account-in-the-azure-portal"></a>Attribuer un rôle limité au compte de stockage dans le Portail Azure

Pour attribuer un rôle intégré permettant d’accéder à l’ensemble des conteneurs ou des files d’attente du compte de stockage dans le Portail Azure :

1. Dans le [Portail Azure](https://portal.azure.com), accédez à votre compte de stockage.
2. Sélectionnez votre compte de stockage, puis sélectionnez **Contrôle d’accès (IAM)** pour afficher les paramètres de contrôle d’accès du compte. Cliquez sur le bouton **Ajouter** pour ajouter un nouveau rôle.

    ![Capture d’écran montrant les paramètres de contrôle d’accès du stockage](media/storage-auth-aad-rbac/portal-access-control.png)

3. Dans la fenêtre **Ajouter des autorisations**, sélectionnez le rôle à attribuer à une identité Azure AD. Recherchez ensuite l’identité à laquelle vous souhaitez attribuer ce rôle. Par exemple, l’image suivante montre l’attribution du rôle **Lecteur des données blob du stockage (préversion)** à un utilisateur.

    ![Capture d’écran montrant comment attribuer un rôle RBAC](media/storage-auth-aad-rbac/add-rbac-role.png)

4. Cliquez sur **Enregistrer**. L’identité à laquelle vous avez attribué le rôle apparaît sous ce dernier. Par exemple, l’image suivante montre que les utilisateurs ajoutés ont désormais des autorisations d’accès en lecture pour toutes les données blob du compte de stockage.

    ![Capture d’écran montrant la liste des utilisateurs auxquels un rôle a été attribué](media/storage-auth-aad-rbac/account-scoped-role.png)

### <a name="assign-a-role-scoped-to-a-container-or-queue-in-the-azure-portal"></a>Attribuer un rôle limité à un conteneur ou une file d’attente dans le Portail Azure

Les étapes d’attribution d’un rôle intégré se limitant à un conteneur ou une file d’attente sont similaires. La procédure illustrée ici attribue un rôle limité à un conteneur, mais vous pouvez suivre les mêmes étapes pour attribuer un rôle limité à une file d’attente : 

1. Dans le [Portail Azure](https://portal.azure.com), accédez à votre compte de stockage, puis affichez la **Vue d’ensemble** du compte.
2. Sous Service BLOB, sélectionnez **Parcourir les objets blob**. 
3. Recherchez le conteneur pour lequel vous souhaitez attribuer un rôle, puis affichez les paramètres de ce conteneur. 
4. Sélectionnez **Contrôle d’accès (IAM)** pour afficher les paramètres de contrôle d’accès du conteneur.
5. Dans la fenêtre **Ajouter des autorisations**, sélectionnez le rôle à attribuer à une identité Azure AD. Recherchez ensuite l’identité à laquelle vous souhaitez attribuer ce rôle.
6. Cliquez sur **Enregistrer**. L’identité à laquelle vous avez attribué le rôle apparaît sous ce dernier. Par exemple, l’image suivante montre que l’utilisateur ajouté a désormais des autorisations d’accès en lecture pour toutes les données du conteneur nommé *sample-container*.

    ![Capture d’écran montrant la liste des utilisateurs auxquels un rôle a été attribué](media/storage-auth-aad-rbac/container-scoped-role.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur RBAC, consultez [Bien démarrer avec le contrôle d’accès en fonction du rôle](../../role-based-access-control/overview.md).
- Pour apprendre à attribuer et gérer les rôles RBAC avec Azure PowerShell, Azure CLI ou l’API REST, consultez les articles suivants :
    - [Gérer le contrôle d’accès en fonction du rôle (RBAC) avec Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Gérer le contrôle d’accès en fonction du rôle (RBAC) avec Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Gérer le contrôle d’accès en fonction du rôle (RBAC) avec l’API REST](../../role-based-access-control/role-assignments-rest.md)
- Pour apprendre à autoriser l’accès aux conteneurs et files d’attente à partir de vos applications de stockage, consultez [Utiliser Azure AD avec les applications de stockage Azure](storage-auth-aad-app.md).
- Pour plus d’informations sur l’intégration d’Azure AD aux conteneurs et files d’attente Azure, consultez le billet de blog de l’équipe Stockage Azure, [Annonce de la préversion d’Azure AD Authentication pour le Stockage Azure](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
- 