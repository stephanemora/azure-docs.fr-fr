---
title: Configurer le consentement de l'utilisateur pour une application – Azure Active Directory
description: Découvrez comment gérer la manière dont les utilisateurs accordent leur consentement pour les autorisations d’application. Vous pouvez simplifier l’expérience utilisateur en accordant le consentement de l’administrateur. Ces méthodes s’appliquent à tous les utilisateurs finaux de votre client Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a187ea9d22efa40c482ea8930be0271d84a899a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273991"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>Configurer le consentement de l'utilisateur final pour une application dans Azure Active Directory
Découvrez comment configurer la manière dont les utilisateurs accordent leur consentement pour les autorisations d’application. Vous pouvez simplifier l’expérience utilisateur en accordant le consentement de l’administrateur. Cet article présente les différentes façons de configurer le consentement de l’utilisateur. Les méthodes s’appliquent à tous les utilisateurs finaux de votre client Azure Active Directory (Azure AD). 

Pour plus d’informations sur le consentement des applications, consultez [Infrastructure de consentement d’Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Prérequis

L’octroi du consentement de l’administrateur vous oblige à vous connecter en tant qu’administrateur général, administrateur d’application ou administrateur d’application cloud.

Pour restreindre l’accès aux applications, vous devez demander l’affectation d’utilisateurs, puis affecter des utilisateurs ou des groupes à l’application.  Pour plus d'informations, voir [Méthodes d'affectation d'utilisateurs et de groupes](methods-for-assigning-users-and-groups.md).

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Accorder le consentement de l’administrateur aux applications d’entreprise dans le portail Azure

Pour accorder le consentement de l’administrateur à une application d’entreprise :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général, administrateur d’application ou administrateur d’application cloud.
2. Cliquez sur **All services** (Tous les services) en haut du menu de navigation de gauche. Le panneau **Extension Azure Active Directory** apparaît.
3. Dans la zone de recherche de filtre, tapez **« Azure Active Directory »** , puis sélectionnez l’élément **Azure Active Directory**.
4. Dans le menu de navigation, cliquez sur **Applications d’entreprise**.
5. Sélectionnez l’application requérant un consentement.
6. Sélectionnez **Autorisations**, puis cliquez sur **Accorder le consentement administrateur**. Vous serez invité à vous connecter pour administrer l’application.
7. Connectez-vous avec un compte disposant des autorisations d’accorder le consentement de l’administrateur pour l’application. 
8. Accordez les autorisations de l’application.

Cette option fonctionne uniquement si l’application est : 

- Inscrite dans votre locataire, ou
- Inscrite dans un autre locataire Azure AD et a été autorisée par au moins un utilisateur final. Une fois qu’un utilisateur final a donné son consentement à une application, AD Azure affiche cette application dans la section **Applications d’entreprise** du portail Azure.

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Accorder le consentement de l’administrateur lors de l’inscription d’une application dans le portail Azure

Pour accorder le consentement de l’administrateur lors de l’inscription d’une application : 

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez au panneau **Inscriptions des applications**.
3. Sélectionnez l’application à autoriser.
4. Sélectionnez **Autorisations de l’API**.
5. Cliquez sur **Accorder le consentement administrateur**.


## <a name="grant-admin-consent-through-a-url-request"></a>Accorder le consentement de l’administrateur via une demande d’URL

Pour accorder le consentement de l’administrateur via une demande d’URL :

1. Créez une demande que vous envoyez à *login.microsoftonline.com* avec les configurations de votre application, à laquelle vous ajoutez `&prompt=admin_consent`. Cette URL doit ressembler à ce qui suit : `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=admin_consent`
2. Une fois que la connexion a été effectuée avec les informations d’identification d’administrateur, l’application reçoit le consentement pour tous les utilisateurs.


## <a name="force-user-consent-through-a-url-request"></a>Forcer le consentement de l’utilisateur via une demande d’URL

Pour forcer les utilisateurs finaux à donner leur consentement à une application chaque fois qu’ils s’authentifient, ajoutez `&prompt=consent` à l’URL de la demande d’authentification.
Cette URL doit ressembler à ce qui suit : `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=consent`

## <a name="next-steps"></a>Étapes suivantes

[Consentement et intégration d’applications pour Azure AD](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[Consentement et octroi d’autorisations pour les applications convergées Azure AD v2.0](../develop/active-directory-v2-scopes.md)

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
