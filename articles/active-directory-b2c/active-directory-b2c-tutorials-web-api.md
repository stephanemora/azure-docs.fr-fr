---
title: Tutoriel - Accorder l’accès à une API web ASP.NET à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Tutoriel sur l’utilisation d’Active Directory B2C pour protéger une API web ASP.NET et l’appeler à partir d’une application web ASP.NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 339b118e48a01469312a40e6b0652a4ffb90291a
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347131"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Didacticiel : Accorder l’accès à une API web ASP.NET à l’aide d’Azure Active Directory B2C

Ce tutoriel vous montre comment appeler une ressource d’API web protégée dans Azure Active Directory (Azure AD) B2C à partir d’une application web ASP.NET.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter une application d’API web
> * Configurer des étendues pour une API web
> * Accorder des autorisations à l’API web
> * Configurer l’exemple pour utiliser l’application

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Effectuez les étapes et les prérequis du [Tutoriel : Activer l’authentification dans une application web à l’aide d’Azure Active Directory B2C](active-directory-b2c-tutorials-web-app.md).

## <a name="add-a-web-api-application"></a>Ajouter une application d’API web

Les ressources d’API web doivent être inscrites auprès de votre locataire pour pouvoir accepter et répondre aux requêtes de ressources protégées émanant des applications clientes qui présentent un jeton d’accès.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Applications**, puis **Ajouter**.
5. Entrez un nom pour l’application. Par exemple, *webapi1*.
6. Pour **inclure l’application web/l’API web** et **autoriser un flux implicite**, sélectionnez **Oui**.
7. Pour l’**URL de réponse**, entrez un point de terminaison où Azure AD B2C doit retourner les jetons demandés par votre application. Dans ce tutoriel, l'exemple s'exécute localement et écoute `https://localhost:44332`.
8. Pour l’**L’URI de l’ID d’application**, entrez l’identificateur utilisé pour votre API web. L’identificateur complet URI, y compris le domaine, est généré pour vous. Par exemple : `https://contosotenant.onmicrosoft.com/api`.
9. Cliquez sur **Créer**.
10. Sur la page des propriétés, enregistrez l'ID d'application que vous utiliserez pour configurer l'application web.

## <a name="configure-scopes"></a>Configurer des étendues

Les étendues permettent de gérer l'accès aux ressources protégées. Elles sont utilisées par l’API web pour implémenter le contrôle d’accès basé sur les étendues. Par exemple, les utilisateurs de l’API web peuvent avoir un accès en lecture et en écriture, ou les utilisateurs de l’API web peuvent avoir l’accès en lecture uniquement. Dans ce didacticiel, vous allez utiliser des étendues pour définir des autorisations d’accès en lecture et en écriture pour l’API web.

1. Sélectionnez **Applications**, puis *webapi1*.
2. Sélectionnez **Étendues publiées**.
3. Pour **Étendue**, entrez `Hello.Read`, puis pour la description, entrez `Read access to hello`.
4. Pour **Étendue**, entrez `Hello.Write`, puis pour la description, entrez `Write access to hello`.
5. Cliquez sur **Enregistrer**.

Les étendues publiées peuvent être utilisées pour accorder à une application cliente l'autorisation d'accéder à l'API web.

## <a name="grant-permissions"></a>Accorder des autorisations

Pour appeler une API web protégée à partir d'une application, vous devez accorder à cette application les autorisations d'accès à l'API. Dans le cadre du tutoriel de prérequis, vous avez créé dans Azure AD B2C une application web nommée *webapp1*. Vous allez utiliser cette application pour appeler l’API web.

1. Sélectionnez **Applications**, puis *webapp1*.
2. Sélectionnez **Accès aux API**, puis sélectionnez **Ajouter**.
3. Dans la liste déroulante **Sélectionner une API**, sélectionnez *webapi1*.
4. Dans la liste déroulante **Sélectionnez des étendues**, sélectionnez les étendues **Hello.Read** et **Hello.Write** que vous avez définies précédemment.
5. Cliquez sur **OK**.

Votre application est inscrite pour appeler l'API web protégée. Un utilisateur s'authentifie auprès d'Azure AD B2C pour utiliser l'application. L’application obtient un octroi d’autorisation d’Azure AD B2C pour accéder à l’API web protégée.

## <a name="configure-the-sample"></a>Configurer l'exemple

Maintenant que l’API web est inscrite et que les étendues sont définies, vous devez configurer l’API web pour utiliser votre locataire Azure AD B2C. Dans ce didacticiel, vous allez configurer un exemple d’API web. L’exemple d’API web est inclus dans le projet que vous avez téléchargé dans le tutoriel des prérequis.

L’exemple de solution contient deux projets :

L’exemple de solution contient les deux projets suivants :

- **TaskWebApp** : permettant de créer et de modifier une liste de tâches. L’exemple utilise le flux d’utilisateur d’**inscription ou de connexion** pour inscrire ou connecter des utilisateurs.
- **TaskService** : prend en charge les fonctionnalités de création, de lecture, de mise à jour et de suppression des listes de tâches. L’API est protégée par Azure AD B2C et appelée par TaskWebApp.

### <a name="configure-the-web-application"></a>Configurer l’application web

1. Ouvrez la solution **B2C-WebAPI-DotNet** dans Visual Studio.
2. Ouvrez **Web.config** dans le projet **TaskWebApp**.
3. Pour exécuter l’API localement, utilisez le paramètre localhost pour **api:TaskServiceUrl**. Modifiez le fichier Web.config comme suit : 

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. Configurez l’URI de l’API. Il s’agit de l’URI que l’application web utilise pour effectuer la demande d’API. Configurez également les autorisations demandées.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>Configurer l’API web

1. Ouvrez **Web.config** dans le projet **TaskService**.
2. Configurez l’API pour utiliser votre client.

    ```csharp
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. Définissez l’ID client sur l’ID d’application inscrite de votre API.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

4. Mettez à jour le paramètre de flux d’utilisateur avec le nom du flux d’utilisateur d’inscription et de connexion.

    ```csharp
    <add key="ida:SignUpSignInUserFlowId" value="B2C_1_signupsignin1" />
    ```

5. Configurez le paramètre des étendues pour qu’il corresponde à ce que vous avez créé dans le portail.

    ```csharp
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>Exécution de l'exemple

Vous devez exécuter les projets **TaskWebApp** et **TaskService**. 

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis sélectionnez **Définir les projets de démarrage...** . 
2. Sélectionnez **Plusieurs projets de démarrage**.
3. Pour les deux projets, définissez le paramètre **Action** sur **Démarrer**.
4. Cliquez sur **OK** pour enregistrer la configuration.
5. Appuyez sur la touche **F5** pour exécuter les deux applications. Chaque application s’ouvre dans son propre onglet de navigateur. `https://localhost:44316/` est l’application web.
    `https://localhost:44332/` est l’API web.

6. Dans l’application web, cliquez sur **S’inscrire/Se connecter** pour vous connecter à l’application web. Utilisez le compte que vous avez créé précédemment. 
7. Une fois connecté, cliquez sur **Liste de tâches** pour créer un élément de liste de tâches.

Lorsque vous créez un élément de liste de tâches, l’application web envoie une requête à l’API web pour le générer. L’application web que vous avez protégée appelle l’API web protégée qui se trouve dans votre locataire Azure AD B2C.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Ajouter une application d’API web
> * Configurer des étendues pour une API web
> * Accorder des autorisations à l’API web
> * Configurer l’exemple pour utiliser l’application

> [!div class="nextstepaction"]
> [Tutoriel : Ajouter des fournisseurs d’identité à vos applications dans Azure Active Directory B2C](tutorial-add-identity-providers.md)
