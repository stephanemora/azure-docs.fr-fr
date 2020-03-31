---
title: Inscrire une application SaaS | Place de marché Microsoft Azure
description: Explique comment inscrire une application SaaS à l’aide du portail Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 9c20fe34e108de95a34aabea56390e8a6f0d858f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275711"
---
# <a name="register-a-saas-application"></a>Inscrire une application SaaS

Cet article explique comment inscrire une application SaaS à l’aide du [portail Microsoft Azure](https://portal.azure.com/).  Lorsque l’inscription réussit, vous recevez un jeton de sécurité Azure Active Directory (Azure AD) que vous pouvez utiliser pour accéder aux API de traitement des commandes SaaS.  Pour plus d’informations sur Azure AD, consultez [Qu’est-ce que l’authentification ?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Flux d’authentification de service à service

Le diagramme suivant montre le flux d’abonnement d’un nouveau client et le moment où ces API sont utilisées :

![Flux de l’API de l’offre SaaS](./media/saas-offer-publish-api-flow-v1.png)

Azure n’impose aucune contrainte sur l’authentification que le service SaaS expose à ses utilisateurs finaux. Toutefois, l’authentification auprès des API de traitement des commandes SaaS est effectuée avec un jeton de sécurité Azure AD, généralement obtenu en inscrivant l’application SaaS via le portail Azure. 


## <a name="register-an-azure-ad-secured-app"></a>Inscrire une application sécurisée Azure AD

Toute application qui souhaite utiliser les fonctionnalités d’Azure AD doit d’abord être enregistrée dans un locataire Azure AD. Le processus d’enregistrement implique de fournir à Azure AD des informations sur votre application, notamment l’URL où elle est située, l’URL à laquelle envoyer une réponse après avoir authentifié un utilisateur, l’URI qui identifie l’application, et ainsi de suite.  Pour inscrire une nouvelle application à l’aide du portail Azure, procédez comme suit :

1.  Connectez-vous au [portail Azure](https://portal.azure.com/).
2.  Si votre compte vous propose plusieurs accès, cliquez sur votre compte en haut à droite et définissez votre session de portail pour le locataire Azure AD souhaité.
3.  Dans le volet de navigation gauche, cliquez sur le service **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis cliquez sur **Nouvelle inscription d’application**.

    ![Inscriptions des applications SaaS](./media/saas-offer-app-registration-v1.png)

4.  Sur la page, saisissez les informations d\'inscription de votre application :
    -   **Nom** : saisissez un nom d’application explicite
    -   **Type d’application** : 
        - Sélectionnez **Native** pour les [applications clientes](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) installées localement sur un appareil. Ce paramètre est utilisé pour les [clients natifs](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) publics OAuth.
        - Sélectionnez **Application Web / API** pour les [applications clientes](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) et les [ressources/applications API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) installées sur un serveur sécurisé. Ce paramètre est utilisé pour les [clients web](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) confidentiels OAuth et les [clients basés sur un agent utilisateur](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client) publics.
        La même application peut également exposer un client et une ressource/API.
    -   **URL de connexion** : pour les applications de type Application Web/API, indiquez l’URL de base de votre application. Par exemple, **http://localhost:31544** peut être l’URL pour une application web en cours d’exécution sur votre ordinateur local. Les utilisateurs peuvent alors utiliser cette URL pour se connecter à une application web cliente.
    -   **URI de redirection** : pour les applications natives, indiquez un URI de redirection utilisé par Azure AD pour retourner les réponses de jeton. Saisissez une valeur spécifique à votre application, par exemple **http://MyFirstAADApp** .

        ![Inscriptions des applications SaaS](./media/saas-offer-app-registration-v1-2.png)

        Pour obtenir des exemples spécifiques d’applications web ou d’applications natives, consultez les configurations guidées de démarrage rapide disponibles dans la section *Prise en main* du [Guide du développeur Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Lorsque vous avez terminé, cliquez sur **Créer**. Azure AD attribue un *ID d’application* unique à votre application, et vous êtes \'redirigé vers la page d’inscription principale de votre application\'. Selon que votre application est une application native ou web, différentes options sont disponibles afin d’ajouter des fonctionnalités supplémentaires à votre application.

>[!Note]
>Par défaut, l’application nouvellement inscrite est configurée pour autoriser uniquement les utilisateurs du même locataire à se connecter à votre application.


## <a name="using-the-azure-ad-security-token"></a>Utilisation du jeton de sécurité Azure AD

Une fois que vous avez inscrit votre application, vous pouvez demander par programmation un jeton de sécurité Azure AD.  L’éditeur doit utiliser ce jeton et effectuer une requête pour le résoudre.  Lorsque vous utilisez plusieurs API de traitement des commandes, le paramètre de requête de jeton se trouve dans l’URL lorsque l’utilisateur est redirigé vers le site web SaaS depuis Azure.  Ce jeton n’est valide que pendant une heure.  De plus, vous devez décoder de l’URL la valeur du jeton à partir du navigateur avant de l’utiliser.

Pour plus d’informations sur ces jetons, consultez [Jetons d’accès Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Obtenir un jeton basé sur l’application Azure AD

Méthode HTTP

`POST`

*URL de requête*

**https://login.microsoftonline.com/ *{tenantId}* /oauth2/token**

*Paramètre URI*

|  **Nom du paramètre**  | **Obligatoire**  | **Description**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | ID de client de l’application AAD inscrite   |
|  |  |  |


*En-tête de requête*

|  **Nom de l’en-tête**  | **Obligatoire** |  **Description**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | Type de contenu associé à la requête. La valeur par défaut est `application/x-www-form-urlencoded`.  |
|  |  |  |


*Corps de la demande*

| **Nom de la propriété**   | **Obligatoire** |  **Description**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Type d’autorisation. La valeur par défaut est `client_credentials`.                    |
|  Client_id          | True         |  Identificateur du client/de l’application associé à l’application Azure AD.                  |
|  client_secret      | True         |  Mot de passe associé à l’application Azure AD.                               |
|  Ressource           | True         |  Ressource cible pour laquelle le jeton est demandé. La valeur par défaut est `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Réponse*

|  **Nom**  | **Type**       |  **Description**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | Requête réussie   |
|  |  |  |

*TokenResponse*

Voici un exemple de token de réponse :

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```


## <a name="next-steps"></a>Étapes suivantes

Votre application sécurisée Azure AD peut maintenant utiliser [l’API de traitement des commandes SaaS version 2](./pc-saas-fulfillment-api-v2.md).
