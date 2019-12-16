---
title: 'Didacticiel : Activer l’authentification dans une application monopage'
titleSuffix: Azure AD B2C
description: Dans ce tutoriel, vous allez voir comment utiliser Azure Active Directory B2C afin de fournir une connexion utilisateur à une application monopage JavaScript.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/14/2019
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 2f18a7ddeb5256c660c6458fc8d2d29f83e04429
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950155"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c-azure-ad-b2c"></a>Didacticiel : Activer l’authentification dans une application monopage avec Azure Active Directory B2C (Azure AD B2C)

Ce tutoriel vous montre comment utiliser Azure Active Directory B2C (Azure AD B2C) pour connecter et inscrire des utilisateurs dans une application monopage. Azure AD B2C permet à vos applications de s’authentifier auprès de comptes de réseaux sociaux, de comptes d’entreprise et de comptes Azure Active Directory à l’aide de protocoles standards ouverts.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Mettre à jour l’application dans Azure AD B2C
> * Configurer l’exemple pour utiliser l’application
> * S’inscrire à l’aide du flux utilisateur

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Avant de poursuivre les étapes de ce tutoriel, vous devez disposer des ressources Azure AD B2C suivantes :

* [Locataire Azure AD B2C](tutorial-create-tenant.md)
* [Application inscrite](tutorial-register-applications.md) dans votre locataire
* [Flux d’utilisateurs créés](tutorial-create-user-flows.md) dans votre locataire

De plus, vous devez disposer des éléments suivants dans votre environnement de développement local :

* Éditeur de code, par exemple [Visual Studio Code](https://code.visualstudio.com/) ou [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [SDK .NET Core 2.2](https://dotnet.microsoft.com/download) ou ultérieur
* [Node.JS](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Mettre à jour l’application

Au cours du deuxième tutoriel que vous avez effectué dans le cadre des prérequis, vous avez inscrit une application web dans Azure AD B2C. Pour permettre la communication avec l’exemple de ce tutoriel, vous devez ajouter un URI de redirection à l’application dans Azure AD B2C.

Vous pouvez utiliser l’expérience **Applications** actuelle ou notre nouvelle expérience unifiée **Inscriptions d’applications (préversion)** pour mettre à jour l’application. [En savoir plus sur la nouvelle expérience](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Applications](#tab/applications/)

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
1. Sélectionnez **Tous les services** en haut à gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Applications**, puis l’application *webapp1*.
1. Sous **URL de réponse**, ajoutez `http://localhost:6420`.
1. Sélectionnez **Enregistrer**.
1. Dans la page de propriétés, enregistrez l’**ID d’application**. Vous allez utiliser l’ID de l’application au cours d’une prochaine étape quand vous mettrez à jour le code dans l’application web monopage.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Inscriptions d’applications (préversion)](#tab/app-reg-preview/)

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications (préversion)** , sélectionnez l’onglet **Applications détenues**, puis l’application *webapp1*.
1. Sélectionnez **Authentification**, puis **Essayer la nouvelle expérience** (si l’option est affichée).
1. Sous **Web**, sélectionnez le lien **Ajouter un URI**, entrez `http://localhost:6420`, puis sélectionnez **Enregistrer**.
1. Sélectionnez **Vue d’ensemble**.
1. Notez l’**ID de l’application** pour l’utiliser plus tard, quand vous mettrez à jour le code dans l’application web monopage.

* * *

## <a name="get-the-sample-code"></a>Obtention de l'exemple de code

Dans ce tutoriel, vous configurez un exemple de code que vous téléchargez depuis GitHub. L’exemple montre comment une application monopage peut utiliser Azure AD B2C pour l’inscription et la connexion des utilisateurs, et pour appeler une API web protégée.

[Téléchargez un fichier zip ](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clonez l’exemple à partir de GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Mettre à jour l’exemple

Une fois que vous avez obtenu l’exemple, mettez à jour le code à l’aide de votre nom de locataire Azure AD B2C et de l’ID d’application que vous avez enregistré au cours d’une étape précédente.

1. Ouvrez le fichier `index.html` à la racine du répertoire de l’exemple.
1. Dans la définition de `msalConfig`, modifiez la valeur de **clientId** à l’aide de l’ID d’application que vous avez enregistré au cours d’une étape précédente. Mettez ensuite à jour la valeur de l’URI **authority** à l’aide de votre nom de locataire Azure AD B2C. Mettez également à jour l’URI à l’aide du nom du flux d’utilisateurs d’inscription/de connexion créé dans l’un des prérequis (par exemple *B2C_1_signupsignin1*).

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", //This is your client ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Le nom de flux d’utilisateur utilisé dans ce tutoriel est **B2C_1_signupsignin1**. Si vous utilisez un autre nom de flux d’utilisateurs, indiquez-le dans la valeur `authority`.

## <a name="run-the-sample"></a>Exécution de l'exemple

1. Ouvrez une fenêtre de console et accédez au répertoire contenant l’exemple. Par exemple :

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Exécutez les commandes suivantes :

    ```
    npm install && npm update
    node server.js
    ```

    La fenêtre de console affiche le numéro de port du serveur Node.js s’exécutant localement :

    ```
    Listening on port 6420...
    ```

1. Accédez à `http://localhost:6420` dans votre navigateur pour voir l’application.

L’exemple prend en charge l’inscription et la connexion des utilisateurs, la modification d’un profil, et la réinitialisation d’un mot de passe. Ce tutoriel met en évidence la manière dont un utilisateur s’inscrit à l’aide d’une adresse e-mail.

### <a name="sign-up-using-an-email-address"></a>S’inscrire au moyen d’une adresse e-mail

> [!WARNING]
> Après l’inscription ou la connexion, une [erreur indiquant une insuffisance d’autorisations](#error-insufficient-permissions) peut apparaître. En raison de l’implémentation actuelle de l’exemple de code, cette erreur est attendue. Ce problème sera résolu dans une version ultérieure de l’exemple de code, et cet avertissement sera supprimé.

1. Sélectionnez **Connexion** pour démarrer le flux utilisateur *B2C_1_signupsignin1* que vous avez spécifié lors d’une étape précédente.
1. Azure AD B2C présente une page de connexion avec un lien pour l’abonnement. Étant donné que vous n’avez pas encore de compte, sélectionnez le lien **Inscrivez-vous maintenant**.
1. Le flux de travail d’abonnement présente une page pour collecter et vérifier l’identité de l’utilisateur à l’aide d’une adresse e-mail. Le flux de travail d’inscription collecte également le mot de passe et les attributs demandés, qui sont définis dans le flux d’utilisateur.

    Utilisez une adresse e-mail valide et validez à l’aide d’un code de vérification. Définissez un mot de passe. Entrez des valeurs pour les attributs requis.

    ![Page d’inscription présentée par le flux utilisateur de connexion/inscription](./media/active-directory-b2c-tutorials-spa/azure-ad-b2c-sign-up-workflow.png)

1. Sélectionnez **Créer** pour créer un compte local dans le répertoire Azure AD B2C.

Quand vous sélectionnez **Créer**, la page d’inscription se ferme et la page de connexion réapparaît.

Vous pouvez désormais utiliser votre adresse e-mail et votre mot de passe pour vous connecter à l’application.

### <a name="error-insufficient-permissions"></a>Erreur : autorisations insuffisantes

Une fois que vous êtes connecté, l’application peut afficher une erreur indiquant une insuffisance d’autorisations :

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

Cette erreur s’affiche car l’application web tente d’accéder à une API web qui est protégée par l’annuaire de démonstration *fabrikamb2c*. Étant donné que votre jeton d’accès n’est valide que pour votre annuaire Azure AD, l’appel d’API n’est pas autorisé.

Pour corriger cette erreur, passez au tutoriel suivant de la série (voir [Étapes suivantes](#next-steps)) afin de créer une API web protégée pour votre annuaire.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Mettre à jour l’application dans Azure AD B2C
> * Configurer l’exemple pour utiliser l’application
> * S’inscrire à l’aide du flux utilisateur

Passez maintenant au prochain tutoriel de la série pour octroyer l’accès à une API web protégée à partir de l’application SPA :

> [!div class="nextstepaction"]
> [Tutoriel : Accorder l’accès à une API web ASP.NET Core dans une application monopage à l’aide d’Azure Active Directory B2C >](active-directory-b2c-tutorials-spa-webapi.md)
