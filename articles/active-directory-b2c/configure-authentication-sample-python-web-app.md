---
title: Configurer l’authentification dans un exemple d’application web Python à l’aide d’Azure Active Directory B2C
description: Utilisation d’Azure Active Directory B2C pour connecter et inscrire des utilisateurs dans une application web Python.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/11/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 18c470101062046ec83cb1d384184e93df724331
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122641329"
---
# <a name="configure-authentication-in-a-sample-python-web-application-using-azure-active-directory-b2c"></a>Configurer l’authentification dans un exemple d’application web Python à l’aide d’Azure Active Directory B2C

Cet article utilise un exemple d’application web Python pour illustrer comment ajouter une authentification Azure Active Directory B2C (Azure AD B2C) à vos applications web.


## <a name="overview"></a>Vue d’ensemble

OpenID Connect (OIDC) est un protocole d’authentification basé sur OAuth 2.0 que vous pouvez utiliser pour connecter de façon sécurisée un utilisateur à une application. Cet exemple d’application web utilise [MSAL pour Python](https://github.com/AzureAD/microsoft-authentication-library-for-python). MSAL pour Python simplifie l’ajout de la prise en charge de l’authentification et des autorisations aux applications web Python. 

Le flux de connexion implique les étapes suivantes :

1. L’utilisateur accède à l’application web et sélectionne **Connexion**. 
1. L’application initie une demande d’authentification et redirige l’utilisateur vers Azure AD B2C.
1. L’utilisateur [s’inscrit ou se connecte](add-sign-up-and-sign-in-policy.md), [réinitialise le mot de passe](add-password-reset-policy.md) ou se connecte à l’aide d’un [compte social](add-identity-provider.md).
1. Une fois la connexion établie, Azure AD B2C renvoie un jeton d’ID à l’application.
1. L’application échange le code d’autorisation avec un jeton d’ID. L’application valide le jeton d’ID, lit les revendications et renvoie une page sécurisée à l’utilisateur.


### <a name="sign-out"></a>Se déconnecter

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)] 

## <a name="prerequisites"></a>Prérequis

Un ordinateur exécutant l’un des éléments suivants : 

* [Visual Studio Code](https://code.visualstudio.com/) ou un autre éditeur de code
* [Python](https://nodejs.org/en/download/) 2.7+ ou 3+ 

## <a name="step-1-configure-your-user-flow"></a>Étape 1 : Configurer votre flux d’utilisateurs

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-a-web-application"></a>Étape 2 : Inscrire une application web

Pour permettre à votre application de se connecter avec Azure AD B2C, inscrivez votre application dans le répertoire Azure AD B2C. L’inscription de votre application établit une relation de confiance entre l’application et Azure AD B2C.  

Pendant l’inscription de l’application, vous spécifiez l’**URI de redirection**. L’URI de redirection est le point de terminaison vers lequel l’utilisateur est redirigé par Azure AD B2C après s’être authentifié avec Azure AD B2C. Le processus d’inscription de l’application génère un **ID d’application**, également appelé **ID client**, qui identifie votre application de façon unique. Une fois votre application inscrite, Azure AD B2C utilise à la fois l’ID d’application et l’URI de redirection pour créer des demandes d’authentification. 

### <a name="21-register-the-app"></a>2.1 Enregistrer l’application

Effectuez les étapes suivantes pour créer l’inscription d’application :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Entrez un **Nom** pour l’application. Par exemple, *webapp1*.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un fournisseur d’identité ou annuaire organisationnel (pour authentifier les utilisateurs avec des flux d’utilisateurs)** . 
1. Sous **URI de redirection**, sélectionnez **Web**, puis entrez `http://localhost:5000/getAToken` dans la zone de texte de l’URL.
1. Sous **Autorisations**, cochez la case **Accorder le consentement administrateur aux autorisations openid et offline access**.
1. Sélectionnez **Inscription**.
1. Sélectionnez **Vue d’ensemble**.
1. Enregistrez **l’ID d’application (client)** que vous utiliserez ultérieurement pour configurer l'application web.

    ![Obtenir votre ID d’application](./media/configure-authentication-sample-python-web-app/get-azure-ad-b2c-app-id.png)  


### <a name="22-create-a-web-app-client-secret"></a>2.2 Créer une clé secrète client d’application web

[!INCLUDE [active-directory-b2c-app-integration-client-secret](../../includes/active-directory-b2c-app-integration-client-secret.md)]

## <a name="step-3-get-the-web-app-sample"></a>Étape 3 : Obtenir l’exemple d’application web

[Téléchargez un fichier zip](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip) ou clonez l’exemple d’application web à partir de GitHub. 

```bash
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

Extrayez l’exemple de fichier dans un dossier où la longueur totale du chemin d’accès est inférieure à 260 caractères.

## <a name="step-4-configure-the-sample-application"></a>Étape 4 : Configurer l’exemple d’application

Dans le répertoire racine du projet :

1. Renommez le fichier *app_config.py* en *app_config.py.OLD*.
1. Renommez *app_config_b2c.py* en *app_config.py*. 

Ouvrez le fichier *app_config.py*. Ce fichier contient des informations sur votre fournisseur d’identité Azure AD B2C. Mettez à jour les propriétés suivantes des paramètres de l’application : 

|Clé  |Valeur  |
|---------|---------|
|`b2c_tenant`| La première partie de votre [nom de locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C. Par exemple : `contoso`.|
|`CLIENT_ID`| L’ID d’application de l’API web de l’[étape 2.1](#21-register-the-app).|
|`CLIENT_SECRET`| La clé secrète client que vous avez créée à l’[étape 2.2](#22-create-a-web-app-client-secret). Pour une sécurité accrue, stockez-la plutôt dans une variable d’environnement comme recommandé dans les commentaires. |
|`*_user_flow`|Le flux d’utilisateurs ou la stratégie personnalisée que vous avez créés à l’[étape 1](#step-1-configure-your-user-flow).|

Votre fichier config final doit ressembler au code Python suivant :

```python
import os

b2c_tenant = "contoso"
signupsignin_user_flow = "B2C_1_signupsignin"
editprofile_user_flow = "B2C_1_profileediting"
resetpassword_user_flow = "B2C_1_passwordreset"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "xxxxxxxxxxxxxxxxxxxxxxxx" # Placeholder - for use ONLY during testing.
```

> [!WARNING]
> Comme indiqué dans les commentaires de l’extrait de code, nous vous recommandons de **ne pas stocker les secrets en texte clair** dans le code de votre application. La variable codée en dur est utilisée dans l’exemple de code *uniquement par souci pratique*. Utilisez plutôt une variable d’environnement ou un magasin de secrets comme Azure Key Vault.


## <a name="step-5-run-the-sample-application"></a>Étape 5 : Exécuter l’exemple d’application

1. Dans votre console ou terminal, basculez vers le répertoire contenant l’exemple. Par exemple :

    ```console
    cd ms-identity-python-webapp
    ```
1. Exécutez les commandes suivantes pour installer les packages requis à partir de PyPi et exécuter l’application web sur votre ordinateur local :

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    La fenêtre de console affiche le numéro de port de l’application s’exécutant localement :

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on http://localhost:5000/ (Press CTRL+C to quit)
    ```

 
1. Accédez à http://localhost:5000 pour voir l’application web en cours d’exécution sur votre machine locale. 

1. Sélectionnez **Connexion**.

    ![Capture d’écran montrant la connexion avec Azure AD B2C.](./media/configure-authentication-sample-python-web-app/web-app-sign-in.png)


1. Terminez le processus d’inscription ou de connexion.

1. Une fois l’authentification réussie, votre nom d’affichage s’affiche. 

    ![Capture d’écran illustrant la revendication de nom complet du jeton de l’application web.](./media/configure-authentication-sample-python-web-app/web-app-token-claims.png)


## <a name="call-to-a-web-api"></a>Appel à une API web

Pour permettre à votre application de se connecter avec Azure AD B2C et d’appeler une API web, vous devez inscrire deux applications dans le répertoire d’Azure AD B2C.  

- L’inscription de l’**application web** (Python) que vous avez déjà créée à l’[étape 2](#step-2-register-a-web-application). L’inscription de l’application lui permet de se connecter avec Azure AD B2C.  Le processus d’inscription de l’application génère un *ID d’application*, également appelé *ID client*, qui identifie votre application de façon unique. Par exemple, l’**ID d’application : 1**.

- L’inscription de **l’API web** permet à votre application d’appeler une API web protégée. Elle expose les autorisations de l’API web (étendues). Le processus d’inscription de l’application génère un *ID d’application* qui identifie votre API web de façon unique. Par exemple, l’**ID d’application : 2**. Accordez à votre application (ID d’application : 1) des autorisations sur les étendues de l’API web (ID d’application : 2).  

Les diagrammes suivants décrivent les inscriptions et l’architecture des applications.

![Le diagramme décrit une application web avec une API web, les inscriptions et les jetons.](./media/configure-authentication-sample-python-web-app/web-app-with-api-architecture.png) 

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="register-the-web-api-application"></a>Inscrire l’application API web

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="configure-scopes"></a>Configurer des étendues

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="grant-the-web-app-permissions"></a>Accorder les autorisations de l’application web

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

### <a name="configure-your-web-api"></a>Configurer votre application API web

Cet exemple montre comment acquérir un jeton d’accès avec les étendues appropriées que l’application web peut utiliser pour une API web. Pour appeler une API web à partir du code, utilisez une API web existante ou créez-en une nouvelle. Pour plus d’informations, consultez [Activer l’authentification dans votre propre API web à l’aide d’Azure AD B2C](enable-authentication-web-api.md).

### <a name="configure-the-sample-application-with-the-web-api"></a>Configurer l’exemple d’application avec l’API web

Ouvrez le fichier *app_config.py*. Ce fichier contient des informations sur votre fournisseur d’identité Azure AD B2C. Mettez à jour les propriétés suivantes des paramètres de l’application : 

|Clé  |Valeur  |
|---------|---------|
|`ENDPOINT`| L’URI de votre API web. Par exemple : https://localhost:44332/hello.|
|`SCOPE`| Les [étendues](#configure-scopes) de l’API web que vous avez créées.|

Votre fichier config final doit ressembler au code Python suivant :

```python
import os

b2c_tenant = "contoso"
signupsignin_user_flow = "B2C_1_signupsignin"
editprofile_user_flow = "B2C_1_profileediting"
resetpassword_user_flow = "B2C_1_passwordreset"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "xxxxxxxxxxxxxxxxxxxxxxxx" # Placeholder - for use ONLY during testing.

### More code here

# This is the API resource endpoint
ENDPOINT = 'https://localhost:44332' 


SCOPE = ["https://contoso.onmicrosoft.com/api/demo.read", "https://contoso.onmicrosoft.com/api/demo.write"] 
```

### <a name="run-the-sample-application"></a>Exécuter l’exemple d’application

1. Dans votre console ou terminal, basculez vers le répertoire contenant l’exemple. 
1. Arrêtez l’application et réexécutez-la.
1. Sélectionnez **Appeler l’API Microsoft Graph (UWP)** .

    ![Capture d’écran montrant comment appeler une API web.](./media/configure-authentication-sample-python-web-app/call-web-api.png)

## <a name="deploy-your-application"></a>Déployer votre application 

Dans une application de production, l’URI de redirection de l’inscription de l’application est généralement un point de terminaison accessible publiquement dans lequel votre application s’exécute, comme `https://contoso.com/getAToken`. 

Vous pouvez ajouter des URI de redirection à vos applications inscrites à tout moment et les modifier. Les restrictions suivantes s’appliquent aux URI de redirection :

* L’URL de réponse doit commencer par le schéma `https`.
* L’URL de réponse respecte la casse. Sa casse doit correspondre à celle du chemin d’URL de votre application en cours d’exécution. 

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [Configurer les options d’authentification dans une application web Python avec Azure Active Directory B2C](enable-authentication-python-web-app-options.md).