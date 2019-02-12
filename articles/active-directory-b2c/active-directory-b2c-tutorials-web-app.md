---
title: Tutoriel - Activer l’authentification dans une application web - Azure Active Directory B2C | Microsoft Docs
description: Didacticiel sur l’utilisation d’Azure Active Directory B2C pour fournir une connexion utilisateur pour une application web ASP.NET.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: ced74cc5af829c3677a12aaf4bffdf9a518f6053
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755626"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Tutoriel : Activer l’authentification dans une application web à l’aide d’Azure Active Directory B2C

Ce tutoriel vous montre comment utiliser Azure Active Directory B2C pour connecter et inscrire des utilisateurs dans une application web ASP.NET. Azure AD B2C permet à vos applications de s’authentifier auprès de comptes de réseaux sociaux, de comptes d’entreprise et de comptes Azure Active Directory à l’aide de protocoles standards ouverts.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Mettre à jour l’application dans Azure AD B2C
> * Configurer l’exemple pour utiliser l’application
> * S’inscrire à l’aide du flux utilisateur

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

- [Créer des flux d’utilisateur](tutorial-create-user-flows.md) pour activer les expériences utilisateur dans votre application 
- Installer [Visual Studio 2017](https://www.visualstudio.com/downloads/) avec la charge de travail **Développement ASP.NET et web**.

## <a name="update-the-application"></a>Mettre à jour l’application

Dans le tutoriel que vous avez effectué dans le cadre des prérequis, vous avez ajouté une application web dans Azure AD B2C. Pour permettre la communication avec l’exemple de ce tutoriel, vous devez ajouter un URI de redirection à l’application dans Azure AD B2C.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Applications**, puis l’application *webapp1*.
5. Sous **URL de réponse**, ajoutez `https://localhost:44316`.
6. Sélectionnez **Enregistrer**.
7. Dans la page des propriétés, enregistrez l’ID d’application que vous utiliserez pour configurer l’application web.
8. Sélectionnez **Clés**, **Générer la clé**, puis **Enregistrer**. Enregistrez la clé que vous utiliserez pour configurer l’application web.

## <a name="configure-the-sample"></a>Configurer l'exemple

Dans ce tutoriel, vous allez configurer un exemple que vous pouvez télécharger à partir de GitHub. L’exemple utilise ASP.NET pour fournir une liste de tâches simple. L’exemple utilise des [composants middleware (intergiciel) Microsoft OWIN](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Téléchargez un fichier zip ](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) ou clonez l’exemple à partir de GitHub. Assurez-vous que vous extrayez l’exemple de fichier dans un dossier où la longueur totale du chemin d’accès est inférieure à 260 caractères.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

L’exemple de solution contient les deux projets suivants :

- **TaskWebApp** : permettant de créer et de modifier une liste de tâches. L’exemple utilise le flux d’utilisateur d’**inscription ou de connexion** pour inscrire ou connecter des utilisateurs.
- **TaskService** : prend en charge les fonctionnalités de création, de lecture, de mise à jour et de suppression des listes de tâches. L’API est protégée par Azure AD B2C et appelée par TaskWebApp.

Vous devez modifier l’exemple pour qu’il utilise l’application qui est inscrite dans votre locataire, ce qui inclut l’ID d’application et la clé que vous avez enregistrés précédemment. Vous devez également configurer les flux d’utilisateur que vous avez créés. L’exemple définit les valeurs de configuration en tant que paramètres dans le fichier Web.config. Pour modifier les paramètres :

1. Ouvrez la solution **B2C-WebAPI-DotNet** dans Visual Studio.
2. Dans le projet **TaskWebApp**, ouvrez le fichier **Web.config**. Remplacez la valeur pour `ida:Tenant` avec le nom du locataire que vous avez créé. Remplacez la valeur pour `ida:ClientId` avec l’ID d’application que vous avez enregistré. Remplacez la valeur de `ida:ClientSecret` avec la clé que vous avez enregistrée.
3. Dans le fichier **Web.config**, remplacez la valeur pour `ida:SignUpSignInPolicyId` avec `b2c_1_signupsignin1`. Remplacez la valeur pour `ida:EditProfilePolicyId` avec `b2c_1_profileediting1`. Remplacez la valeur pour `ida:ResetPasswordPolicyId` avec `b2c_1_passwordreset1`.

## <a name="run-the-sample"></a>Exécution de l'exemple

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **TaskWebApp**, puis cliquez sur **Définir en tant que projet de démarrage**.
2. Appuyez sur **F5**. Le navigateur par défaut se lance à l’adresse du site web local `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>S’inscrire au moyen d’une adresse e-mail

1. Cliquez sur **S’inscrire/Se connecter** pour vous inscrire en tant qu’utilisateur de l’application. Le flux d’utilisateur **b2c_1_signupsignin1** est utilisé.
2. Azure AD B2C présente une page de connexion avec un lien pour l’abonnement. Si vous ne disposez pas encore d’un compte, sélectionnez **Inscrivez-vous maintenant**. Le flux de travail d’abonnement présente une page pour collecter et vérifier l’identité de l’utilisateur à l’aide d’une adresse e-mail. Le flux de travail d’inscription collecte également le mot de passe et les attributs demandés, qui sont définis dans le flux d’utilisateur.
3. Utilisez une adresse e-mail valide et validez à l’aide d’un code de vérification. Définissez un mot de passe. Entrez des valeurs pour les attributs requis. 

    ![Flux de travail d’abonnement](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. Cliquez sur **Créer** pour créer un compte local dans le locataire Azure AD B2C.

Maintenant, l’utilisateur peut utiliser son adresse e-mail pour se connecter et utiliser l’application web.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Mettre à jour l’application dans Azure AD B2C
> * Configurer l’exemple pour utiliser l’application
> * S’inscrire à l’aide du flux utilisateur

> [!div class="nextstepaction"]
> [Tutoriel : Utiliser Azure Active Directory B2C pour protéger une API Web ASP.NET](active-directory-b2c-tutorials-web-api.md)
