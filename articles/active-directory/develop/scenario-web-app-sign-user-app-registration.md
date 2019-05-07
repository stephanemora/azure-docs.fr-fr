---
title: Application Web qui connecte les utilisateurs (inscription de l’application) - plateforme d’identité Microsoft
description: Découvrez comment créer une application web qui connecte les utilisateurs (inscription de l’application)
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
ms.openlocfilehash: e6f512911811535818f4ad857c5c3b956870f619
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074544"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>Application Web qui connecte les utilisateurs - inscription de l’application

Cette page explique les spécificités de l’inscription d’application pour une application web que les utilisateurs se connecte.

Pour inscrire votre application, vous pouvez utiliser :

- Le [web Démarrages rapides application](#register-an-app-using-the-quickstarts) -en plus d’être une excellente première expérience avec la création d’une application, les guides de démarrage rapide dans le portail Azure contiennent un bouton nommé **apporter cette modification pour moi**. Vous pouvez utiliser ce bouton pour définir les propriétés que vous avez besoin, même pour une application existante. Vous devrez adapter les valeurs de ces propriétés à votre propre cas. En particulier, l’URL de l’API web pour votre application est probablement sera différente de celle proposée par défaut, ce qui affectera également l’URI de déconnexion.
- Le portail Azure pour [inscrire votre application manuellement](#register-an-app-using-azure-portal)
- PowerShell et outils de ligne de commande

## <a name="register-an-app-using-the-quickstarts"></a>Inscrire une application en utilisant les Démarrages rapides

Si vous accédez à ce lien, vous pouvez créer d’amorçage la création de votre application web :

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>Inscrire une application à l’aide du portail Azure

> [!NOTE]
> doit utiliser le portail est différent selon si votre application s’exécute dans le cloud public Microsoft Azure ou dans un cloud national ou souverain. Pour plus d’informations, consultez [Clouds nationaux](./authentication-national-cloud.md#app-registration-endpoints)

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft. Vous pouvez également vous connecter dans le cloud national Azure portal de choix.
1. Si votre compte donne votre accès à plusieurs locataires, sélectionnez votre compte dans l’angle supérieur droit, définissez votre session de portail vers Azure AD souhaité client.
1. Dans le volet de navigation de gauche, sélectionnez le **Azure Active Directory** de service, puis sélectionnez **inscriptions** > **nouvelle inscription**.
1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :
   - Choisissez les types de compte pris en charge pour votre application (consultez [types de comptes pris en charge](./v2-supported-account-types.md))
   - Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple `AspNetCore-WebApp`.
   - Dans **URL de réponse**, ajoutez l’URL de réponse de votre application, par exemple `https://localhost:44321/`, puis sélectionnez **inscrire**.
1. Sélectionnez le menu **Authentification** et ajoutez les informations suivantes :
- Dans **URL de réponse**, ajoutez `https://localhost:44321/signin-oidc` et sélectionnez **Inscrire**.
- Dans le **paramètres avancés** section, définissez **URL de déconnexion** à `https://localhost:44321/signout-oidc`.
- Sous **Octroi implicite**, cochez **Jetons d’ID**.
- Sélectionnez **Enregistrer**.

### <a name="register-an-app-using-powershell"></a>Inscrire une application à l’aide de PowerShell

> [!NOTE]
> Actuellement Azure AD PowerShell crée uniquement des applications avec les types de compte pris en charge suivants :
>
> - MyOrg (comptes dans ce répertoire d’organisation uniquement)
> - AnyOrg (comptes dans n’importe quel répertoire d’organisation).
>
> Si vous souhaitez créer une application que les utilisateurs se connecte avec leurs Accounts personnels Microsoft (par exemple, Skype, XBox, Outlook.com), vous devez commencer par créer une application mutualisée (prise en charge des types de compte = comptes dans n’importe quel répertoire d’organisation), puis modifiez le `signInAudience` propriété dans le manifeste d’application à partir du portail Azure. Cela est expliqué en détail dans l’étape [1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) du didacticiel ASP.NET Core (et peut être étendue aux applications web dans n’importe quel langage).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configuration de code de l’application](scenario-web-app-sign-user-app-configuration.md)
