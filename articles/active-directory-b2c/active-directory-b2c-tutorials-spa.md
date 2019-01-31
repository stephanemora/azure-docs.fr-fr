---
title: Didacticiel - Activer l’authentification d’application à page unique avec des comptes à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Didacticiel sur l’utilisation d’Azure Active Directory B2C pour fournir une connexion utilisateur pour une application à page unique (JavaScript).
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 0632d97c85e4baf837329a2b573f4abe5c15bf26
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195703"
---
# <a name="tutorial-enable-single-page-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>Tutoriel : Activer l’authentification d’application monopage avec des comptes à l’aide d’Azure Active Directory B2C

Ce didacticiel vous montre comment utiliser Azure Active Directory (Azure AD) B2C pour connecter et inscrire des utilisateurs dans une application à page unique (SPA). Azure AD B2C permet à vos applications de s’authentifier auprès de comptes des réseaux sociaux, de comptes d’entreprise et de comptes Azure Active Directory à l’aide de protocoles standards ouverts.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Inscrire un exemple d’application à page unique dans votre répertoire Azure AD B2C.
> * Créer des flux d’utilisateurs pour l’inscription et la connexion des utilisateurs, la modification d’un profil et la réinitialisation d’un mot de passe.
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
    | **Nom** | Mon exemple d’application à page unique | Entrez un **nom** décrivant votre application aux consommateurs. | 
    | **Inclure une application/API web** | Oui | Sélectionnez **Oui** pour une application à page unique. |
    | **Autoriser le flux implicite** | Oui | Sélectionnez **Oui** puisque l’application utilise la [connexion OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL de réponse** | `http://localhost:6420` | Les URL de réponse sont des points de terminaison auxquels Azure AD B2C retourne les jetons demandés par votre application. Dans ce didacticiel, l’exemple s’exécute localement (localhost) et écoute sur le port 6420. |
    | **Inclure le client natif** | Non  | Dans la mesure où il s’agit d’une application à page unique et non d’un client natif, sélectionnez Non. |
    
3. Cliquez sur **Créer** pour inscrire votre application.

Les applications inscrites sont indiquées dans la liste des applications du répertoire Azure AD B2C. Sélectionnez votre application à page unique dans la liste. Le volet de propriétés de l’application à page unique inscrite s’affiche.

![Propriétés de l’application à page unique](./media/active-directory-b2c-tutorials-spa/b2c-spa-properties.png)

Notez **l’ID du client d’application**. Cet ID identifie l’application de manière unique. Il est nécessaire pour configurer l’application ultérieurement dans le didacticiel.

## <a name="create-user-flows"></a>Créer des flux d’utilisateur

Un flux d’utilisateur Azure AD B2C définit l’expérience utilisateur pour une tâche d’identité. Par exemple, la connexion, l’inscription, le changement de mot de passe et la modification de profil sont des flux d’utilisateur courants.

### <a name="create-a-sign-up-or-sign-in-user-flow"></a>Créer un flux d’utilisateur d’inscription ou de connexion

Pour inscrire des utilisateurs, puis les connecter à l’application web, créez un **flux d’utilisateur d’inscription ou de connexion**.

1. Dans la page du portail Azure AD B2C, sélectionnez **flux d’utilisateur** et cliquez sur **Nouveau flux d’utilisateur**.
2. Sous l’onglet **Recommandé**, cliquez sur **Inscription et connexion**.

    Pour configurer votre flux d’utilisateur, utilisez les paramètres suivants :

    ![Ajouter un flux d’utilisateur d’inscription ou de connexion](media/active-directory-b2c-tutorials-spa/add-susi-user-flow.png)

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nom** | SiUpIn | Entrez un **Nom** pour le flux d’utilisateur. Le nom du flux d’utilisateur est préfixé avec **B2C_1_**. Vous utilisez le nom complet du flux d’utilisateur **B2C_1_SiUpIn** dans l’exemple de code. | 
    | **Fournisseurs d’identité** | Inscription par e-mail | Le fournisseur d’identité utilisé pour identifier l’utilisateur. |

3. Sous **Attributs utilisateur et revendications**, cliquez sur **Afficher plus** et sélectionnez les paramètres suivants :

    ![Ajouter un flux d’utilisateur d’inscription ou de connexion](media/active-directory-b2c-tutorials-spa/add-attributes-and-claims.png)

    | Colonne      | Valeur suggérée  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Collecter l’attribut** | Nom d’affichage et Code Postal | Sélectionnez les attributs à collecter auprès de l'utilisateur pendant l'inscription. |
    | **Revendication de retour** | Nom d’affichage, Code Postal, L’utilisateur est nouveau, ID d’objet de l’utilisateur | Sélectionnez les [revendications](../active-directory/develop/developer-glossary.md#claim) que vous souhaitez inclure dans le [jeton d’accès](../active-directory/develop/developer-glossary.md#access-token). |

4. Cliquez sur **OK**.
5. Cliquez sur **Créer** pour créer votre flux d’utilisateur. 

### <a name="create-a-profile-editing-user-flow"></a>Créer un flux d’utilisateur de modification de profil

Pour permettre aux utilisateurs de réinitialiser eux-mêmes les informations de leur profil utilisateur, créez un **flux d’utilisateur de modification de profil**.

1. Dans la page du portail Azure AD B2C, sélectionnez **flux d’utilisateur** et cliquez sur **Nouveau flux d’utilisateur**.
2. Sous l’onglet **Recommandé**, cliquez sur **Modification de profil**.

    Pour configurer votre flux d’utilisateur, utilisez les paramètres suivants :

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nom** | SiPe | Entrez un **Nom** pour le flux d’utilisateur. Le nom du flux d’utilisateur est préfixé avec **B2C_1_**. Vous utilisez le nom complet du flux d’utilisateur **B2C_1_SiPe** dans l’exemple de code. | 
    | **Fournisseurs d’identité** | Local Account SignIn | Le fournisseur d’identité utilisé pour identifier l’utilisateur. |

3.  Sous **Attributs utilisateur**, cliquez sur **Afficher plus** et sélectionnez les paramètres suivants :

    | Colonne      | Valeur suggérée  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Collecter l’attribut** | Nom d’affichage et Code Postal | Sélectionnez les attributs que les utilisateurs peuvent modifier durant la modification du profil. |
    | **Revendication de retour** | Nom d’affichage, Code postal, ID d’objet de l’utilisateur | Sélectionnez les [revendications](../active-directory/develop/developer-glossary.md#claim) que vous souhaitez inclure dans le [jeton d’accès](../active-directory/develop/developer-glossary.md#access-token) après une modification de profil réussie. |

4. Cliquez sur **OK**.
5. Cliquez sur **Créer** pour créer votre flux d’utilisateur. 

### <a name="create-a-password-reset-user-flow"></a>Créer un flux d’utilisateur de réinitialisation du mot de passe

Pour activer la réinitialisation du mot de passe sur votre application, vous devez créer un **flux d’utilisateur de réinitialisation de mot de passe**. Ce flux d’utilisateur décrit les expériences des clients lors de la réinitialisation du mot de passe, et le contenu des jetons que l’application reçoit en cas d’opération réussie.

1. Dans la page du portail Azure AD B2C, sélectionnez **Flux d’utilisateurs** et cliquez sur **Nouveau flux d’utilisateur**.
2. Sous l’onglet **Recommandé**, cliquez sur **Réinitialisation du mot de passe**.

    Pour configurer votre flux d’utilisateur, utilisez les paramètres suivants :

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nom** | SSPR | Entrez un **Nom** pour le flux d’utilisateur. Le nom du flux d’utilisateur est préfixé avec **B2C_1_**. Vous utilisez le nom complet du flux d’utilisateur **B2C_1_SSPR** dans l’exemple de code. | 
    | **Fournisseurs d’identité** | Réinitialiser le mot de passe à l’aide d’une adresse e-mail | Il s’agit du fournisseur d’identité utilisé pour identifier l’utilisateur. |

3. Sous **Revendications d’application**, cliquez sur **Afficher plus** et sélectionnez les paramètres suivants :

    | Colonne      | Valeur suggérée  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Revendication de retour** | ID d’objet de l’utilisateur | Sélectionnez les [revendications](../active-directory/develop/developer-glossary.md#claim) que vous souhaitez inclure dans le [jeton d’accès](../active-directory/develop/developer-glossary.md#access-token) après une réinitialisation réussie du mot de passe. |

4. Cliquez sur **OK**.
5. Cliquez sur **Créer** pour créer votre flux d’utilisateur. 

## <a name="update-single-page-app-code"></a>Mettre à jour le code de l’application à page unique

Maintenant que vous disposez d’une application inscrite et de flux d’utilisateur créés, vous devez configurer votre application pour utiliser votre annuaire Azure AD B2C. Dans ce didacticiel, vous configurez un exemple d’application SPA JavaScript que vous pouvez télécharger à partir de GitHub. 

[Téléchargez un fichier zip ](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clonez l’exemple d’application web à partir de GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```
L’exemple d’application montre comment une application à page unique peut utiliser Azure AD B2C pour l’inscription et la connexion des utilisateurs, et appeler une API web protégée. Vous devez modifier l’application pour utiliser l’inscription de l’application dans votre annuaire et configurer les flux d’utilisateur que vous avez créés. 

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

    Le nom de flux d’utilisateur utilisé dans ce tutoriel est **B2C_1_SiUpIn**. Si vous utilisez un autre nom de flux d’utilisateur, indiquez-le dans la valeur `authority`.

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

1. Cliquez sur **Connexion** pour vous inscrire en tant qu’utilisateur de l’application SPA. Cette méthode utilise le flux d’utilisateur **B2C_1_SiUpIn** que vous avez défini à une étape précédente.

2. Azure AD B2C présente une page de connexion avec un lien pour l’abonnement. Si vous ne possédez pas encore de compte, cliquez sur le lien **Inscrivez-vous maintenant**. 

3. Le flux de travail d’abonnement présente une page pour collecter et vérifier l’identité de l’utilisateur à l’aide d’une adresse e-mail. Le flux de travail d’inscription collecte également le mot de passe et les attributs demandés, qui sont définis dans le flux d’utilisateur.

    Utilisez une adresse e-mail valide et validez à l’aide d’un code de vérification. Définissez un mot de passe. Entrez des valeurs pour les attributs requis. 

    ![Flux de travail d’abonnement](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Cliquez sur **Créer** pour créer un compte local dans le répertoire Azure AD B2C.

Maintenant l’utilisateur peut utiliser son adresse e-mail pour se connecter et utiliser l’application SPA.

> [!NOTE]
> Une fois connecté, l’application affiche une erreur d’autorisations insuffisantes. Vous recevez cette erreur car vous tentez d’accéder à une ressource du répertoire de démonstration. Étant donné que votre jeton d’accès est valide uniquement pour votre répertoire Azure AD, l’appel d’API n’est pas autorisé. Passez au didacticiel suivant pour créer une API web protégée pour votre répertoire. 

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous pouvez utiliser votre répertoire Azure AD B2C si vous envisagez d’effectuer d’autres didacticiels Azure AD B2C. Si vous n’en avez plus besoin, vous pouvez [supprimer votre répertoire Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer un annuaire Azure AD B2C, à créer des flux d’utilisateur et à mettre à jour l’exemple d’application monopage pour utiliser votre annuaire Azure AD B2C. Passez au didacticiel suivant pour apprendre à inscrire, configurer et appeler une API web protégée à partir d’une application de bureau.

> [!div class="nextstepaction"]
> [Exemples de code Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
