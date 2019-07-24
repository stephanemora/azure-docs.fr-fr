---
title: Application web qui connecte des utilisateurs (inscription d’application) - Plateforme d’identités Microsoft
description: Apprenez à générer une application web qui connecte des utilisateurs (inscription d’application)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ae638f8cbef29c5d167a3ab59188169cbd934ef
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150225"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>Application web qui connecte des utilisateurs - Inscription d’application

Cette page explique les spécificités de l’inscription d’application pour une application web qui connecte des utilisateurs.

Pour inscrire votre application, vous pouvez utiliser les méthodes suivantes :

- [Démarrages rapides d’application web](#register-an-app-using-the-quickstarts) : en plus d’être une excellente première expérience pour la création d’une application, les démarrages rapides du portail Azure contiennent un bouton nommé **Apporter cette modification pour moi**. Vous pouvez utiliser ce bouton pour définir les propriétés dont vous avez besoin, même pour une application existante. Vous devrez adapter les valeurs de ces propriétés à votre propre cas. En particulier, l’URL de l’API web pour votre application sera probablement différente de celle proposée par défaut, ce qui influera également sur l’URI de déconnexion.
- Portail Azure pour [inscrire votre application manuellement](#register-an-app-using-azure-portal)
- PowerShell et outils en ligne de commande

## <a name="register-an-app-using-the-quickstarts"></a>Inscrire une application en utilisant les démarrages rapides

Si vous accédez à ce lien, vous pouvez lancer la création de votre application web :

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>Inscrire une application à l’aide du portail Azure

> [!NOTE]
> Le portail à utiliser est différent selon que votre application s’exécute dans le cloud public Microsoft Azure ou dans un cloud national ou souverain. Pour plus d’informations, voir [Clouds nationaux](./authentication-national-cloud.md#app-registration-endpoints)

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft. Vous pouvez également vous connecter au portail Azure du cloud national de votre choix.
1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
1. Dans le volet de navigation de gauche, sélectionnez le service **Azure Active Directory**, puis **Inscriptions d’applications** > **Nouvelle inscription**.
1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :
   1. Choisissez les types de comptes pris en charge par votre application (voir [Types de comptes pris en charge](./v2-supported-account-types.md)).
   1. Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple `AspNetCore-WebApp`.
   1. Dans **URI de redirection**, ajoutez le type d’application et la destination d’URI qui acceptera les réponses de jeton retournées après une authentification réussie. Par exemple : `https://localhost:44321/`.  Sélectionnez **Inscription**.
1. Sélectionnez le menu **Authentification** et ajoutez les informations suivantes :
   1. Dans **URL de réponse**, ajoutez `https://localhost:44321/signin-oidc`.
   1. Dans la section **Paramètres avancés**, définissez **URL de déconnexion** sur `https://localhost:44321/signout-oidc`.
   1. Sous **Octroi implicite**, cochez **Jetons d’ID**.
   1. Sélectionnez **Enregistrer**.

### <a name="register-an-app-using-powershell"></a>Inscrire une application avec PowerShell

> [!NOTE]
> Actuellement, Azure AD PowerShell crée uniquement des applications avec les types de compte pris en charge suivants :
>
> - MyOrg (comptes dans cet annuaire organisationnel uniquement).
> - AnyOrg (comptes dans un annuaire organisationnel).
>
> Si vous souhaitez créer une application qui connecte des utilisateurs avec leurs comptes personnels Microsoft (par exemple, Skype, XBox, Outlook.com), vous pouvez d’abord créer une application mutualisée (types de compte pris en charge = comptes dans n’importe quel répertoire organisationnel), puis modifiez la propriété `signInAudience` dans le manifeste d’application à partir du portail Azure. Cette procédure est expliquée en détail à l’étape [1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) du didacticiel ASP.NET Core (et peut être étendue aux applications web dans n’importe quel langage).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configuration de code de l’application](scenario-web-app-sign-user-app-configuration.md)
