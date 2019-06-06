---
title: Tutoriel - Accorder l’accès à une API web Node.js à partir d’une application de bureau avec Azure Active Directory B2C | Microsoft Docs
description: Didacticiel sur l’utilisation d’Active Directory B2C pour protéger une API web Node.js et l’appeler depuis une application de bureau .NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b4b70ddfe6ea2baf42227cc83ed0cc14969b3e92
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508094"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Tutoriel : Accorder l’accès à une API web Node.js depuis une application de bureau à l’aide d’Azure Active Directory B2C

Ce didacticiel vous montre comment appeler une ressource d’API web Node.js protégée par Azure Active Directory (Azure AD) B2C depuis une application de bureau Windows Presentation Foundation (WPF).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter une application d’API web
> * Configurer des étendues pour une API web
> * Accorder des autorisations à l’API web
> * Mettre à jour l’exemple pour utiliser l’application

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Effectuez les étapes et les prérequis du [Tutoriel : Activer l’authentification d’application de bureau avec des comptes à l’aide d’Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Ajouter une application d’API web

Les ressources d’API web doivent être inscrites auprès de votre locataire pour pouvoir accepter et répondre aux requêtes de ressources protégées émanant des applications clientes qui présentent un jeton d’accès. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Applications**, puis **Ajouter**.
5. Entrez un nom pour l’application. Par exemple, *webapi1*.
6. Pour **inclure l’application web/l’API web** et **autoriser un flux implicite**, sélectionnez **Oui**.
7. Pour l’**URL de réponse**, entrez un point de terminaison où Azure AD B2C doit retourner les jetons demandés par votre application. Dans ce tutoriel, l'exemple s'exécute localement et écoute `https://localhost:5000`.
8. Pour l’**L’URI de l’ID d’application**, entrez l’identificateur utilisé pour votre API web. L’identificateur complet URI, y compris le domaine, est généré pour vous. Par exemple : `https://contosotenant.onmicrosoft.com/api`.
9. Cliquez sur **Créer**.
10. Sur la page des propriétés, enregistrez l'ID d'application que vous utiliserez pour configurer l'application web.

## <a name="configure-scopes"></a>Configurer des étendues

Les étendues permettent de gérer l'accès aux ressources protégées. Elles sont utilisées par l’API web pour implémenter le contrôle d’accès basé sur les étendues. Par exemple, certains utilisateurs peuvent bénéficier d’un accès en lecture et en écriture tandis que d’autres peuvent disposer d’autorisations d’accès en lecture seule. Dans ce didacticiel, vous allez définir des autorisations d’accès en lecture pour l’API web.

1. Sélectionnez **Applications**, puis *webapi1*.
2. Sélectionnez **Étendues publiées**.
3. Pour **Étendue**, entrez `Hello.Read`, puis pour la description, entrez `Read access to hello`.
4. Pour **Étendue**, entrez `Hello.Write`, puis pour la description, entrez `Write access to hello`.
5. Cliquez sur **Enregistrer**.

Les étendues publiées peuvent servir à accorder à une application cliente une autorisation d’accès à l’API web.

## <a name="grant-permissions"></a>Accorder des autorisations

Pour appeler une API web protégée à partir d’une application, vous devez accorder à cette application des autorisations d’accès à l’API. Dans le tutoriel de prérequis, vous avez créé une application web nommée *app1* dans Azure AD B2C. Vous allez utiliser cette application pour appeler l’API web.

1. Sélectionnez **Applications**, puis *nativeapp1*.
2. Sélectionnez **Accès aux API**, puis **Ajouter**.
3. Dans la liste déroulante **Sélectionner une API**, sélectionnez *webapi1*.
4. Dans la liste déroulante **Sélectionnez des étendues**, sélectionnez les étendues **Hello.Read** et **Hello.Write** que vous avez définies précédemment.
5. Cliquez sur **OK**.

Un utilisateur s’authentifie auprès d’Azure AD B2C pour utiliser l’application de bureau WPF. L’application de bureau obtient un octroi d’autorisation d’Azure AD B2C pour accéder à l’API web protégée.

## <a name="configure-the-sample"></a>Configurer l'exemple

Maintenant que l’API web est inscrite et que les étendues sont définies, vous devez configurer le code de l’API web pour utiliser votre locataire Azure AD B2C. Dans ce tutoriel, vous allez configurer un exemple d’application web Node.js que vous pouvez télécharger à partir de GitHub. 

[Téléchargez un fichier zip ](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) ou clonez l’exemple d’application web à partir de GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
L’exemple d’API web Node.js utilise la bibliothèque Passport.js pour activer Azure AD B2C afin de protéger les appels vers l’API. 

1. Ouvrez le fichier `index.js` .
2. Configurez l’exemple avec les informations d’inscription des locataires Azure AD B2C. Modifiez les lignes de code suivantes :

    ```nodejs
    var tenantID = "<your-tenant-name>.onmicrosoft.com";
    var clientID = "<application-ID>";
    var policyName = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Exécution de l'exemple

1. Lancez une invite de commande Node.js.
2. Accédez au répertoire contenant l’exemple Node.js. Par exemple `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Exécutez les commandes suivantes :
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Exécuter l’application de bureau

1. Ouvrez la solution **active directory b2c wpf** dans Visual Studio.
2. Appuyez sur **F5** pour exécuter l’application de bureau.
3. Connectez-vous à l’aide de l’adresse e-mail et du mot de passe utilisés dans le [didacticiel Activer l’authentification d’application de bureau avec des comptes à l’aide d’Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).
4. Cliquez sur le bouton **Appel d’API**. 

L’application de bureau envoie une requête à l’API web et obtient une réponse avec le nom d’affichage de l’utilisateur connecté. L’application de bureau que vous avez protégée appelle l’API web protégée dans votre locataire Azure AD B2C.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Ajouter une application d’API web
> * Configurer des étendues pour une API web
> * Accorder des autorisations à l’API web
> * Mettre à jour l’exemple pour utiliser l’application

> [!div class="nextstepaction"]
> [Tutoriel : Ajouter des fournisseurs d’identité à vos applications dans Azure Active Directory B2C](tutorial-add-identity-providers.md)
