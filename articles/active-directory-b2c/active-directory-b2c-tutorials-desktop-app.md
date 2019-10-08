---
title: Tutoriel - Activer l’authentification dans une application cliente native - Azure Active Directory B2C | Microsoft Docs
description: Tutoriel sur l’utilisation d’Azure Active Directory B2C pour fournir une connexion utilisateur à une application de bureau .NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3740a032db6ca9fd0fb88ce348610684d9f895bc
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326328"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>Didacticiel : Activer l’authentification dans une application cliente native à l’aide d’Azure Active Directory B2C

Ce didacticiel vous montre comment utiliser Azure Active Directory B2C (Azure AD B2C) pour connecter et inscrire des utilisateurs dans une application de bureau Windows Presentation Foundation (WPF). Azure AD B2C permet à vos applications de s’authentifier auprès de comptes de réseaux sociaux, de comptes d’entreprise et de comptes Azure Active Directory à l’aide de protocoles standards ouverts.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter l’application cliente native
> * Configurer l’exemple pour utiliser l’application
> * S’inscrire à l’aide du flux utilisateur

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

- [Créer des flux d’utilisateur](tutorial-create-user-flows.md) pour activer les expériences utilisateur dans votre application
- Installez [Visual Studio 2019](https://www.visualstudio.com/downloads/) avec les charges de travail **Développement .NET Desktop** et **Développement web et ASP.NET**.

## <a name="add-the-native-client-application"></a>Ajouter l’application cliente native

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Enregistrez l’**ID D’APPLICATION** pour l’utiliser lors d’une étape ultérieure.

## <a name="configure-the-sample"></a>Configurer l'exemple

Dans ce tutoriel, vous allez configurer un exemple que vous pouvez télécharger à partir de GitHub. L’exemple d’application de bureau WPF illustre l’inscription et la connexion, et appelle une API web protégée dans Azure AD B2C. [Téléchargez un fichier zip ](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [parcourez le référentiel](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) ou clonez l’exemple à partir de GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Pour changer les paramètres d’application, remplacez `<your-tenant-name>` par votre nom de locataire, puis remplacez `<application-ID`> par l’ID d’application que vous avez enregistré.

1. Ouvrez la solution `active-directory-b2c-wpf` dans Visual Studio.
2. Dans le projet `active-directory-b2c-wpf`, ouvrez le fichier **App.xaml.cs** et effectuez les mises à jour suivantes :

    ```csharp
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. Mettez à jour la variable **PolicySignUpSignIn** avec le nom du flux utilisateur que vous avez créé.

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Exécution de l'exemple

Appuyez sur **F5** pour générer et exécuter l’exemple.

### <a name="sign-up-using-an-email-address"></a>S’inscrire au moyen d’une adresse e-mail

1. Cliquez sur **Se connecter** pour vous inscrire en tant qu’utilisateur. Le flux d’utilisateur **B2C_1_signupsignin1** est utilisé.
2. Azure AD B2C présente une page de connexion avec un lien pour l’abonnement. Si vous ne possédez pas encore de compte, cliquez sur le lien **Inscrivez-vous maintenant**.
3. Le flux de travail d’abonnement présente une page pour collecter et vérifier l’identité de l’utilisateur à l’aide d’une adresse e-mail. Le flux de travail d’inscription collecte également le mot de passe et les attributs demandés, qui sont définis dans le flux d’utilisateur.

    Utilisez une adresse e-mail valide et validez à l’aide d’un code de vérification. Définissez un mot de passe. Entrez des valeurs pour les attributs requis.

    ![Page d’inscription affichée dans le cadre du flux de travail de connexion/inscription](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

4. Cliquez sur **Créer** pour créer un compte local dans le locataire Azure AD B2C.

Maintenant, l’utilisateur peut utiliser son adresse e-mail pour se connecter et utiliser l’application de bureau.

> [!NOTE]
> Si vous cliquez sur le bouton **Appel d’API**, vous recevez une erreur de type « Non autorisé ». Vous recevez cette erreur car vous tentez d’accéder à une ressource du locataire de démonstration. Étant donné que votre jeton d’accès est valide uniquement pour votre locataire Azure AD, l’appel d’API n’est pas autorisé. Passez au didacticiel suivant pour créer une API web protégée pour votre locataire.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Ajouter l’application cliente native
> * Configurer l’exemple pour utiliser l’application
> * S’inscrire à l’aide du flux utilisateur

> [!div class="nextstepaction"]
> [Tutoriel : Accorder l’accès à une API web Node.js à partir d’une application de bureau avec Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
