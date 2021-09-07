---
title: Fournisseur d’identité d’un compte Azure AD
description: Utilisez Azure Active Directory pour permettre à un utilisateur externe (invité) de se connecter à vos applications Azure AD à l’aide de son compte professionnel Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/09/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00edf144e06710204bf5d6eb477187668b3e5237
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525789"
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

## <a name="verifying-the-applications-publisher-domain"></a>Vérification du domaine de l’éditeur de l’application
Depuis novembre 2020, les inscriptions de nouvelles applications s’affichent comme étant non vérifiées dans l’invite de consentement de l’utilisateur, sauf si [le domaine de l’éditeur de l’application a été vérifié](../develop/howto-configure-publisher-domain.md) ***et*** si l’identité de l’entreprise a été vérifiée auprès de Microsoft Partner Network puis associée à l’application. ([En savoir plus sur ce changement](../develop/publisher-verification-overview.md)) Notez que pour les flux d’utilisateurs Azure AD, le domaine de l’éditeur s’affiche uniquement lorsque vous utilisez un [compte Microsoft](microsoft-account.md) ou un autre locataire Azure AD comme fournisseur d’identité. Pour répondre à ces nouvelles conditions, effectuez les étapes suivantes :

1. [Vérifiez l’identité de votre entreprise avec votre compte Microsoft Partner Network (MPN)](/partner-center/verification-responses). Ce processus vérifie les informations relatives à votre société et au contact principal de votre entreprise.
1. Effectuez le processus de vérification de l’éditeur pour associer votre compte MPN à votre inscription d’application à l’aide de l’une des options suivantes :
   - Si l’inscription d’application pour le fournisseur d’identité du compte Microsoft se trouve dans un locataire Azure AD, [vérifiez votre application dans le portail d’inscription des applications](../develop/mark-app-as-publisher-verified.md).
   - Si l’inscription de votre application pour le fournisseur d’identité du compte Microsoft se trouve dans un locataire Azure AD B2C, [marquez votre application comme étant validée par l’éditeur à l’aide des API Microsoft Graph](../develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) (par exemple, à l’aide de l’Afficheur Graph).

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter des utilisateurs de collaboration B2B Azure Active Directory](add-users-administrator.md)
- [Ajouter l’inscription en libre-service à une application](self-service-sign-up-user-flow.md)