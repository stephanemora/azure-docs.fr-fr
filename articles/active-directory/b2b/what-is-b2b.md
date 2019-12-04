---
title: Qu’est-ce que B2B Collaboration dans Azure Active Directory ?
description: Azure AD B2B Collaboration prend en charge l’accès utilisateur invité, qui vous permet de partager des ressources et de collaborer avec des partenaires externes de manière sécurisée.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 09/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18d60614a07895c7e9468d35b2d389a28129af4d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74271664"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Qu’est-ce que l’accès utilisateur invité dans Azure Active Directory B2B ?

Azure Active Directory (Azure AD) B2B Collaboration vous permet de partager de manière sécurisée les applications et les services de votre entreprise avec des utilisateurs invités et des partenaires externes issus de n’importe quelle organisation, tout en conservant le contrôle sur vos propres données d’entreprise. Collaborez de manière sécurisée avec des partenaires externes issus de petites ou grandes entreprises, même s’ils n’utilisent pas Azure AD ou n’ont pas de service informatique. Un simple processus d’invitation et d’échange d’invitation permet à vos partenaires d’utiliser leurs propres informations d’identification pour accéder aux ressources de votre société. Les développeurs peuvent utiliser les API B2B d’Azure AD pour personnaliser le processus d’invitation ou pour écrire des applications telles que des portails d’inscription libre-service.

Regardez la vidéo pour savoir comment collaborer de manière sécurisée avec les utilisateurs invités, en les invitant à se connecter aux applications et aux services de votre entreprise avec leur propre identité.

La vidéo suivante fournit une vue d’ensemble utile.

>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="collaborate-with-any-partner-using-their-identities"></a>Collaborer avec tous les partenaires qui utilisent leur propre identité
Avec Azure AD B2B, vos partenaires utilisent leur propre solution de gestion des identités. Votre organisation n’a donc aucuns frais administratifs externes à payer. 
- Les partenaires utilisent leurs propres identités et informations d’identification. Azure AD n’est donc pas nécessaire. 
- Vous n’avez pas à gérer les comptes externes ni les mots de passe. 
- Vous n’avez pas à synchroniser les comptes ni à gérer les cycles de vie des comptes.  

![Capture d’écran montrant la page Ajouter des membres](media/what-is-b2b/add-member.png)

## <a name="invite-guest-users-with-a-simple-invitation-and-redemption-process"></a>Inviter des utilisateurs invités avec un simple processus d’invitation et d’échange d’invitation
Les utilisateurs invités peuvent se connecter à vos applications et services avec leurs propres identités professionnelles, scolaires ou sociales. Si l’utilisateur invité n’a pas de compte Microsoft ou de compte Azure AD, un compte est créé pour lui au moment où il active l’invitation. 
- Invitez les utilisateurs avec l’identité e-mail de leur choix.
- Envoyez directement un lien vers une application, ou envoyez une invitation au panneau d’accès de l’utilisateur invité. 
- Les utilisateurs invités suivent quelques étapes simples d’échange d’invitation pour se connecter.

![Capture d’écran montrant la page Passer en revue les autorisations](media/what-is-b2b/consentscreen.png)

## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Utiliser des stratégies pour partager vos applications et vos services de manière sécurisée
Vous pouvez utiliser des stratégies d’autorisation pour protéger le contenu de votre entreprise. Les stratégies d’accès conditionnel, telles que l’authentification multifacteur, peuvent être appliquées :
- Au niveau du locataire
- Au niveau de l’application
- Pour certains utilisateurs invités afin de protéger les données et les applications d’entreprise

![Capture d’écran montrant l’option Accès conditionnel](media/what-is-b2b/tutorial-mfa-policy-2.png)


## <a name="easily-add-guest-users-in-the-azure-ad-portal"></a>Ajouter facilement des utilisateurs invités dans le portail Azure AD

En tant qu’administrateur, vous pouvez facilement ajouter des utilisateurs invités à votre organisation dans le portail Azure.
- Créez un utilisateur invité dans Azure AD, comme vous le feriez pour ajouter tout autre utilisateur.
- L’utilisateur invité reçoit immédiatement une invitation personnalisable qui lui permet de se connecter à son panneau d’accès.
- Les utilisateurs invités de l’annuaire peuvent être affectés à des applications ou à des groupes.  

![Capture d’écran montrant la page d’entrée de l’invitation Nouvel utilisateur invité](media/what-is-b2b/adding-b2b-users-admin.png)

## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Déléguer à chaque propriétaire d’application ou de groupe la gestion de ses propres utilisateurs invités

Vous pouvez déléguer la gestion des utilisateurs invités aux propriétaires d’applications, afin qu’ils puissent les ajouter directement aux applications qu’ils souhaitent partager, qu’il s’agisse ou non d’une application Microsoft. 
 - Les administrateurs configurent la gestion des applications et des groupes libre-service.
 - Les utilisateurs qui ne sont pas administrateurs utilisent leur [panneau d’accès](https://myapps.microsoft.com) pour ajouter des utilisateurs invités aux applications ou aux groupes.

![Capture d’écran montrant le panneau d’accès pour un utilisateur invité](media/what-is-b2b/access-panel-manage-app.png)

## <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Utilisez les API et l’exemple de code pour créer facilement des applications à intégrer

Intégrez vos partenaires externes de façon personnalisée en fonction des besoins de votre organisation.
- Utilisez les [API d’invitation B2B Collaboration](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) pour personnaliser votre expérience d’intégration, notamment pour créer des portails d’inscription libre-service. 
- Utilisez l’exemple de code pour un portail libre-service fourni [sur GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![Capture d’écran montrant l’exemple de portail d’inscription](media/what-is-b2b/sign-up-portal.png)

## <a name="next-steps"></a>Étapes suivantes

- [Guide sur l’attribution de licences Azure AD B2B Collaboration](licensing-guidance.md)
- [Ajouter des utilisateurs invités B2B Collaboration dans le portail](add-users-administrator.md)
- [Comprendre le processus d’échange d’invitation](redemption-experience.md)
- Et, comme toujours, contactez l’équipe produit pour vos commentaires, discussions et suggestions par le biais de notre [Communauté technologique Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
