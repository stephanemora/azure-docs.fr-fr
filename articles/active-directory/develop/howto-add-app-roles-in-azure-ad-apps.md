---
title: Ajouter des rôles d’application et les obtenir à partir d’un jeton | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment ajouter des rôles d’application dans une application inscrite dans Azure Active Directory, comment affecter des utilisateurs et des groupes à ces rôles et les recevoir dans la revendication `roles` au sein du jeton.
services: active-directory
author: kkrishna
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: aedf5d710b82185cb634fcd92e6981a2c358ad52
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85477887"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Procédure : Ajouter des rôles d’application dans votre application et les recevoir dans le jeton

Le contrôle d’accès en fonction du rôle (RBAC) est un mécanisme populaire pour appliquer l’autorisation dans des applications. En utilisant RBAC, un administrateur accorde des autorisations aux rôles, pas à des utilisateurs individuels ou à des groupes. L’administrateur peut alors attribuer des rôles aux différents utilisateurs et groupes pour contrôler l’accès au contenu et aux fonctionnalités.

En utilisant le RBAC avec des rôles d’application et des revendications de rôle, les développeurs mettre en place des autorisations en toute sécurité dans leurs applications sans beaucoup d’efforts.

Une autre approche consiste à utiliser des groupes Azure AD et des revendications de groupe, comme indiqué dans [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). Les groupes Azure AD et les rôles d’application ne sont pas mutuellement exclusifs et peuvent être utilisés conjointement pour offrir un contrôle d’accès encore plus précis.

## <a name="declare-roles-for-an-application"></a>Déclarer des rôles pour une application

Ces rôles d’application sont définis dans le [portail Azure](https://portal.azure.com) au sein du manifeste de l’inscription d’application.  Quand un utilisateur se connecte à l’application, Azure AD émet une revendication `roles` pour chaque rôle qui lui a été accordé individuellement ou de par son appartenance à un groupe.  L’attribution des utilisateurs et des groupes aux rôles peut être effectuée via l’interface utilisateur du portail ou par programmation à l’aide de [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Déclarer des rôles d’application à l’aide du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Répertoire + abonnement** dans la barre d’outils du portail.
1. Dans la liste **Favoris** ou **Tous les répertoires**, choisissez le locataire Active Directory dans lequel vous souhaitez inscrire votre application.
1. Dans le Portail Azure, recherchez et sélectionnez **Azure Active Directory**.
1. Dans le volet **Azure Active Directory**, sélectionnez **Inscriptions d'applications** pour afficher une liste de toutes vos applications.
1. Sélectionnez l’application pour laquelle vous souhaitez définir des rôles d’application. Sélectionnez alors **Manifeste**.
1. Modifiez le manifeste d’application en recherchant le paramètre `appRoles` et en ajoutant tous vos rôles d'application.

     > [!NOTE]
     > Chaque définition de rôle d’application dans ce manifeste doit avoir un GUID valide différent dans le contexte du manifeste pour la propriété `id`.
     >
     > La propriété `value` de chaque définition de rôle d’application doit correspondre exactement aux chaînes qui sont utilisées dans le code de l’application. La propriété `value` ne peut pas contenir d’espaces. Si tel est le cas, vous recevrez une erreur lors de l’enregistrement du manifeste.

1. Enregistrez le manifeste.

### <a name="examples"></a>Exemples

L’exemple suivant montre le `appRoles` que vous pouvez assigner à `users`.

> [!NOTE]
>Le `id` doit être un GUID unique.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-98f8-45fd-aa4a-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

> [!NOTE]
>Le `displayName` ne peut pas contenir d’espaces.

Vous pouvez définir des rôles d’application pour cibler `users`, `applications`, ou les deux. Lorsqu’ils sont disponibles pour `applications`, les rôles d’application apparaissent sous la forme d’autorisations d’application dans le panneau **Autorisations requises**. L’exemple suivant montre un rôle d’application ciblé sur un `Application`.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

Le nombre de rôles définis affecte les limites du manifeste d’application. Ils ont été présentés en détail sur la page [Limites du manifeste](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits).

### <a name="assign-users-and-groups-to-roles"></a>Affecter des utilisateurs et des groupes à des rôles

Une fois que vous avez ajouté des rôles d’application dans votre application, vous pouvez leur affecter des utilisateurs et des groupes.

1. Dans le volet **Azure Active Directory**, sélectionnez **Applications d’entreprise** dans le menu de navigation de gauche d’**Azure Active Directory**.
1. Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

     Si vous ne voyez pas l’application que vous souhaitez afficher ici, utilisez les filtres présents en haut de la liste **Toutes les applications** pour restreindre la liste ou bien faites-la défiler vers le bas pour localiser votre application.

1. Sélectionnez l’application dans laquelle vous souhaitez assigner des utilisateurs ou un groupe de sécurité aux rôles.
1. Sélectionnez le volet **Utilisateurs et groupes** dans le menu de navigation de gauche de l'application.
1. En haut de la liste **Utilisateurs et groupes**, sélectionnez le bouton **Ajouter un utilisateur** en haut de la liste pour ouvrir le volet **Ajouter une attribution**.
1. Sélectionnez le sélecteur **Utilisateurs et groupes** à partir du volet **Ajouter une attribution**.

     Une liste des utilisateurs et des groupes de sécurité s’affiche, ainsi qu’une zone de texte pour rechercher et localiser un utilisateur ou un groupe spécifique. Cet écran vous permet de sélectionner plusieurs utilisateurs et groupes d’un coup.

1. Une fois que vous avez sélectionné les utilisateurs et groupes, appuyez sur le bouton **Sélectionner** en bas pour passer à l’étape suivante.
1. Choisissez le sélecteur **Sélectionner un rôle** à partir du volet **Ajouter une attribution**. Tous les rôles déclarés plus tôt dans le manifeste d’application s’affichent.
1. Choisissez un rôle et appuyez sur le bouton **Sélectionner**.
1. Appuyez sur le bouton **Attribuer** en bas pour terminer l’attribution des utilisateurs et des groupes à l’application.
1. Vérifiez que les utilisateurs et les groupes ajoutés figurent dans la liste **Utilisateurs et groupes** mise à jour.

## <a name="more-information"></a>Informations complémentaires

- [Ajouter une autorisation à une application web ASP.NET Core à l'aide de rôles d'application et de revendications de rôles](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Utilisation de groupes de sécurité et de rôles d’Application dans vos applications (vidéo)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, désormais avec les revendications de groupe et les rôles d’application](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Manifeste d’application Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [Jetons d’accès AAD](access-tokens.md)
- [AAD `id_tokens`](id-tokens.md)
