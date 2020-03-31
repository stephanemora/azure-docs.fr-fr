---
title: Conditions préalables à l'utilisation d'API | Place de marché Azure
description: Conditions préalables à l'utilisation d'API du portail Cloud Partner.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 2a1022c6d041bf645b43dfed391a489de30b2fce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288561"
---
<a name="api-prerequisites"></a>Conditions préalables à l'utilisation d'API
================

Deux ressources de programmation sont requises pour utiliser les API du portail Cloud Partner : un principal de service et un jeton d’accès Azure Active Directory (Azure AD).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Créer un principal de service dans votre locataire Azure Active Directory
----------------------------------------------------------------

Tout d'abord, vous devez créer un principal de service dans votre locataire Azure AD. Ce locataire se verra attribuer son propre jeu d'autorisations sur le portail Cloud Partner. Votre code appellera les API en utilisant ce locataire plutôt que vos informations d'identification personnelles.  Pour une explication complète sur la création d'un principal de service, consultez [Utiliser le portail pour créer une application et un principal de service Azure Active Directory pouvant accéder aux ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).


<a name="add-the-service-principal-to-your-account"></a>Ajouter le principal de service à votre compte
-----------------------------------------

Maintenant que vous avez créé le principal de service dans votre locataire, vous pouvez l'ajouter en tant qu'utilisateur à votre compte sur le portail Cloud Partner. Comme un utilisateur, sur le portail, le principal de service peut être un propriétaire ou un contributeur.

Suivez les étapes ci-dessous pour ajouter le principal de service :

1. Connectez-vous au portail Cloud Partner. 
2. Cliquez sur l'option **Utilisateurs** de la barre de menus de gauche, puis sélectionnez **Ajouter un utilisateur**.

   ![Ajouter un utilisateur au portail](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. Dans la liste déroulante **Type**, sélectionnez **Principal du service** et ajoutez les informations suivantes :

-   Un **Nom convivial** pour le principal de service, par exemple `spAccount`.
-   L'**ID de l'application**. Pour trouver cet identificateur, accédez au [portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**, sélectionnez **Inscriptions d'applications**, puis cliquez sur votre application.
-   L'**ID de locataire** (ou **ID Directory**) de votre locataire Azure AD. Vous trouverez cet identificateur sur la page Azure Active Directory du [portail Azure](https://portal.azure.com), sous **Propriétés**.
-   L'**ID d’objet** de votre objet principal de service. Vous pouvez vous procurer cet identificateur à partir du portail Azure. Accédez à **Azure Active Directory**, choisissez **Inscriptions d'applications**, sélectionnez votre application et cliquez sur le nom de l’application sous **Application managée dans l'annuaire local**. Accédez ensuite à la page **Propriétés** pour rechercher l’ID d’objet. Veillez à récupérer l’ID d’objet de l’application managée, et non l’ID d’objet initial qui se trouve sur votre application.
-   Le **Rôle** associé au compte, qui sera utilisé pour RBAC.

     ![Ajouter une application managée au portail](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Cliquez sur **Ajouter** pour ajouter le principal de service à votre compte.

   ![Ajouter un principal de service](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Obtenir un jeton d’accès Azure AD
----------------------------

Les API du portail Cloud Partner utilisent les ressources et protocoles suivants lors de l’authentification :

- Un jeton de porteur JSON Web Token (JWT) pour demander l’accès aux ressources
- Le protocole [OpenID Connect](https://openid.net/connect/) (OIDC) pour vérifier l’identité
- [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) comme autorité d’identité

Il existe deux grandes approches pour acquérir un jeton JWT par programmation :

- Utiliser Microsoft Authentication Library pour .NET ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Cette approche de niveau supérieur est recommandée aux développeurs .NET. 
- Adresser une requête **HTTP POST** au point de terminaison du **jeton** OAuth Azure AD, sous la forme suivante :

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Vous pouvez ensuite transmettre ce jeton dans l’en-tête d’autorisation pour les demandes d’API.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Pour toutes les API de cette référence, il est toujours considéré que l’en-tête d’autorisation est transmis, ce qui explique pourquoi cela n'est pas explicitement mentionné.

Si vous rencontrez des erreurs d’authentification dans votre requête, consultez [Résoudre les erreurs d’authentification](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
