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
ms.openlocfilehash: 7ab7c3699abdd5c094b1b14cd53f76023fa8c1ac
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309595"
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

## <a name="register-an-app-using-azure-portal"></a>Inscrire une application à l’aide du portail Azure

> [!NOTE]
> Le portail à utiliser est différent selon que votre application s’exécute dans le cloud public Microsoft Azure ou dans un cloud national ou souverain. Pour plus d’informations, voir [Clouds nationaux](./authentication-national-cloud.md#app-registration-endpoints)


1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft. Vous pouvez également vous connecter au portail Azure du cloud national de votre choix.
1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
1. Dans le volet de navigation de gauche, sélectionnez le service **Azure Active Directory**, puis **Inscriptions d’applications** > **Nouvelle inscription**.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

4. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :
   1. Choisissez les types de comptes pris en charge par votre application (voir [Types de comptes pris en charge](./v2-supported-account-types.md)).
   1. Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple `AspNetCore-WebApp`.
   1. Dans **URI de redirection**, ajoutez le type d’application et la destination d’URI qui acceptera les réponses de jeton retournées après une authentification réussie. Par exemple : `https://localhost:44321/`.  Sélectionnez **Inscription**.
1. Sélectionnez le menu **Authentification** et ajoutez les informations suivantes :
   1. Dans **URL de réponse**, ajoutez `https://localhost:44321/signin-oidc` de type « Web ».
   1. Dans la section **Paramètres avancés**, définissez **URL de déconnexion** sur `https://localhost:44321/signout-oidc`.
   1. Sous **Octroi implicite**, cochez **Jetons d’ID**.
   1. Sélectionnez **Enregistrer**.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

4. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :
   1. Choisissez les types de comptes pris en charge par votre application (voir [Types de comptes pris en charge](./v2-supported-account-types.md)).
   - Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple `MailApp-openidconnect-v2`.
   - Dans la section URI de redirection (facultatif), sélectionnez **Web** dans la zone de liste modifiable et entrez les URI de redirection suivants : `https://localhost:44326/`.
1. Sélectionnez **Inscrire** pour créer l’application.
1. Sélectionnez le menu **Authentification** et ajoutez les informations suivantes :
   - Dans la section **Paramètres avancés** | **Octroi implicite**, cochez **Jetons d’ID**, car cet exemple exige l’activation du [flux d’octroi implicite](v2-oauth2-implicit-grant-flow.md) pour permettre la connexion de l’utilisateur.
1. Sélectionnez **Enregistrer**.

# <a name="javatabjava"></a>[Java](#tab/java)

4. Quand la page **Inscrire une application** apparaît, entrez un nom convivial pour l’application, par exemple « Java-webapp », sélectionnez « Comptes dans un annuaire organisationnel et comptes personnels Microsoft (par exemple, Skype, Xbox, Outlook.com) » et sélectionnez « Application web/API » dans *Type d’application*.
1. Cliquez sur **Inscrire** pour inscrire l’application.
1. Dans le menu de gauche, cliquez sur **Authentification**, puis sous *URI de redirection*, sélectionnez « Web ». Vous devrez entrer deux URI de redirection différents : un pour la page de connexion et un pour la page des utilisateurs du graphique. Pour les deux, vous devez utiliser le même hôte et le même numéro de port, suivis de « /msal4jsample/secure/aad » pour la page de connexion et de « msal4jsample/graph/users » pour la page des utilisateurs.
 Par défaut, l’exemple utilise : 

    - `http://localhost:8080/msal4jsample/secure/aad`. 
    - `http://localhost:8080/msal4jsample/graph/users`

Cliquez sur **Enregistrer**.

# <a name="pythontabpython"></a>[Python](#tab/python)

4. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :
   - Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple `python-webapp`.
   - Affectez à **Types de comptes pris en charge** la valeur **Comptes dans un annuaire organisationnel et comptes personnels Microsoft (par exemple, Skype, Xbox, Outlook.com)** .
   - Dans la section URI de redirection (facultatif), sélectionnez **Web** dans la zone de liste déroulante et entrez les URI de redirection suivants : `http://localhost:5000/getAToken`.
1. Sélectionnez **Inscrire** pour créer l’application.
1. Dans la page **Vue d’ensemble** de l’application, recherchez la valeur **ID d’application (client)** et notez-la. Vous en aurez besoin pour configurer le fichier de configuration Visual Studio pour ce projet.
1. Sélectionnez la section **Authentification** dans la page de vue d’ensemble de l’application.
   - Dans la section **Paramètres avancés**, affectez à **URL de déconnexion** la valeur `http://localhost:5000/logout`.
1. Sélectionnez **Enregistrer**.

---

## <a name="register-an-app-using-powershell"></a>Inscrire une application avec PowerShell

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
