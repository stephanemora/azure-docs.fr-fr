---
title: Didacticiel - Accorder l’accès à une API web Node.js depuis une application de bureau à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Didacticiel sur l’utilisation d’Active Directory B2C pour protéger une API web Node.js et l’appeler depuis une application de bureau .NET.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/01/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 01c13b214d40fba278ce788047e2b158adc20287
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711594"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Didacticiel - Accorder l’accès à une API web Node.js depuis une application de bureau à l’aide d’Azure Active Directory B2C

Ce didacticiel vous montre comment appeler une ressource d’API web Node.js protégée par Azure Active Directory (Azure AD) B2C depuis une application de bureau Windows Presentation Foundation (WPF).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * inscrire une API web dans votre client Azure AD B2C ;
> * définir et configurer les étendues d’une API web ;
> * accorder à une application des autorisations d’accès à l’API web ;
> * mettre à jour l’exemple de code pour utiliser Azure AD B2C afin de protéger une API web.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

* Suivez le [didacticiel Activer l’authentification d’application de bureau avec des comptes à l’aide d’Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).
* Installez [Visual Studio 2017](https://www.visualstudio.com/downloads/) avec les charges de travail **Développement Bureau .NET** et **Développement web et ASP.NET**.
* Installez [Node.js](https://nodejs.org/en/download/)

## <a name="register-web-api"></a>Inscrire une API web

Les ressources d’API web doivent être inscrites dans votre client pour pouvoir accepter les [demandes de ressources protégées](../active-directory/develop/active-directory-dev-glossary.md#resource-server) des [applications clientes](../active-directory/develop/active-directory-dev-glossary.md#client-application) qui présentent un [jeton d’accès](../active-directory/develop/active-directory-dev-glossary.md#access-token) d’Azure Active Directory et y répondre. L’inscription établit [l’objet principal de service et d’application](../active-directory/develop/active-directory-dev-glossary.md#application-object) dans votre client. 

Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre client Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Sélectionnez **Azure AD B2C** dans la liste des services du portail Azure.

2. Dans les paramètres B2C, cliquez sur **Applications**, puis sur **Ajouter**.

    Pour inscrire l’exemple d’API web dans votre client, utilisez les paramètres ci-dessous.
    
    ![Ajouter une nouvelle API](media/active-directory-b2c-tutorials-desktop-app-webapi/web-api-registration.png)
    
    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | Mon exemple d’API web Node.js | Entrez un **nom** qui décrit votre API web pour les développeurs. |
    | **Inclure une application/API web** | OUI | Sélectionnez **Oui** pour une API web. |
    | **Autoriser le flux implicite** | OUI | Sélectionnez **Oui** puisque l’API utilise la [connexion OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL de réponse** | `http://localhost:5000` | Les URL de réponse sont des points de terminaison auxquels Azure AD B2C retourne les jetons demandés par votre API. Dans ce didacticiel, l’exemple d’API web s’exécute localement (localhost) et écoute sur le port 5000. |
    | **URI ID d’application** | demoapi | L’URI identifie de façon unique l’API dans le client. Vous pouvez ainsi inscrire plusieurs API par client. Les [étendues](../active-directory/develop/active-directory-dev-glossary.md#scopes) régissent l’accès à la ressource d’API protégée et sont définies par l’URI ID d’application. |
    | **Client natif** | Non  | Dans la mesure où il s’agit d’une API web et pas d’un client natif, sélectionnez Non. |
    
3. Cliquez sur **Créer** pour inscrire votre API.

Les API inscrites sont indiquées dans la liste des applications du client Azure AD B2C. Sélectionnez votre API web dans la liste. Le volet de propriétés de l’API web s’affiche.

![Propriétés de l’API Web](./media/active-directory-b2c-tutorials-web-api/b2c-web-api-properties.png)

Notez **l’ID du client d’application**. Cet ID identifie l’API de manière unique. Il est nécessaire pour configurer l’API ultérieurement dans le didacticiel.

L’inscription de l’API web avec Azure AD B2C définit une relation d’approbation. Étant donné que l’API est inscrite avec B2C, elle peut désormais approuver les jetons d’accès B2C reçus des autres applications.

## <a name="define-and-configure-scopes"></a>Définir et configurer des étendues

Les [étendues](../active-directory/develop/active-directory-dev-glossary.md#scopes) permettent de gérer l’accès aux ressources protégées. Elles sont utilisées par l’API web pour implémenter le contrôle d’accès basé sur les étendues. Par exemple, certains utilisateurs peuvent bénéficier d’un accès en lecture et en écriture tandis que d’autres peuvent disposer d’autorisations d’accès en lecture seule. Dans ce didacticiel, vous allez définir des autorisations d’accès en lecture et en écriture pour l’API web.

### <a name="define-scopes-for-the-web-api"></a>Définir les étendues de l’API web

Les API inscrites sont indiquées dans la liste des applications du client Azure AD B2C. Sélectionnez votre API web dans la liste. Le volet de propriétés de l’API web s’affiche.

Cliquez sur **Étendues publiées (préversion)**.

Pour configurer les étendues de l’API, ajoutez les entrées ci-dessous. 

![étendues définies dans l’api web](media/active-directory-b2c-tutorials-web-api/scopes-defined-in-web-api.png)

| Paramètre      | Valeur suggérée  | Description                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Portée** | demo.read | Accès en lecture à l’API de démonstration|

Cliquez sur **Enregistrer**.

Les étendues publiées peuvent servir à accorder à une application cliente une autorisation d’accès à l’API web.

### <a name="grant-app-permissions-to-web-api"></a>Accorder à une application des autorisations d’accès à l’API web

Pour appeler une API web protégée à partir d’une application, vous devez accorder à cette application des autorisations d’accès à l’API. Dans ce didacticiel, utilisez l’application de bureau créée dans le [didacticiel Activer l’authentification d’application de bureau avec des comptes à l’aide d’Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).

1. Dans le portail Azure, sélectionnez **Azure AD B2C** dans la liste des services, puis cliquez sur **Applications** pour afficher la liste des applications inscrites.

2. Dans la liste des applications, sélectionnez **Mon exemple d’application WPF** et cliquez sur **Accès d’API (préversion)**, puis sur **Ajouter**.

3. Dans la liste déroulante **Sélectionner une API**, sélectionnez votre API web inscrite **Mon exemple d’API web Node.js**.

4. Dans la liste déroulante **Sélectionnez des étendues**, sélectionnez les étendues que vous avez définies au cours de l’inscription de l’API web.

    ![sélection d’étendues pour l’application](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. Cliquez sur **OK**.

L’application **Mon exemple d’application WPF** est inscrite pour appeler **Mon exemple d’API web Node.js** protégée. Un utilisateur [s’authentifie](../active-directory/develop/active-directory-dev-glossary.md#authentication) auprès d’Azure AD B2C pour utiliser l’application de bureau WPF. L’application de bureau obtient un [octroi d’autorisation](../active-directory/develop/active-directory-dev-glossary.md#authorization-grant) d’Azure AD B2C pour accéder à l’API web protégée.

## <a name="update-web-api-code"></a>Mettre à jour le code de l’API web

Maintenant que l’API web est inscrite et que les étendues sont définies, vous devez configurer le code de l’API web pour utiliser votre client Azure AD B2C. Dans ce didacticiel, vous configurez un exemple d’application web Node.js que vous pouvez télécharger à partir de GitHub. 

[Téléchargez un fichier zip ](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) ou clonez l’exemple d’application web à partir de GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
L’exemple d’API web Node.js utilise la bibliothèque Passport.js pour activer Azure AD B2C afin de protéger les appels vers l’API. 

### <a name="configure-the-web-api"></a>Configurer l’API web

1. Ouvrez le fichier `index.html` dans l’exemple d’API web Node.js.
2. Configurez l’exemple avec les informations d’inscription des locataires Azure AD B2C. Modifiez les lignes de code suivantes :

```nodejs
var tenantID = "<your-tenant-name>.onmicrosoft.com";
var clientID = "<Application ID for your Node.js Web API>";
var policyName = "B2C_1_SiUpIn";  // Sign-in / sign-up policy name
```

### <a name="configure-the-desktop-app"></a>Configurer l’application de bureau

1. Ouvrez la solution `active-directory-b2c-wpf` du [didacticiel Activer l’authentification d’application de bureau avec des comptes à l’aide d’Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md) dans Visual Studio.

## <a name="run-the-sample"></a>Exécution de l'exemple

Exécutez l’API web Node.js :

1. Lancez une invite de commande Node.js.
2. Accédez au répertoire contenant l’exemple Node.js. Par exemple `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Exécutez les commandes suivantes :
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```
Exécutez l’application de bureau :

1. Appuyez sur **F5** pour exécuter l’application de bureau.
2. Connectez-vous à l’aide de l’adresse e-mail et du mot de passe utilisés dans le [didacticiel Activer l’authentification d’application de bureau avec des comptes à l’aide d’Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).
3. Cliquez sur le bouton **Appel d’API**. 

L’application de bureau envoie une requête à l’API web et obtient une réponse avec le nom d’affichage de l’utilisateur connecté. L’application de bureau que vous avez protégée appelle l’API web protégée dans votre locataire Azure AD B2C.

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous pouvez utiliser votre client Azure AD B2C si vous envisagez d’effectuer d’autres didacticiels Azure AD B2C. Si vous n’en avez plus besoin, vous pouvez [supprimer votre client Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a expliqué en détail comment protéger une API Web ASP.NET en inscrivant et en définissant des étendues dans Azure AD B2C. Apprenez-en davantage en parcourant les exemples de code Azure AD B2C disponibles.

> [!div class="nextstepaction"]
> [Exemples de code Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
