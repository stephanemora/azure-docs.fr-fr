---
title: Infrastructure de consentement d’Azure Active Directory
description: Découvrez l’infrastructure de consentement dans Azure Active Directory et comment elle facilite le développement d’applications web multi-locataires et d’applications clientes natives.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev
ms.openlocfilehash: 942151c0ce2a3a79dbdce9b90adea721456f920f
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288473"
---
# <a name="azure-active-directory-consent-framework"></a>Infrastructure de consentement d’Azure Active Directory

L’infrastructure de consentement d’Azure Active Directory (Azure AD) facilite le développement d’applications web multi-locataires et d’applications clientes natives. Ces applications autorisent la connexion au moyen de comptes d’utilisateurs d’un locataire Azure AD qui est différent de celui où l’application a été inscrite. Elles peuvent également avoir à accéder aux API web telles que l’API Microsoft Graph (pour l’accès à Azure AD, à Intune et aux services d’Office 365) et d’autres API de services Microsoft, en plus de vos propres API web.

L’infrastructure est basée sur le consentement d’un utilisateur ou d’un administrateur à l’inscription d’une application dans son répertoire, ce qui peut impliquer l’accès aux données du répertoire. Par exemple, si une application cliente web doit lire les informations de calendrier de l’utilisateur à partir d’Office 365, cet utilisateur doit d’abord donner son consentement à l’application cliente. Une fois le consentement donné, l’application cliente sera en mesure d’appeler l’API Microsoft Graph au nom de l’utilisateur et d’utiliser les informations de calendrier en fonction des besoins. L’[API Microsoft Graph](https://developer.microsoft.com/graph) permet d’accéder aux données d’Office 365 (comme les calendriers et les messages Exchange, les sites et les listes SharePoint, les documents OneDrive, les blocs-notes OneNote, les tâches Organiseur et les classeurs Excel), ainsi qu’aux utilisateurs et groupes d’Azure AD, et à d’autres objets de données provenant d’autres services cloud Microsoft.

L'infrastructure de consentement est conçue sur OAuth 2.0 et ses différents flux, notamment l’octroi d’un code d’autorisation et d’informations d'identification du client, à l'aide de clients publics ou confidentiels. En utilisant OAuth 2.0, Azure AD permet de créer de nombreux types d’applications clientes, sur téléphone, tablette, serveur ou web, et d’accéder aux ressources requises.

Pour plus d’informations sur l’utilisation de l’infrastructure de consentement avec les demandes d’autorisation OAuth2.0, consultez [Autoriser l’accès aux applications web à l’aide d’OAuth 2.0 et d’Azure AD](v1-protocols-oauth-code.md) et [Scénarios d’authentification pour Azure AD](authentication-scenarios.md). Pour plus d’informations sur l’obtention d’un accès autorisé à Office 365 via Microsoft Graph, consultez [Authentification de l’application avec Microsoft Graph](https://developer.microsoft.com/graph/docs/authorization/auth_overview).

## <a name="consent-experience---an-example"></a>Exemple d’expérience de consentement

Les étapes suivantes vous montrent comment l’expérience de consentement fonctionne à la fois pour le développeur d’applications et l’utilisateur.

1. Supposons que vous ayez une application cliente web devant demander des autorisations spécifiques pour accéder à une ressource/API. Vous allez apprendre à effectuer cette configuration dans la section suivante, mais le portail Azure est essentiellement utilisé pour déclarer des demandes d’autorisation au moment de la configuration. Comme d’autres paramètres de configuration, elles sont incluses dans l’inscription Azure AD de l’application :

  ![Autorisations pour d'autres applications](./media/quickstart-v1-integrate-apps-with-azure-ad/requiredpermissions.png)

1. Considérons que les autorisations de votre application ont été mises à jour, que l'application s'exécute et qu’un utilisateur s'apprête à l’utiliser pour la première fois. L’application doit tout d’abord obtenir un code d’autorisation du point de terminaison `/authorize` d’Azure AD. Le code d’autorisation peut ensuite être utilisé pour obtenir un nouvel accès et un jeton d’actualisation.

1. Si l’utilisateur n’est pas déjà authentifié, le point de terminaison `/authorize` d’Azure AD l’invite à se connecter.

  ![Connexion d’un utilisateur ou d’un administrateur à Azure AD](./media/quickstart-v1-integrate-apps-with-azure-ad/usersignin.png)

1. Une fois l'utilisateur connecté, Azure AD déterminera s’il est nécessaire d'afficher une page de consentement pour l’utilisateur. La détermination est différente selon que l’utilisateur (ou l’administrateur de son organisation), a déjà accordé ou non son consentement à l'application. Si l'autorisation n'a pas encore été accordée, Azure AD invite l'utilisateur à fournir son consentement et affiche les autorisations dont il a besoin pour fonctionner. L’ensemble des autorisations qui s’affichent dans la boîte de dialogue de consentement correspondent à celles sélectionnées dans la section **Autorisations déléguées** du portail Azure.

  ![Expérience de consentement de l'utilisateur](./media/quickstart-v1-integrate-apps-with-azure-ad/consent.png)

1. Une fois que l'utilisateur a donné son consentement, un code d'autorisation est retourné à votre application, qui est utilisé pour acquérir un jeton d'accès et un jeton d'actualisation. Pour plus d’informations sur ce flux, consultez [Type d’application API web](web-api.md).

1. En tant qu’administrateur, vous pouvez également donner votre consentement pour les autorisations déléguées d’une application pour le compte de tous les utilisateurs de votre client. Le consentement administrateur empêche l’affichage d’une boîte de dialogue pour chaque utilisateur dans le locataire, ce qui peut être fait dans le [portail Azure](https://portal.azure.com) par les utilisateurs avec le rôle administrateur. Dans la page **Paramètres** de votre application, sélectionnez **Autorisations requises**, puis cliquez sur le bouton **Accorder des autorisations**.

  ![Accorder des autorisations pour un consentement administrateur explicite](./media/quickstart-v1-integrate-apps-with-azure-ad/grantpermissions.png)

  > [!IMPORTANT]
  > Pour les applications monopages (SPA) qui utilisent ADAL.js, vous devez accorder un consentement explicite à l’aide du bouton **Accorder des autorisations**. Sinon, l’application échoue lorsque le jeton d’accès est demandé.

## <a name="next-steps"></a>Étapes suivantes

* Consultez [comment convertir une application multi-locataire](howto-convert-app-to-be-multi-tenant.md)
* Pour plus de détails, découvrez [la façon dont le consentement est pris en charge au niveau de la couche du protocole OAuth 2.0 pendant le flux d’octroi de code d’autorisation.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)
