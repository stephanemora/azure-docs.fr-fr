---
title: 'Tutoriel : Activer l’authentification dans une application monopage'
titleSuffix: Azure AD B2C
description: Dans ce tutoriel, vous allez voir comment utiliser Azure Active Directory B2C afin de fournir une connexion utilisateur à une application monopage JavaScript.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 42c2ca777a999a4d4387646110ed88af84631183
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258896"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Tutoriel : Activer l’authentification dans une application monopage avec Azure AD B2C

Ce tutoriel vous montre comment utiliser Azure Active Directory B2C (Azure AD B2C) pour inscrire et connecter des utilisateurs dans une application monopage.

Dans ce tutoriel, qui est le premier d’une série de deux, vous allez :

> [!div class="checklist"]
> * Ajouter une URL de réponse à une application inscrite dans votre locataire Azure AD B2C
> * Télécharger un exemple de code depuis GitHub
> * Modifier le code de l’exemple d’application pour qu’il fonctionne avec votre locataire
> * Inscrire en utilisant votre flux utilisateur d’inscription/connexion

Le [tutoriel suivant](tutorial-single-page-app-webapi.md) de la série active la partie API web de l’exemple de code.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Avant de poursuivre les étapes de ce tutoriel, vous devez disposer des ressources Azure AD B2C suivantes :

* [Locataire Azure AD B2C](tutorial-create-tenant.md)
* [Application inscrite](tutorial-register-applications.md) dans votre locataire
* [Flux d’utilisateurs créés](tutorial-create-user-flows.md) dans votre locataire

De plus, vous devez disposer des éléments suivants dans votre environnement de développement local :

* [Visual Studio Code](https://code.visualstudio.com/) ou un autre éditeur de code
* [Node.JS](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Mettre à jour l’application

Au cours du deuxième tutoriel que vous avez effectué dans le cadre des prérequis, vous avez inscrit une application web dans Azure AD B2C. Pour permettre la communication avec l’exemple de code de ce tutoriel, ajoutez une URL de réponse (également appelée URI de redirection) à l’inscription d’application.

Pour mettre à jour une application dans votre locataire Azure AD B2C, vous pouvez utiliser notre nouvelle expérience unifiée **Inscriptions d’applications** ou notre expérience héritée **Applications (héritées)** . [En savoir plus sur la nouvelle expérience](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[Inscriptions des applications](#tab/app-reg-ga/)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications**, sélectionnez l’onglet **Applications détenues**, puis l’application *webapp1*.
1. Sous **Web**, sélectionnez le lien **Ajouter un URI**, puis entrez `http://localhost:6420`.
1. Sous **Octroi implicite**, cochez les cases **Jetons d’accès** et **Jetons d’ID**, puis sélectionnez **Enregistrer**.
1. Sélectionnez **Vue d’ensemble**.
1. Notez l’**ID de l’application** pour l’utiliser plus tard, quand vous mettrez à jour le code dans l’application web monopage.

#### <a name="applications-legacy"></a>[Applications (héritées)](#tab/applications-legacy/)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Sélectionnez **Tous les services** en haut à gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Applications (héritées)** , puis l’application *webapp1*.
1. Sous **URL de réponse**, ajoutez `http://localhost:6420`.
1. Sélectionnez **Enregistrer**.
1. Dans la page de propriétés, enregistrez l’**ID d’application**. Vous allez utiliser l’ID de l’application au cours d’une prochaine étape quand vous mettrez à jour le code dans l’application web monopage.

* * *

## <a name="get-the-sample-code"></a>Obtention de l'exemple de code

Dans ce tutoriel, vous configurez un exemple de code que vous téléchargez depuis GitHub pour qu’il fonctionne avec votre locataire B2C. L’exemple montre comment une application monopage peut utiliser Azure AD B2C pour l’inscription et la connexion des utilisateurs, et pour appeler une API web protégée (vous activez l’API web dans le tutoriel suivant de la série).

[Téléchargez un fichier zip ](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clonez l’exemple à partir de GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Mettre à jour l’exemple

Une fois que vous avez obtenu l’exemple, mettez à jour le code à l’aide de votre nom de locataire Azure AD B2C et de l’ID d’application que vous avez enregistré au cours d’une étape précédente.

1. Ouvrez le fichier *authConfig.js* dans le dossier *JavaScriptSPA*.
1. Dans l’objet `msalConfig`, mettez à jour :
    * `clientId` avec la valeur de l’**ID d’application (client)** que vous avez noté dans une étape précédente
    * L’URI de `authority` avec le nom du locataire Azure AD B2C et le nom du flux utilisateur d’inscription/connexion créé dans le cadre des prérequis (par exemple *B2C_1_signupsignin1*).

    ```javascript
    const msalConfig = {
        auth: {
          clientId: "00000000-0000-0000-0000-000000000000", // Replace this value with your Application (client) ID
          authority: b2cPolicies.authorities.signUpSignIn.authority,
          validateAuthority: false
        },
        cache: {
          cacheLocation: "localStorage",
          storeAuthStateInCookie: true
        }
    };

    const loginRequest = {
       scopes: ["openid", "profile"],
    };

    const tokenRequest = {
      scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
    };
    ```

## <a name="run-the-sample"></a>Exécution de l'exemple

1. Ouvrez une fenêtre de console et accédez au répertoire contenant l’exemple. Par exemple :

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Exécutez les commandes suivantes :

    ```console
    npm install && npm update
    npm start
    ```

    La fenêtre de console affiche le numéro de port du serveur Node.js s’exécutant localement :

    ```console
    Listening on port 6420...
    ```
1. Accédez à `http://localhost:6420` pour voir l’application web en cours d’exécution sur votre machine locale.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="Navigateur web montrant l’application monopage s’exécutant localement":::

### <a name="sign-up-using-an-email-address"></a>S’inscrire au moyen d’une adresse e-mail

Cet exemple d’application prend en charge l’inscription, la connexion et la réinitialisation du mot de passe. Dans ce tutoriel, vous vous inscrivez en utilisant une adresse e-mail.

1. Sélectionnez **Se connecter** pour démarrer le flux utilisateur *B2C_1_signupsignin1* que vous avez spécifié lors d’une étape précédente.
1. Azure AD B2C présente une page de connexion qui inclut un lien d’inscription. Étant donné que vous n’avez pas encore de compte, sélectionnez le lien **Inscrivez-vous maintenant**.
1. Le flux de travail d’inscription présente une page pour collecter et vérifier l’identité de l’utilisateur avec une adresse e-mail. Le flux de travail d’inscription collecte également le mot de passe de l’utilisateur et les attributs demandés qui sont définis dans le flux utilisateur.

    Utilisez une adresse e-mail valide et validez à l’aide d’un code de vérification. Définissez un mot de passe. Entrez des valeurs pour les attributs requis.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Navigateur web montrant l’application monopage s’exécutant localement":::

1. Sélectionnez **Créer** pour créer un compte local dans le répertoire Azure AD B2C.

Quand vous sélectionnez **Créer**, l’application affiche le nom de l’utilisateur connecté.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Navigateur web montrant l’application monopage s’exécutant localement":::

Si vous voulez tester la connexion, sélectionnez le bouton **Se déconnecter**, puis sélectionnez **Se connecter**, et connectez-vous avec l’adresse e-mail et le mot de passe que vous avez entrés lors de votre inscription.

### <a name="what-about-calling-the-api"></a>Qu’en est-il de l’appel de l’API ?

Si vous sélectionnez le bouton **Appeler l’API** après vous être connecté, la page d’inscription/connexion du flux utilisateur s’affiche à la place des résultats de l’appel de l’API. Cela est dû au fait que vous n’avez pas encore configuré la partie API de l’application pour communiquer avec une application d’API web inscrite dans *votre*  locataire Azure AD B2C.

À ce stade, l’application tente encore de communiquer avec l’API inscrite dans le locataire de démonstration (fabrikamb2c.onmicrosoft.com) et, comme vous n’êtes pas authentifié auprès de ce locataire, la page d’inscription/connexion est affichée.

Passez au tutoriel suivant de la série dans pour activer l’API protégée (consultez la section [Étapes suivantes](#next-steps)).

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez configuré une application monopage pour qu’elle fonctionne avec un flux utilisateur dans votre locataire Azure AD B2C pour fournir une fonctionnalité d’inscription et de connexion. Vous avez effectué ces étapes :

> [!div class="checklist"]
> * Ajouter une URL de réponse à une application inscrite dans votre locataire Azure AD B2C
> * Télécharger un exemple de code depuis GitHub
> * Modifier le code de l’exemple d’application pour qu’il fonctionne avec votre locataire
> * Inscrire en utilisant votre flux utilisateur d’inscription/connexion

Passez maintenant au prochain tutoriel de la série pour octroyer l’accès à une API web protégée à partir de l’application SPA :

> [!div class="nextstepaction"]
> [Tutoriel : Protéger et accorder l’accès à une API web à partir d’une application monopage >](tutorial-single-page-app-webapi.md)
