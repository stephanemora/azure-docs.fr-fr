---
title: Didacticiel - Activer l’authentification d’application à page unique avec des comptes à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Didacticiel sur l’utilisation d’Azure Active Directory B2C pour fournir une connexion utilisateur pour une application à page unique (JavaScript).
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/02/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 47589b8cb8aa6e8d1cacaa028948242431f02c44
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45604788"
---
# <a name="tutorial-enable-single-page-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>Didacticiel - Activer l’authentification d’application à page unique avec des comptes à l’aide d’Azure Active Directory B2C

Ce didacticiel vous montre comment utiliser Azure Active Directory (Azure AD) B2C pour connecter et inscrire des utilisateurs dans une application à page unique (SPA). Azure AD B2C permet à vos applications de s’authentifier auprès de comptes des réseaux sociaux, de comptes d’entreprise et de comptes Azure Active Directory à l’aide de protocoles standards ouverts.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Inscrire un exemple d’application à page unique dans votre répertoire Azure AD B2C.
> * Créer des stratégies pour l’inscription et la connexion des utilisateurs, la modification d’un profil, et la réinitialisation d’un mot de passe.
> * Configurer l’exemple d’application pour utiliser votre répertoire Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

* Créer votre propre [répertoire Azure AD B2C](active-directory-b2c-get-started.md)
* Installer [Visual Studio 2017](https://www.visualstudio.com/downloads/) avec la charge de travail **Développement ASP.NET et web**.
* [Kit de développement logiciel (SDK) .NET Core 2.0.0](https://www.microsoft.com/net/core) ou version ultérieure
* Installez [Node.js](https://nodejs.org/en/download/)

## <a name="register-single-page-app"></a>Inscrire une application à page unique

Les applications doivent être [inscrites](../active-directory/develop/developer-glossary.md#application-registration) dans votre répertoire avant qu’elles ne puissent recevoir des [jetons d’accès](../active-directory/develop/developer-glossary.md#access-token) de la part de Azure Active Directory. L’inscription d’une application crée un [id d’application](../active-directory/develop/developer-glossary.md#application-id-client-id) pour celle-ci dans votre répertoire. 

Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre annuaire Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Sélectionnez **Azure AD B2C** dans la liste des services du portail Azure. 

2. Dans les paramètres B2C, cliquez sur **Applications**, puis sur **Ajouter**. 

    Pour inscrire l’exemple d’application web dans votre répertoire, utilisez les paramètres suivants :
    
    ![Ajouter une nouvelle application](media/active-directory-b2c-tutorials-spa/spa-registration.png)
    
    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | Mon exemple d’application à page unique | Entrez un **nom** décrivant votre application aux consommateurs. | 
    | **Inclure une application/API web** | Oui | Sélectionnez **Oui** pour une application à page unique. |
    | **Autoriser le flux implicite** | Oui | Sélectionnez **Oui** puisque l’application utilise la [connexion OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL de réponse** | `http://localhost:6420` | Les URL de réponse sont des points de terminaison auxquels Azure AD B2C retourne les jetons demandés par votre application. Dans ce didacticiel, l’exemple s’exécute localement (localhost) et écoute sur le port 6420. |
    | **Inclure le client natif** | Non  | Dans la mesure où il s’agit d’une application à page unique et non d’un client natif, sélectionnez Non. |
    
3. Cliquez sur **Créer** pour inscrire votre application.

Les applications inscrites sont indiquées dans la liste des applications du répertoire Azure AD B2C. Sélectionnez votre application à page unique dans la liste. Le volet de propriétés de l’application à page unique inscrite s’affiche.

![Propriétés de l’application à page unique](./media/active-directory-b2c-tutorials-spa/b2c-spa-properties.png)

Notez **l’ID du client d’application**. Cet ID identifie l’application de manière unique. Il est nécessaire pour configurer l’application ultérieurement dans le didacticiel.

## <a name="create-policies"></a>Création des stratégies

Une stratégie d’Azure AD B2C définit les flux de travail des utilisateurs. Par exemple, la connexion, l’inscription, le changement des mots de passe et la modification des profils sont des flux de travail courants.

### <a name="create-a-sign-up-or-sign-in-policy"></a>Création d’une stratégie d’inscription ou de connexion

Pour inscrire des utilisateurs puis les connecter à l’application web, créez une **stratégie d’inscription ou de connexion**.

1. Dans la page du portail Azure AD B2C, sélectionnez **Stratégies d’inscription ou de connexion** et cliquez sur **Ajouter**.

    Pour configurer votre stratégie, utilisez les paramètres suivants :

    ![Ajouter une stratégie d’inscription ou de connexion](media/active-directory-b2c-tutorials-web-app/add-susi-policy.png)

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SiUpIn | Entrez un **nom** pour la stratégie. Le nom de la stratégie est préfixé avec **B2C_1_**. Vous utilisez le nom complet de la stratégie **B2C_1_SiUpIn** dans l’exemple de code. | 
    | **Fournisseur d’identité** | Inscription par e-mail | Le fournisseur d’identité utilisé pour identifier l’utilisateur. |
    | **Attributs de l’inscription** | Nom d’affichage et Code Postal | Sélectionnez les attributs à collecter auprès de l'utilisateur pendant l'inscription. |
    | **Revendications de l’application** | Nom d’affichage, Code Postal, L’utilisateur est nouveau, ID d’objet de l’utilisateur | Sélectionnez les [revendications](../active-directory/develop/developer-glossary.md#claim) que vous souhaitez inclure dans le [jeton d’accès](../active-directory/develop/developer-glossary.md#access-token). |

2. Cliquez sur **Créer** pour créer votre stratégie. 

### <a name="create-a-profile-editing-policy"></a>Création d’une stratégie de modification de profil

Pour permettre aux utilisateurs de réinitialiser eux-mêmes les informations de leur profil utilisateur, créez une **stratégie de modification de profil**.

1. Dans la page du portail Azure AD B2C, sélectionnez **Stratégies de modification de profil** et cliquez sur **Ajouter**.

    Pour configurer votre stratégie, utilisez les paramètres suivants :

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SiPe | Entrez un **nom** pour la stratégie. Le nom de la stratégie est préfixé avec **B2C_1_**. Vous utilisez le nom complet de la stratégie **B2C_1_SiPe** dans l’exemple de code. | 
    | **Fournisseur d’identité** | Local Account SignIn | Le fournisseur d’identité utilisé pour identifier l’utilisateur. |
    | **Attributs de profil** | Nom d’affichage et Code Postal | Sélectionnez les attributs que les utilisateurs peuvent modifier durant la modification du profil. |
    | **Revendications de l’application** | Nom d’affichage, Code postal, ID d’objet de l’utilisateur | Sélectionnez les [revendications](../active-directory/develop/developer-glossary.md#claim) que vous souhaitez inclure dans le [jeton d’accès](../active-directory/develop/developer-glossary.md#access-token) après une modification de profil réussie. |

2. Cliquez sur **Créer** pour créer votre stratégie. 

### <a name="create-a-password-reset-policy"></a>Création d’une stratégie de réinitialisation du mot de passe

Pour activer la réinitialisation du mot de passe sur votre application, vous devez créer une **stratégie de réinitialisation de mot de passe**. Cette stratégie décrit les expériences des clients lors de la réinitialisation du mot de passe et le contenu des jetons que l’application reçoit en cas d’opération réussie.

1. Dans la page du portail Azure AD B2C, sélectionnez **Stratégies de réinitialisation de mot de passe** et cliquez sur **Ajouter**.

    Pour configurer votre stratégie, utilisez les paramètres suivants.

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SSPR | Entrez un **nom** pour la stratégie. Le nom de la stratégie est préfixé avec **B2C_1_**. Vous utilisez le nom complet de la stratégie **B2C_1_SSPR** dans l’exemple de code. | 
    | **Fournisseur d’identité** | Réinitialiser le mot de passe à l’aide d’une adresse e-mail | Il s’agit du fournisseur d’identité utilisé pour identifier l’utilisateur. |
    | **Revendications de l’application** | ID d’objet de l’utilisateur | Sélectionnez les [revendications](../active-directory/develop/developer-glossary.md#claim) que vous souhaitez inclure dans le [jeton d’accès](../active-directory/develop/developer-glossary.md#access-token) après une réinitialisation réussie du mot de passe. |

2. Cliquez sur **Créer** pour créer votre stratégie. 

## <a name="update-single-page-app-code"></a>Mettre à jour le code de l’application à page unique

Maintenant que vous avez une application inscrite et des stratégies créées, vous devez configurer votre application pour utiliser votre répertoire Azure AD B2C. Dans ce didacticiel, vous configurez un exemple d’application SPA JavaScript que vous pouvez télécharger à partir de GitHub. 

[Téléchargez un fichier zip ](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clonez l’exemple d’application web à partir de GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```
L’exemple d’application montre comment une application à page unique peut utiliser Azure AD B2C pour l’inscription et la connexion des utilisateurs, et appeler une API web protégée. Vous devez modifier l’application pour utiliser l’inscription de l’application dans votre répertoire et configurer les stratégies que vous avez créées. 

Pour modifier les paramètres d’application :

1. Ouvrez le fichier `index.html` dans l’exemple d’application monopage Node.js.
2. Configurez l’exemple avec les informations d’inscription des répertoires Azure AD B2C. Modifiez les lignes de code suivantes (veillez à remplacer les valeurs par les noms de votre répertoire et de vos API) :

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://fabrikamb2c.b2clogin.com/tfp/fabrikamb2c.onmicrosoft.com/B2C_1_<Sign-up or sign-in policy name>",
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://fabrikamb2chello.azurewebsites.net/hello',
    };
    ```

    Le nom de stratégie utilisé dans ce didacticiel est **B2C_1_SiUpIn**. Si vous utilisez un autre nom de stratégie, indiquez-le dans la valeur `authority`.

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

4. Utilisez un navigateur pour accéder à l’adresse `http://localhost:6420` pour afficher l’application.

L’exemple d’application prend en charge l’inscription et la connexion des utilisateurs, la modification d’un profil, et la réinitialisation d’un mot de passe. Ce didacticiel met en évidence la manière dont un utilisateur s’inscrit pour utiliser l’application à l’aide d’une adresse e-mail. Vous pouvez étudier les autres scénarios vous-même.

### <a name="sign-up-using-an-email-address"></a>S’inscrire au moyen d’une adresse e-mail

1. Cliquez sur **Connexion** pour vous inscrire en tant qu’utilisateur de l’application SPA. Cette méthode utilise la stratégie **B2C_1_SiUpIn** que vous avez définie à l’étape précédente.

2. Azure AD B2C présente une page de connexion avec un lien pour l’abonnement. Si vous ne possédez pas encore de compte, cliquez sur le lien **Inscrivez-vous maintenant**. 

3. Le flux de travail d’abonnement présente une page pour collecter et vérifier l’identité de l’utilisateur à l’aide d’une adresse e-mail. Le flux de travail d’abonnement collecte également le mot de passe et les attributs demandés définis dans la stratégie.

    Utilisez une adresse e-mail valide et validez à l’aide d’un code de vérification. Définissez un mot de passe. Entrez des valeurs pour les attributs requis. 

    ![Flux de travail d’abonnement](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Cliquez sur **Créer** pour créer un compte local dans le répertoire Azure AD B2C.

Maintenant l’utilisateur peut utiliser son adresse e-mail pour se connecter et utiliser l’application SPA.

> [!NOTE]
> Une fois connecté, l’application affiche une erreur d’autorisations insuffisantes. Vous recevez cette erreur car vous tentez d’accéder à une ressource du répertoire de démonstration. Étant donné que votre jeton d’accès est valide uniquement pour votre répertoire Azure AD, l’appel d’API n’est pas autorisé. Passez au didacticiel suivant pour créer une API web protégée pour votre répertoire. 

## <a name="clean-up-resources"></a>Supprimer les ressources

Vous pouvez utiliser votre répertoire Azure AD B2C si vous envisagez d’effectuer d’autres didacticiels Azure AD B2C. Si vous n’en avez plus besoin, vous pouvez [supprimer votre répertoire Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à créer un répertoire Azure AD B2C, créer des stratégies et mettre à jour l’exemple d’application à page unique pour utiliser votre répertoire Azure AD B2C. Passez au didacticiel suivant pour apprendre à inscrire, configurer et appeler une API web protégée à partir d’une application de bureau.

> [!div class="nextstepaction"]
> 
