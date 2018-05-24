---
title: Qu’est-ce qu’Azure Active Directory B2B Collaboration ? | Microsoft Docs
description: Azure Active Directory B2B Collaboration prend en charge les relations interentreprises en permettant aux partenaires commerciaux d’accéder de façon sélective à vos applications d’entreprise.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 1eff04cf43260a12a50a08b9145e1975b51af0cc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>Qu’est-ce qu’Azure AD B2B Collaboration ?

Les fonctionnalités d’Azure Active Directory (Azure AD) business-to-business (B2B) Collaboration permettent à n’importe quelle organisation utilisant Azure AD de travailler en toute sécurité avec des utilisateurs de n’importe quelle autre organisation, petite ou grande. Ces organisations peuvent avoir Azure AD ou non et ne doivent pas nécessairement disposer d’un service informatique.

Les organisations utilisant Azure AD peuvent donner accès aux documents, ressources et applications à leurs partenaires, tout en conservant un contrôle complet sur leurs propres données d’entreprise. Les développeurs peuvent utiliser les API Azure AD business-to-business pour écrire des applications qui rapprochent deux organisations de manière sécurisée. En outre, la navigation est facile pour les utilisateurs finaux.

La vidéo suivante fournit une vue d’ensemble utile.
>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="key-benefits-of-azure-ad-b2b-collaboration"></a>Principaux avantages d’Azure AD B2B Collaboration

### <a name="work-with-any-user-from-any-partner"></a>Travaillez avec n’importe quel utilisateur de n’importe quel partenaire

- Les partenaires utilisent leurs propres informations d’identification
- Aucune exigence pour les partenaires d’utiliser Azure AD
- Aucun répertoire externe ou configuration complexe requis

### <a name="simple-and-secure-collaboration"></a>Collaboration simple et sécurisée

- Permet l’accès à toutes données ou applications d’entreprise avec l’application de stratégies d’autorisation puissantes d’Azure AD
- Simplicité pour les utilisateurs
- Sécurité d’entreprise pour les applications et les données

### <a name="no-management-overhead"></a>Aucune surcharge de gestion

- Aucune gestion de compte ou mot de passe externe
- Aucune gestion manuelle des synchronisations ou du cycle de vie des comptes
- Aucune surcharge administrative externe

## <a name="easily-add-b2b-collaboration-users"></a>Ajouter facilement des utilisateurs de collaboration B2B

En tant qu’administrateur, vous pouvez facilement ajouter des utilisateurs de collaboration (invités) B2B à votre organisation dans le [portail Azure](https://portal.azure.com).

![ajouter des utilisateurs invités](media/active-directory-b2b-what-is-azure-ad-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>Permettez à vos collaborateurs d’utiliser leur propre identité

Les collaborateurs B2B peuvent se connecter avec l’identité de leur choix. Si l’utilisateur n’a pas de compte Microsoft ou Azure AD, un compte est créé pour lui en toute transparence au moment de l’utilisation de l’offre.

### <a name="delegate-to-application-and-group-owners"></a>Déléguez aux propriétaires d’applications et de groupes

En tant que propriétaire de l’application ou du groupe, vous pouvez ajouter des utilisateurs B2B directement à n’importe quelle application qui vous intéresse, qu’il s’agisse d’une application Microsoft ou non. Les administrateurs peuvent déléguer l’autorisation d’ajouter des utilisateurs B2B aux utilisateurs non administrateurs. Les utilisateurs non administrateurs peuvent utiliser le [volet d’accès aux applications Azure AD](https://myapps.microsoft.com) pour ajouter des utilisateurs B2B Collaboration aux applications ou aux groupes.

![volet d’accès](media/active-directory-b2b-what-is-azure-ad-b2b/access-panel.png)

![ajout d’un membre](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>Les stratégies d’autorisation protègent votre contenu d’entreprise

Les stratégies d’accès conditionnel, telles que l’authentification multifacteur, peuvent être appliquées :
- Au niveau du locataire
- Au niveau de l’application
- Pour des utilisateurs spécifiques afin de protéger les données et les applications d’entreprise

### <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Utilisez les API et l’exemple de code pour créer facilement des applications à intégrer

Intégrez vos partenaires externes de façon personnalisée en fonction des besoins de votre organisation.

Utilisez les [API d’invitation B2B Collaboration](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) pour personnaliser votre expérience d’intégration, notamment pour créer des portails d’inscription libre-service. Nous fournissons un exemple de code pour un portail libre-service [sur Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![portail d’inscription](media/active-directory-b2b-what-is-azure-ad-b2b/sign-up-portal.png)

Avec Azure AD B2B Collaboration, vous pouvez tirer parti de la puissance d’Azure AD pour protéger vos relations avec vos partenaires de façon simple et intuitive pour vos utilisateurs finaux.

## <a name="next-steps"></a>Étapes suivantes

- [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-admin-add-users.md)
- [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-iw-add-users.md)
- [Utilisation d’une invitation B2B Collaboration](active-directory-b2b-redemption-experience.md)
- [Attribution de licences Azure AD B2B Collaboration](active-directory-b2b-licensing.md)
- Et, comme toujours, contactez l’équipe produit pour vos commentaires, discussions et suggestions par le biais de notre [Communauté technologique Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).