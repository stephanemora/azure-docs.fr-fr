---
title: Tutoriel - Activer l’authentification dans une application monopage - Azure Active Directory B2C | Microsoft Docs
description: Didacticiel sur l’utilisation d’Azure Active Directory B2C pour fournir une connexion utilisateur pour une application à page unique (JavaScript).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6e4be3a14a6cfba6b32bea8a77975e3e34ea012d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507812"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>Didacticiel : Activer l’authentification dans une application monopage à l’aide d’Azure Active Directory B2C

Ce tutoriel vous montre comment utiliser Azure Active Directory (Azure AD) B2C pour connecter et inscrire des utilisateurs dans une application monopage. Azure AD B2C permet à vos applications de s’authentifier auprès de comptes de réseaux sociaux, de comptes d’entreprise et de comptes Azure Active Directory à l’aide de protocoles standards ouverts.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Mettre à jour l’application dans Azure AD B2C
> * Configurer l’exemple pour utiliser l’application
> * S’inscrire à l’aide du flux utilisateur

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

* [Créer des flux d’utilisateur](tutorial-create-user-flows.md) pour activer les expériences utilisateur dans votre application 
* Installer [Visual Studio 2019](https://www.visualstudio.com/downloads/) avec la charge de travail **Développement ASP.NET et web**.
* Installer le [SDK .NET Core 2.0.0](https://www.microsoft.com/net/core) ou une version ultérieure
* Installez [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Mettre à jour l’application

Dans le tutoriel que vous avez effectué dans le cadre des prérequis, vous avez ajouté une application web dans Azure AD B2C. Pour permettre la communication avec l’exemple de ce tutoriel, vous devez ajouter un URI de redirection à l’application dans Azure AD B2C.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Applications**, puis l’application *webapp1*.
5. Sous **URL de réponse**, ajoutez `http://localhost:6420`.
6. Sélectionnez **Enregistrer**.
7. Dans la page des propriétés, enregistrez l’ID d’application que vous utiliserez pour configurer l’application web.
8. Sélectionnez **Clés**, **Générer la clé**, puis **Enregistrer**. Enregistrez la clé que vous utiliserez pour configurer l’application web.

## <a name="configure-the-sample"></a>Configurer l'exemple

Dans ce tutoriel, vous allez configurer un exemple que vous pouvez télécharger à partir de GitHub. L’exemple montre comment une application monopage peut utiliser Azure AD B2C pour l’inscription et la connexion des utilisateurs, et pour appeler une API web protégée.

[Téléchargez un fichier zip ](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clonez l’exemple à partir de GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

Pour modifier les paramètres :

1. Ouvrez le fichier `index.html` dans l’exemple.
2. Configurez l’exemple avec l’ID d’application et la clé que vous avez enregistrée précédemment. Modifiez les lignes de code suivantes en remplaçant les valeurs par les noms de votre répertoire et de vos API :

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID>',
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
    };
    ```

    Le nom de flux d’utilisateur utilisé dans ce tutoriel est **B2C_1_signupsignin1**. Si vous utilisez un autre nom de flux d’utilisateur, indiquez-le dans la valeur `authority`.

## <a name="run-the-sample"></a>Exécution de l'exemple

1. Lancez une invite de commande Node.js.
2. Accédez au répertoire contenant l’exemple Node.js. Par exemple `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Exécutez les commandes suivantes :

    ```
    npm install && npm update
    node server.js
    ```

    La fenêtre de console affiche le numéro de port sur lequel l’application est hébergée.
    
    ```
    Listening on port 6420...
    ```

4. Utilisez un navigateur pour accéder à l’adresse `http://localhost:6420` afin d’afficher l’application.

L’exemple prend en charge l’inscription et la connexion des utilisateurs, la modification d’un profil, et la réinitialisation d’un mot de passe. Ce tutoriel met en évidence la manière dont un utilisateur s’inscrit à l’aide d’une adresse e-mail.

### <a name="sign-up-using-an-email-address"></a>S’inscrire au moyen d’une adresse e-mail

1. Cliquez sur **Connexion** pour vous inscrire en tant qu’utilisateur de l’application. Cette méthode utilise le flux d’utilisateur **B2C_1_signupsignin1** que vous avez défini à l’étape précédente.
2. Azure AD B2C présente une page de connexion avec un lien pour l’abonnement. Si vous ne possédez pas encore de compte, cliquez sur le lien **Inscrivez-vous maintenant**. 
3. Le flux de travail d’abonnement présente une page pour collecter et vérifier l’identité de l’utilisateur à l’aide d’une adresse e-mail. Le flux de travail d’inscription collecte également le mot de passe et les attributs demandés, qui sont définis dans le flux d’utilisateur.

    Utilisez une adresse e-mail valide et validez à l’aide d’un code de vérification. Définissez un mot de passe. Entrez des valeurs pour les attributs requis. 

    ![Flux de travail d’abonnement](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Cliquez sur **Créer** pour créer un compte local dans le répertoire Azure AD B2C.

Maintenant, l’utilisateur peut utiliser son adresse e-mail pour se connecter et utiliser l’application SPA.

> [!NOTE]
> Une fois connecté, l’application affiche une erreur d’autorisations insuffisantes. Vous recevez cette erreur car vous tentez d’accéder à une ressource du répertoire de démonstration. Étant donné que votre jeton d’accès est valide uniquement pour votre répertoire Azure AD, l’appel d’API n’est pas autorisé. Passez au didacticiel suivant pour créer une API web protégée pour votre répertoire.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Mettre à jour l’application dans Azure AD B2C
> * Configurer l’exemple pour utiliser l’application
> * S’inscrire à l’aide du flux utilisateur

> [!div class="nextstepaction"]
> [Tutoriel : Accorder l’accès à une API web ASP.NET Core dans une application monopage à l’aide d’Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
