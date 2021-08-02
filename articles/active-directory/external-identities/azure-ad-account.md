---
title: Fournisseur d’identité d’un compte Azure AD
description: Utilisez Azure Active Directory pour permettre à un utilisateur externe (invité) de se connecter à vos applications Azure AD à l’aide de son compte professionnel Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d9d7a8e74767982d5089a1308ca36108eb5f106
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111972525"
---
# <a name="azure-active-directory-azure-ad-identity-provider-for-external-identities"></a>Fournisseur d’identité Azure Active Directory (Azure AD) pour External Identities

Azure Active Directory est disponible en tant qu’option de fournisseur d’identité pour B2B Collaboration par défaut. Si un utilisateur invité externe dispose d’un compte Azure AD par le biais d’un compte professionnel ou scolaire, il peut accepter vos invitations B2B Collaboration ou compléter vos flux d’utilisateurs d’inscription à l’aide de son compte Azure AD.

## <a name="guest-sign-in-using-azure-active-directory-accounts"></a>Connexion d’invité à l’aide de comptes Azure Active Directory

Azure Active Directory est disponible dans la liste des fournisseurs d’identité External Identitites par défaut. Aucune autre configuration n’est requise pour permettre aux utilisateurs invités de se connecter avec leur compte Azure AD en utilisant le flux d’invitation ou un flux d’utilisateur d’inscription en libre-service.

![Compte Azure AD dans la liste des fournisseurs d’identité](media/azure-ad-account/azure-ad-account-identity-provider.png)

### <a name="azure-ad-account-in-the-invitation-flow"></a>Compte Azure AD dans le flux d’invitation

Lorsque vous [invitez un utilisateur invité](add-users-administrator.md) dans B2B Collaboration, vous pouvez spécifier son compte Azure AD en tant qu’adresse e-mail qu’il utilisera pour se connecter.

![Inviter à l’aide d’un compte Azure AD](media/azure-ad-account/azure-ad-account-invite.png)

### <a name="azure-ad-account-in-self-service-sign-up-user-flows"></a>Compte Azure AD dans les flux d’utilisateurs d’inscription en libre-service

Le compte Azure AD est une option de fournisseur d’identité pour vos flux d’utilisateurs d’inscription en libre-service. Les utilisateurs peuvent s’inscrire à vos applications à l’aide de leurs propres comptes Azure AD. Dans un premier temps, vous devez [activer l’inscription en libre-service](self-service-sign-up-user-flow.md) pour votre locataire. Puis, vous devez configurer un flux d’utilisateurs pour l’application et sélectionner Azure Active Directory parmi les options de connexion.

![Compte Azure AD dans un flux d’utilisateurs d’inscription en libre-service](media/azure-ad-account/azure-ad-account-user-flow.png)

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter des utilisateurs de collaboration B2B Azure Active Directory](add-users-administrator.md)
- [Ajouter l’inscription en libre-service à une application](self-service-sign-up-user-flow.md)