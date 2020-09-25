---
title: Ajouter des rôles d’application et les obtenir à partir d’un jeton | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment ajouter des rôles d’application à une application inscrite dans Azure Active Directory, comment affecter des utilisateurs et des groupes à ces rôles et les recevoir dans la revendication Rôles au sein du jeton.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 02fe3c1ebc893dea259abcda3ea1d13ab96d68d5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706011"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Procédure : Ajouter des rôles d’application dans votre application et les recevoir dans le jeton

Le contrôle d’accès en fonction du rôle (RBAC) est un mécanisme populaire pour appliquer l’autorisation dans des applications. En utilisant RBAC, un administrateur accorde des autorisations aux rôles, pas à des utilisateurs individuels ou à des groupes. L’administrateur peut alors attribuer des rôles aux différents utilisateurs et groupes pour contrôler l’accès au contenu et aux fonctionnalités.

En utilisant le RBAC avec des rôles d’application et des revendications de rôle, les développeurs peuvent mettre en place des autorisations en toute sécurité dans leurs applications sans beaucoup d’efforts.

Une autre approche consiste à utiliser des groupes Azure AD et des revendications de groupe, comme indiqué dans l’exemple de code [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet) sur GitHub. Les groupes Azure AD et les rôles d’application ne sont pas mutuellement exclusifs et peuvent être utilisés conjointement pour offrir un contrôle d’accès encore plus précis.

## <a name="declare-roles-for-an-application"></a>Déclarer des rôles pour une application

Les rôles d’application sont définis dans le [portail Azure](https://portal.azure.com).  Quand un utilisateur se connecte à l’application, Azure AD émet une revendication `roles` pour chaque rôle qui lui a été accordé individuellement ou de par son appartenance à un groupe.  L’attribution des utilisateurs et des groupes aux rôles peut être effectuée via l’interface utilisateur du portail ou par programmation à l’aide de [Microsoft Graph](/graph/azuread-identity-access-management-concept-overview).

Pour créer un rôle d’application :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **répertoire + abonnement** dans le menu supérieur, puis choisissez l’abonné Azure Active Directory qui contient l’inscription de l’application à laquelle vous souhaitez ajouter un rôle d’application.
1. Recherchez et sélectionnez **Azure Active Directory**.
1. Sous **Gérer**, sélectionnez **Inscriptions d’applications**, puis sélectionnez l’application dans laquelle vous souhaitez définir des rôles d’application.
1. Sélectionnez **Rôles d’application | Aperçu**, puis **Créer un rôle d’application**.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Volet Rôles d’application d’une inscription d’application dans le portail Azure":::
1. Dans le volet **Créer un rôle d’application** , entrez les paramètres du rôle. Le tableau qui suit l’image décrit chaque réglage et leurs paramètres.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Volet contextuel de création de rôles d’application d’une inscription d’application dans le portail Azure":::

    | Champ | Description | Exemple |
    |-------|-------------|---------|
    | **Nom complet** | Nom d'affichage du rôle d’application qui apparaît lors du consentement de l'administrateur et de l'affectation de l'application. Cette valeur peut contenir des espaces.  | `Survey Writer` |
    | **Types de membres autorisés** | Spécifie si ce rôle d’application peut être attribué aux utilisateurs, aux applications ou aux deux. | `Users/Groups` |
    | **Valeur** | Spécifie la valeur de la revendication des rôles que l'application doit attendre dans le jeton. La valeur doit correspondre exactement à la chaîne référencée dans le code de l’application. La valeur ne peut pas contenir d’espaces. | `Survey.Create` |
    | **Description** | Description plus détaillée du rôle d’application affiché pendant les expériences d’affectation et de consentement des applications d’administration. | `Writers can create surveys.` |
    | **Voulez-vous activer ce rôle d'application ?** | Indique si le rôle d’application est activé. Pour supprimer un rôle d’application, décochez cette case et appliquez la modification avant de tenter l’opération de suppression. | *Activée* |

1. Sélectionnez **Appliquer** pour enregistrer vos modifications.

> [!NOTE]
> Le nombre de rôles que vous ajoutez est compté pour les limites définies pour un manifeste d’application. Pour plus d’informations sur ces limites, consultez la section [Limites du manifeste](./reference-app-manifest.md#manifest-limits) de [Référence du manifeste d’application Azure Active Directory](reference-app-manifest.md).

## <a name="assign-users-and-groups-to-roles"></a>Affecter des utilisateurs et des groupes à des rôles

Une fois que vous avez ajouté des rôles d’application à votre application, vous pouvez leur affecter des utilisateurs et des groupes.

1. Sélectionnez **Azure Active Directory** dans le portail Azure, sélectionnez  **Applications d’entreprise** dans le menu de navigation de gauche.
1. Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

     Si votre application n’apparaît pas dans la liste, utilisez les filtres présents en haut de la liste **Toutes les applications** pour restreindre la liste ou bien faites-la défiler vers le bas pour localiser votre application.

1. Sélectionnez l’application dans laquelle vous souhaitez assigner des utilisateurs ou un groupe de sécurité aux rôles.
1. Sous **Gérer**, sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un membre** pour ouvrir le volet **Ajouter une attribution**.
1. Sélectionnez le sélecteur **Utilisateurs et groupes** à partir du volet **Ajouter une attribution**.

     Une liste d’utilisateurs et de groupes de sécurité s’affiche. Vous pouvez rechercher un utilisateur ou un groupe et sélectionner plusieurs utilisateurs et groupes qui s’affichent dans la liste.

1. Une fois que vous avez sélectionné des utilisateurs et des groupes, sélectionnez le bouton **Sélectionner** pour continuer.
1. Sélectionnez **Sélectionner un rôle** dans le volet **Ajouter une attribution**. Tous les rôles que vous avez définis pour l’application s’affichent.
1. Choisissez un rôle et sélectionnez **Sélectionner**.
1. Appuyez sur le bouton **Attribuer** pour terminer l’attribution des utilisateurs et des groupes à l’application.
1. Vérifiez que les utilisateurs et les groupes ajoutés figurent dans la liste **Utilisateurs et groupes**.

## <a name="receive-roles-in-tokens"></a>Recevoir des rôles dans des jetons

Lorsque les utilisateurs assignés aux différents rôles d’application se connectent à l’application, leurs jetons portent les rôles qui leur ont été attribués dans la revendication `roles`.

## <a name="web-api-application-permissions"></a>Autorisations de l'application API Web

Vous pouvez définir des rôles d’application qui ciblent **Utilisateurs et groupes**, **Applications**ou **les deux**. Lorsque vous sélectionnez **Applications** ou **Les deux**, le rôle d’application apparaît comme autorisation d’application dans les **autorisations de l’API** d’une application cliente.

Pour sélectionner l’autorisation d’une application dans une inscription d’application cliente après avoir défini un rôle ciblant **Applications** ou **Les deux** :

1. Dans **Azure Active Directory** du portail Azure, sélectionnez **Inscriptions d’applications**, puis l’inscription de l’application cliente.
1. Sous **Gérer**, sélectionnez **Autorisations de l’API**.
1. Sélectionnez **Ajouter une autorisation** > **Mes API**.
1. Sélectionnez l’application à laquelle vous avez ajouté le rôle d’application, puis sélectionnez **Autorisations de l’application**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les rôles des applications, consultez les ressources suivantes :

- Exemple de code : [Ajouter une autorisation à une application Web ASP.NET Core à l'aide de rôles d'application et de revendications de rôles](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles) (GitHub)
- Vidéo : [Implémenter l’autorisation dans vos applications avec la plateforme d’identité Microsoft (vidéo)](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
- [Manifeste d’application Azure Active Directory](./reference-app-manifest.md)
- [Jetons d’accès Azure AD](access-tokens.md)
- [Azure AD `id_tokens`](id-tokens.md)
