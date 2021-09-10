---
title: Fournisseur d’identité de compte Microsoft (MSA) dans Azure AD
description: Utilisez Azure AD pour permettre à un utilisateur externe (invité) de se connecter à vos applications Azure AD à l’aide de son compte Microsoft (MSA).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/09/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2333ba6aec10d124d960dfc746bc9df2c449f3bb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525636"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities"></a>Fournisseur d’identité de compte Microsoft (MSA) pour External Identities

Vos utilisateurs invités B2B peuvent utiliser leurs propres comptes Microsoft à des fins de collaboration B2B sans configuration supplémentaire. Les utilisateurs invités peuvent échanger vos invitations de collaboration B2B ou compléter vos flux d’utilisateurs d’inscription à l’aide de leur compte Microsoft personnel.

Les comptes Microsoft sont configurés par un utilisateur pour accéder aux produits et services cloud Microsoft orientés consommateurs, comme Outlook, OneDrive, Xbox LIVE ou Microsoft 365. Le compte est créé et stocké dans le système de comptes d’identité des consommateurs de Microsoft.

## <a name="guest-sign-in-using-microsoft-accounts"></a>Connexion d’invité à l’aide de comptes Microsoft

Le compte Microsoft est disponible dans la liste des fournisseurs d’identité External Identities par défaut. Aucune autre configuration n’est requise pour permettre aux utilisateurs invités de se connecter avec leur compte Microsoft en utilisant le flux d’invitation ou un flux d’utilisateur d’inscription en libre-service.

![Compte Microsoft dans la liste des fournisseurs d’identité](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>Compte Microsoft dans le flux d’invitation

Lorsque vous [invitez un utilisateur invité](add-users-administrator.md) à des fins de collaboration B2B, vous pouvez spécifier son compte Microsoft comme adresse e-mail qu’il utilisera pour se connecter.

![Inviter à l’aide d’un compte Microsoft](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>Compte Microsoft dans les flux d’utilisateur d’inscription en libre-service

Le compte Microsoft est une option de fournisseur d’identité pour vos flux d’utilisateur d’inscription en libre-service. Les utilisateurs peuvent s’inscrire à vos applications à l’aide de leurs propres comptes Microsoft. Dans un premier temps, vous devez [activer l’inscription en libre-service](self-service-sign-up-user-flow.md) pour votre locataire. Puis, vous devez configurer un flux d’utilisateur pour l’application et sélectionner « Compte Microsoft » parmi les options de connexion.

![Compte Microsoft dans un flux d’inscription en libre-service](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="verifying-the-applications-publisher-domain"></a>Vérification du domaine de l’éditeur de l’application
Depuis novembre 2020, les inscriptions de nouvelles applications s’affichent comme étant non vérifiées dans l’invite de consentement de l’utilisateur, sauf si [le domaine de l’éditeur de l’application a été vérifié](../develop/howto-configure-publisher-domain.md) ***et*** si l’identité de l’entreprise a été vérifiée auprès de Microsoft Partner Network puis associée à l’application. ([En savoir plus](../develop/publisher-verification-overview.md) sur ce changement.) Notez que pour les flux d’utilisateur Azure AD, le domaine de l’éditeur s’affiche uniquement lorsque vous utilisez un compte Microsoft ou un autre [locataire Azure AD](azure-ad-account.md) comme fournisseur d’identité. Pour répondre à ces nouvelles exigences, procédez comme suit :

1. [Vérifiez l’identité de votre entreprise à l’aide de votre compte Microsoft Partner Network (MPN)](/partner-center/verification-responses). Ce processus vérifie les informations relatives à votre entreprise et au contact principal de votre entreprise.
1. Effectuez le processus de vérification de l’éditeur pour associer votre compte MPN à votre inscription d’application à l’aide de l’une des options suivantes :
   - Si l’inscription d’application pour le fournisseur d’identité du compte Microsoft se trouve dans un locataire Azure AD, [vérifiez votre application dans le portail d’inscription des applications](../develop/mark-app-as-publisher-verified.md).
   - Si votre inscription d’application pour le fournisseur d’identité du compte Microsoft se trouve dans un locataire Azure AD B2C, [marquez votre application comme étant validée par l’éditeur à l’aide des API Microsoft Graph](../develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) (par exemple, à l’aide d’Afficheur Graph).

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter des utilisateurs de collaboration B2B Azure Active Directory](add-users-administrator.md)
- [Ajouter l’inscription en libre-service à une application](self-service-sign-up-user-flow.md)