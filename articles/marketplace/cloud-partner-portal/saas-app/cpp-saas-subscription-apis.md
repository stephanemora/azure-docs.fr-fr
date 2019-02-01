---
title: 'Vente SaaS via Azure : interfaces de programmation d’applications | Microsoft Docs'
description: Explique comment créer une offre SaaS via les API de la place de marché.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: a778723093b226ee0e681c2a95ce4db597a310e5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198698"
---
# <a name="saas-sell-through-azure---apis"></a>Vente SaaS via Azure : interfaces de programmation d’applications

Cet article explique comment créer une offre SaaS avec des API. Les API sont nécessaires pour permettre les abonnements à votre offre SaaS si vous avez sélectionné Vente via Azure.  Cet article est divisé en deux sections :

-   Authentification de service à service entre le service SaaS et la Place de marché Azure
-   Méthodes et points de terminaison de l’API

Les API suivantes sont fournies pour vous aider à intégrer votre service SaaS auprès d’Azure :

-   Résoudre
-   S'abonner
-   Convertir
-   Se désabonner

Le diagramme suivant montre le flux d’abonnement d’un nouveau client et le moment où ces API sont utilisées :

![Flux de l’API de l’offre SaaS](./media/saas-offer-publish-api-flow.png)


## <a name="service-to-service-authentication-between-saas-service-and-azure-marketplace"></a>Authentification de service à service entre le service SaaS et la Place de marché Azure

Azure n’impose aucune contrainte sur l’authentification que le service SaaS expose à ses utilisateurs finaux. Cependant, lorsqu’il s’agit du service SaaS communiquant avec les API Azure Marketplace, l’authentification s’effectue dans le contexte d’une application Azure Active Directory (Azure AD).

La section suivante explique comment créer une application Azure AD.


### <a name="register-an-azure-ad-application"></a>Inscrire une application Azure AD

Toute application qui souhaite utiliser les fonctionnalités d’Azure AD doit d’abord être enregistrée dans un locataire Azure AD. Le processus d’enregistrement implique de fournir à Azure AD des informations sur votre application, notamment l’URL où elle est située, l’URL à laquelle envoyer une réponse après avoir authentifié un utilisateur, l’URI qui identifie l’application, et ainsi de suite.

Pour inscrire une nouvelle application à l’aide du portail Azure, procédez comme suit :

1.  Connectez-vous au [Portail Azure](https://portal.azure.com/).
2.  Si votre compte vous propose plusieurs accès, cliquez sur votre compte en haut à droite et définissez votre session de portail pour le locataire de Azure AD souhaité.
3.  Dans le volet de navigation gauche, cliquez sur le service **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis cliquez sur **Nouvelle inscription d’application**.

    ![Inscriptions des applications SaaS](./media/saas-offer-app-registration.png)

4.  Sur la page, saisissez les informations d\'inscription de votre application :
    -   **Nom** : saisissez un nom d’application explicite
    -   **Type d’application** : 
        - Sélectionnez **Native** pour les [applications clientes](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) installées localement sur un appareil. Ce paramètre est utilisé pour les [clients natifs](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) publics OAuth.
        - Sélectionnez **Application Web / API** pour les [applications clientes](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) et les [ressources/applications API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) installées sur un serveur sécurisé. Ce paramètre est utilisé pour les [clients web](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) confidentiels OAuth et les [clients basés sur un agent utilisateur](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client) publics.
        La même application peut également exposer un client et une ressource/API.
    -   **URL de connexion** : pour des applications web app/API, indiquez l’URL de base de votre application. Par exemple, **http://localhost:31544** peut être l’URL pour une application web en cours d’exécution sur votre ordinateur local. Les utilisateurs peuvent alors utiliser cette URL pour se connecter à une application web cliente.
    -   **URI de redirection** : pour des applications natives, indiquez l’URI utilisé par Azure AD pour retourner les réponses de jeton. Saisissez une valeur spécifique à votre application, par exemple **http://MyFirstAADApp**.

        ![Inscriptions d’application SaaS AD](./media/saas-offer-app-registration-2.png) Pour obtenir des exemples spécifiques d’applications web ou d’applications natives, consultez les configurations guidées de démarrage rapide disponibles dans la section Démarrage du [Guide du développeur Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#get-started).

5.  Lorsque vous avez terminé, cliquez sur **Créer**. Azure AD assigne un ID d\'application unique à votre application, et vous êtes redirigé vers la page d\'enregistrement principale de votre application. Selon que votre application est une application native ou web, différentes options sont disponibles afin d’ajouter des fonctionnalités supplémentaires à votre application.

    **Remarque :** par défaut, l’application nouvellement inscrite est configurée pour autoriser uniquement les utilisateurs du même locataire à se connecter à votre application.

<a name="api-methods-and-endpoints"></a>Méthodes et points de terminaison de l’API
-------------------------

Les sections suivantes décrivent les méthodes d’API et les points de terminaison disponibles pour l’activation des abonnements à une offre SaaS.

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Obtenir un jeton basé sur l’application Azure AD

Méthode HTTP

`GET`

*URL de requête*

**https://login.microsoftonline.com/*{tenantId}*/oauth2/token**

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

### <a name="marketplace-api-endpoint-and-api-version"></a>Point de terminaison de l’API de la Place de marché Azure et version de l’API

Le point de terminaison pour l’API de la Place de marché Azure est `https://marketplaceapi.microsoft.com`.

La version actuelle de l’API est `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Résoudre l’abonnement

L’action POST sur le point de terminaison de résolution permet aux utilisateurs d’associer un jeton à un ID de ressource persistant.

*Requête*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Nom du paramètre** |     **Description**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  api-version        |  Version de l’opération à utiliser pour cette requête.   |
|  |  |


*En-têtes*

| **Clé d’en-tête**     | **Obligatoire** | **Description**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Non            | Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
| x-ms-correlationid | Non            | Valeur de chaîne unique pour l’opération sur le client. Elle sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| Content-Type       | Oui          | `application/json`                                        |
| autorisation      | Oui          | Jeton du porteur Web JSON (JWT).                    |
| x-ms-marketplace-token| Oui| Le paramètre de requête de jeton dans l’URL lorsque l’utilisateur est redirigé vers le site de l’ISV SaaS depuis Azure. **Remarque :** Ce jeton n’est valide que pendant 1 heure. De plus, une URL décode la valeur du jeton à partir du navigateur avant de l’utiliser.|
|  |  |  |
  

*Corps de réponse*

 ``` json       
    { 
        “id”: “”, 
        “subscriptionName”: “”,
        “offerId”:””, 
         “planId”:””
    }     
```

| **Nom du paramètre** | **Type de données** | **Description**                       |
|--------------------|---------------|---------------------------------------|
| id                 | Chaîne        | ID de l’abonnement SaaS.          |
| subscriptionName| Chaîne| Nom de l’abonnement SaaS défini par l’utilisateur dans Azure lors de son abonnement au service SaaS.|
| OfferId            | Chaîne        | ID de l’offre à laquelle l’utilisateur s’est abonné. |
| planId             | Chaîne        | ID du plan auquel l’utilisateur s’est abonné.  |
|  |  |  |


*Codes de réponse*

| **Code d’état HTTP** | **Code d’erreur**     | **Description**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Jeton résolu avec succès.                                                            |
| 400                  | `BadRequest`         | Soit les en-têtes requis sont manquants, soit une api-version invalide est spécifiée. Échec de la résolution du jeton parce que le jeton est mal formé ou a expiré (le jeton n’est valide que pendant 1 heure une fois généré). |
| 403                  | `Forbidden`          | L’appelant n’est pas autorisé à effectuer cette opération.                                 |
| 429                  | `RequestThrottleId`  | Le service est occupé à traiter les requêtes, réessayez plus tard.                                |
| 503                  | `ServiceUnavailable` | Le service est temporairement interrompu, réessayez plus tard.                                        |
|  |  |  |


*En-têtes de réponse*

| **Clé d’en-tête**     | **Obligatoire** | **Description**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Oui          | ID de requête reçu de la part du client.                                                                   |
| x-ms-correlationid | Oui          | ID de corrélation si transmis par le client, sinon cette valeur est l’ID de corrélation du serveur.                   |
| x-ms-activityid    | Oui          | Valeur de chaîne unique pour le suivi de la requête du service. Ceci est utilisé pour tous les rapprochements. |
| Retry-After        | Non            | Cette valeur est définie uniquement pour une réponse 429.                                                                   |
|  |  |  |


### <a name="subscribe"></a>S'abonner

Le point de terminaison d’abonnement permet aux utilisateurs de s’abonner à un service SaaS pour un plan donné et d’activer la facturation dans le système marchand.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}* ?api-version=2017-04-15**

| **Nom du paramètre**  | **Description**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Id unique de l’abonnement SaaS obtenu après résolution du jeton via l’API Resolve.                              |
| api-version         | Version de l’opération à utiliser pour cette requête. |
|  |  |

*En-têtes*

|  **Clé d’en-tête**        | **Obligatoire** |  **Description**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   Non          | Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| x-ms-correlationid     |   Non          | Valeur de chaîne unique pour l’opération sur le client. Cette valeur sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| If-Match/If-None-Match |   Non          |   Valeur forte d’ETag de validateur.                                                          |
| content-type           |   Oui        |    `application/json`                                                                   |
|  autorisation         |   Oui        |    Jeton du porteur Web JSON (JWT).                                               |
| x-ms-marketplace-session-mode| Non  | Drapeau pour activer le mode marche à vide lors de l’abonnement à une offre SaaS. S’il est défini, l’abonnement n’est pas facturé. Cela est utile pour les scénarios de test des éditeurs de logiciels indépendants. Définissez-le sur **‘dryrun’**|
|  |  |  |

*Corps*

``` json
  { 
      “planId”:””
   }      
```

| **Nom de l’élément** | **Type de données** | **Description**                      |
|------------------|---------------|--------------------------------------|
| planId           | (Obligatoire) Chaîne        | Id de plan auquel l’utilisateur du service SaaS est abonné.  |
|  |  |  |

*Codes de réponse*

| **Code d’état HTTP** | **Code d’erreur**     | **Description**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Activation d’abonnement SaaS reçue pour un plan donné.                   |
| 400                  | `BadRequest`         | Soit les en-têtes requis sont manquants, soit le corps JSON est mal formé. |
| 403                  | `Forbidden`          | L’appelant n’est pas autorisé à effectuer cette opération.                   |
| 404                  | `NotFound`           | Abonnement introuvable avec l’ID fourni                                  |
| 409                  | `Conflict`           | Une autre opération est en cours sur l’abonnement.                     |
| 429                  | `RequestThrottleId`  | Le service est occupé à traiter les requêtes, réessayez plus tard.                  |
| 503                  | `ServiceUnavailable` | Le service est temporairement interrompu, réessayez plus tard.                          |
|  |  |  |

Pour une réponse 202, suivez le statut de l’opération de requête dans l’en-tête « Operation-location ». L’authentification est identique à celle des autres API de la Place de marché.

*En-têtes de réponse*

| **Clé d’en-tête**     | **Obligatoire** | **Description**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Oui          | ID de requête reçu de la part du client.                                                                   |
| x-ms-correlationid | Oui          | ID de corrélation si transmis par le client, sinon cette valeur est l’ID de corrélation du serveur.                   |
| x-ms-activityid    | Oui          | Valeur de chaîne unique pour le suivi de la requête du service. Cette valeur est utilisée pour tous les rapprochements. |
| Retry-After        | Oui          | Intervalle avec lequel le client qui peut vérifier l’état.                                                       |
| Operation-Location | Oui          | Lien vers une ressource pour obtenir le statut de l’opération.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Changer de point de terminaison pour le plan

Le point de terminaison de changement permet à l’utilisateur de convertir son plan en cours en un nouveau plan.

**PATCH**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}* ?api-version=2017-04-15**

| **Nom du paramètre**  | **Description**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID d’abonnement SaaS.                              |
| api-version         | Version de l’opération à utiliser pour cette requête. |
|  |  |

*En-têtes*

| **Clé d’en-tête**          | **Obligatoire** | **Description**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | Non            | Valeur de chaîne unique pour le suivi de la requête du client. Recommandez un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.   |
| x-ms-correlationid      | Non            | Valeur de chaîne unique pour l’opération sur le client. Cette valeur sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| If-Match /If-None-Match | Non            | Valeur forte d’ETag de validateur.                              |
| content-type            | Oui          | `application/json`                                        |
| autorisation           | Oui          | Jeton du porteur Web JSON (JWT).                    |
|  |  |  |


*Corps*

``` json
                { 
                    “planId”:””
                } 
```


|  **Nom de l’élément** |  **Type de données**  | **Description**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  (Obligatoire) Chaîne         | Id de plan auquel l’utilisateur du service SaaS est abonné.          |
|  |  |  |

*Codes de réponse*

| **Code d’état HTTP** | **Code d’erreur**     | **Description**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Activation d’abonnement SaaS reçue pour un plan donné.                   |
| 400                  | `BadRequest`         | Soit les en-têtes requis sont manquants, soit le corps JSON est mal formé. |
| 403                  | `Forbidden`          | L’appelant n’est pas autorisé à effectuer cette opération.                   |
| 404                  | `NotFound`           | Abonnement introuvable avec l’ID fourni                                  |
| 409                  | `Conflict`           | Une autre opération est en cours sur l’abonnement.                     |
| 429                  | `RequestThrottleId`  | Le service est occupé à traiter les requêtes, réessayez plus tard.                  |
| 503                  | `ServiceUnavailable` | Le service est temporairement interrompu, réessayez plus tard.                          |
|  |  |  |

*En-têtes de réponse*

| **Clé d’en-tête**     | **Obligatoire** | **Description**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Oui          | ID de requête reçu de la part du client.                                                                   |
| x-ms-correlationid | Oui          | ID de corrélation si transmis par le client, sinon cette valeur est l’ID de corrélation du serveur.                   |
| x-ms-activityid    | Oui          | Valeur de chaîne unique pour le suivi de la requête du service. Cette valeur est utilisée pour tous les rapprochements. |
| Retry-After        | Oui          | Intervalle avec lequel le client qui peut vérifier l’état.                                                       |
| Operation-Location | Oui          | Lien vers une ressource pour obtenir le statut de l’opération.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Supprimer l’abonnement

L’action Delete sur le point de terminaison d’abonnement permet à un utilisateur de supprimer un abonnement avec un ID donné.

*Requête*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}* ?api-version=2017-04-15**

| **Nom du paramètre**  | **Description**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID d’abonnement SaaS.                              |
| api-version         | Version de l’opération à utiliser pour cette requête. |
|  |  |

*En-têtes*

| **Clé d’en-tête**     | **Obligatoire** | **Description**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | Non            | Valeur de chaîne unique pour le suivi de la requête du client. Recommandez un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.                                                           |
| x-ms-correlationid | Non            | Valeur de chaîne unique pour l’opération sur le client. Cette valeur sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| autorisation      | Oui          | Jeton du porteur Web JSON (JWT).                    |
|  |  |  |
 

*Codes de réponse*

| **Code d’état HTTP** | **Code d’erreur**     | **Description**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Activation d’abonnement SaaS reçue pour un plan donné.                   |
| 400                  | `BadRequest`         | Soit les en-têtes requis sont manquants, soit le corps JSON est mal formé. |
| 403                  | `Forbidden`          | L’appelant n’est pas autorisé à effectuer cette opération.                   |
| 404                  | `NotFound`           | Abonnement introuvable avec l’ID fourni                                  |
| 429                  | `RequestThrottleId`  | Le service est occupé à traiter les requêtes, veuillez réessayer ultérieurement.                  |
| 503                  | `ServiceUnavailable` | Le service est temporairement interrompu. Veuillez réessayer ultérieurement.                          |
|  |  |  |

Pour une réponse 202, suivez le statut de l’opération de requête dans l’en-tête « Operation-location ». L’authentification est identique à celle des autres API de la Place de marché.

*En-têtes de réponse*

| **Clé d’en-tête**     | **Obligatoire** | **Description**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Oui          | ID de requête reçu de la part du client.                                                                   |
| x-ms-correlationid | Oui          | ID de corrélation si transmis par le client, sinon il s’agit de l’ID de corrélation du serveur.                   |
| x-ms-activityid    | Oui          | Valeur de chaîne unique pour le suivi de la requête du service. Ceci est utilisé pour tous les rapprochements. |
| Retry-After        | Oui          | Intervalle avec lequel le client qui peut vérifier l’état.                                                       |
| Operation-Location | Oui          | Lien vers une ressource pour obtenir le statut de l’opération.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Obtenir l’état d’une opération Get

Ce terminal permet à l’utilisateur de suivre l’état d’une opération asynchrone déclenchée (S’abonner/Se désabonner/Changer de plan).

*Requête*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}*?api-version=2017-04-15**

| **Nom du paramètre**  | **Description**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | ID unique pour l’opération déclenchée.                |
| api-version         | Version de l’opération à utiliser pour cette requête. |
|  |  |


*En-têtes*

| **Clé d’en-tête**     | **Obligatoire** | **Description**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Non            | Valeur de chaîne unique pour le suivi de la requête du client. Recommandez un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.   |
| x-ms-correlationid | Non            | Valeur de chaîne unique pour l’opération sur le client. Cette valeur sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
| autorisation      | Oui          | Jeton du porteur Web JSON (JWT).                    |
|  |  |  | 
  

*Corps de réponse*

``` json
  { 
      “id”: “”, 
      “status”:””, 
       “resourceLocation”:””, 
      “created”:””, 
      “lastModified”:”” 
  } 
```

| **Nom du paramètre** | **Type de données** | **Description**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | Chaîne        | ID de l’opération.                                                                      |
| status             | Enum          | État de l’opération, une des valeurs suivantes : `In Progress`, `Succeeded` ou `Failed`.          |
| resourceLocation   | Chaîne        | Lien vers l’abonnement qui a été créé ou modifié. Cela permet au client d’obtenir une mise à jour de l’état après l’opération. Cette valeur n’est pas définie pour `Unsubscribe` opérations. |
| created            | DateTime      | Heure de création de l’opération en UTC.                                                           |
| lastModified       | DateTime      | Dernière mise à jour de l’opération en heure UTC.                                                      |
|  |  |  |

*Codes de réponse*

| **Code d’état HTTP** | **Code d’erreur**     | **Description**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Requête get résolue avec succès. Le corps contient la réponse.    |
| 400                  | `BadRequest`         | Soit les en-têtes requis sont manquants, soit une api-version invalide a été spécifiée. |
| 403                  | `Forbidden`          | L’appelant n’est pas autorisé à effectuer cette opération.                      |
| 404                  | `NotFound`           | Abonnement introuvable avec l’ID fourni                                     |
| 429                  | `RequestThrottleId`  | Le service est occupé à traiter les requêtes, réessayez plus tard.                     |
| 503                  | `ServiceUnavailable` | Le service est temporairement interrompu, réessayez plus tard.                             |
|  |  |  |

*En-têtes de réponse*

| **Clé d’en-tête**     | **Obligatoire** | **Description**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Oui          | ID de requête reçu de la part du client.                                                                   |
| x-ms-correlationid | Oui          | ID de corrélation si transmis par le client, sinon il s’agit de l’ID de corrélation du serveur.                   |
| x-ms-activityid    | Oui          | Valeur de chaîne unique pour le suivi de la requête du service. Ceci est utilisé pour tous les rapprochements. |
| Retry-After        | Oui          | Intervalle avec lequel le client qui peut vérifier l’état.                                                       |
|  |  |  |

### <a name="get-subscription"></a>S’abonner

L’action Get sur le point de terminaison d’abonnement permet à un utilisateur de récupérer un abonnement avec un identifiant de ressource donné.

*Requête*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}* ?api-version=2017-04-15**

| **Nom du paramètre**  | **Description**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID d’abonnement SaaS.                              |
| api-version         | Version de l’opération à utiliser pour cette requête. |
|  |  |

*En-têtes*

| **Clé d’en-tête**     | **Obligatoire** | **Description**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Non            | Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.                                                           |
| x-ms-correlationid | Non            | Valeur de chaîne unique pour l’opération sur le client. Cette valeur sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| autorisation      | Oui          | Jeton du porteur Web JSON (JWT).                                                                    |
|  |  |  |

*Corps de réponse*

``` json
  { 
      “id”: “”, 
      “saasSubscriptionName”:””, 
      “offerId”:””, 
       “planId”:””, 
      “saasSubscriptionStatus”:””, 
      “created”:””, 
      “lastModified”: “” 
  }
```
| **Nom du paramètre**     | **Type de données** | **Description**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Chaîne        | ID de la ressource d’abonnement SaaS dans Azure.    |
| offerId                | Chaîne        | ID de l’offre à laquelle l’utilisateur s’est abonné.         |
| planId                 | Chaîne        | ID du plan auquel l’utilisateur s’est abonné.          |
| saasSubscriptionName   | Chaîne        | Nom de l’abonnement SaaS.                |
| saasSubscriptionStatus | Enum          | État de l’opération.  Celui-ci peut avoir l'une des valeurs suivantes :  <br/> - `Subscribed` : l’abonnement est actif.  <br/> - `Pending` : l’utilisateur crée la ressource mais elle n’est pas activée par l’ISV.   <br/> - `Unsubscribed` : l’utilisateur a annulé son abonnement.   <br/> - `Suspended` : l’utilisateur a suspendu l’abonnement.   <br/> - `Deactivated` :  l’abonnement Azure est suspendu.  |
| created                | DateTime      | Valeur d’horodatage de création de l’abonnement au format UTC. |
| lastModified           | DateTime      | Valeur d’horodatage de création de l’abonnement au format UTC. |
|  |  |  |

*Codes de réponse*

| **Code d’état HTTP** | **Code d’erreur**     | **Description**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Requête get résolue avec succès. Le corps contient la réponse.    |
| 400                  | `BadRequest`         | Soit les en-têtes requis sont manquants, soit une api-version invalide a été spécifiée. |
| 403                  | `Forbidden`          | L’appelant n’est pas autorisé à effectuer cette opération.                      |
| 404                  | `NotFound`           | Abonnement introuvable avec l’ID fourni                                     |
| 429                  | `RequestThrottleId`  | Le service est occupé à traiter les requêtes, réessayez plus tard.                     |
| 503                  | `ServiceUnavailable` | Le service est temporairement interrompu, réessayez plus tard.                             |
|  |  |  |

*En-têtes de réponse*

| **Clé d’en-tête**     | **Obligatoire** | **Description**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Oui          | ID de requête reçu de la part du client.                                                                   |
| x-ms-correlationid | Oui          | ID de corrélation si transmis par le client, sinon il s’agit de l’ID de corrélation du serveur.                   |
| x-ms-activityid    | Oui          | Valeur de chaîne unique pour le suivi de la requête du service. Ceci est utilisé pour tous les rapprochements. |
| Retry-After        | Non            | Intervalle avec lequel le client qui peut vérifier l’état.                                                       |
| etag               | Oui          | Lien vers une ressource pour obtenir le statut de l’opération.                                                        |
|  |  |  |


### <a name="get-subscriptions"></a>Obtenir des abonnements

L’action Get sur le point de terminaison d’abonnement permet à un utilisateur de récupérer tous les abonnements pour toutes les offres émanant de l’éditeur de logiciels indépendant.

*Requête*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Nom du paramètre**  | **Description**                                       |
|---------------------|-------------------------------------------------------|
| api-version         | Version de l’opération à utiliser pour cette requête. |
|  |  |

*En-têtes*

| **Clé d’en-tête**     | **Obligatoire** | **Description**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Non            | Valeur de chaîne unique pour le suivi de la requête du client. Recommandez un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.             |
| x-ms-correlationid | Non            | Valeur de chaîne unique pour l’opération sur le client. Cette valeur sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| autorisation      | Oui          | Jeton du porteur Web JSON (JWT).                    |
|  |  |  |


*Corps de réponse*

``` json
  { 
      “id”: “”, 
      “saasSubscriptionName”:””, 
      “offerId”:””, 
       “planId”:””, 
      “saasSubscriptionStatus”:””, 
      “created”:””, 
      “lastModified”: “”
  }
```

| **Nom du paramètre**     | **Type de données** | **Description**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Chaîne        | ID de la ressource d’abonnement SaaS dans Azure.    |
| offerId                | Chaîne        | ID de l’offre à laquelle l’utilisateur s’est abonné.         |
| planId                 | Chaîne        | ID du plan auquel l’utilisateur s’est abonné.          |
| saasSubscriptionName   | Chaîne        | Nom de l’abonnement SaaS.                |
| saasSubscriptionStatus | Enum          | État de l’opération.  Celui-ci peut avoir l'une des valeurs suivantes :  <br/> - `Subscribed` : l’abonnement est actif.  <br/> - `Pending` : l’utilisateur crée la ressource mais elle n’est pas activée par l’ISV.   <br/> - `Unsubscribed` : l’utilisateur a annulé son abonnement.   <br/> - `Suspended` : l’utilisateur a suspendu l’abonnement.   <br/> - `Deactivated` :  l’abonnement Azure est suspendu.  |
| created                | DateTime      | Valeur d’horodatage de création de l’abonnement au format UTC. |
| lastModified           | DateTime      | Valeur d’horodatage de création de l’abonnement au format UTC. |
|  |  |  |

*Codes de réponse*

| **Code d’état HTTP** | **Code d’erreur**     | **Description**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Requête get résolue avec succès. Le corps contient la réponse.    |
| 400                  | `BadRequest`         | Soit les en-têtes requis sont manquants, soit une api-version invalide a été spécifiée. |
| 403                  | `Forbidden`          | L’appelant n’est pas autorisé à effectuer cette opération.                      |
| 404                  | `NotFound`           | Abonnement introuvable avec l’ID fourni                                     |
| 429                  | `RequestThrottleId`  | Le service est occupé à traiter les requêtes, veuillez réessayer ultérieurement.                     |
| 503                  | `ServiceUnavailable` | Le service est temporairement interrompu. Veuillez réessayer ultérieurement.                             |
|  |  |  |

*En-têtes de réponse*

| **Clé d’en-tête**     | **Obligatoire** | **Description**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Oui          | ID de requête reçu de la part du client.                                                                   |
| x-ms-correlationid | Oui          | ID de corrélation si transmis par le client, sinon il s’agit de l’ID de corrélation du serveur.                   |
| x-ms-activityid    | Oui          | Valeur de chaîne unique pour le suivi de la requête du service. Ceci est utilisé pour tous les rapprochements. |
| Retry-After        | Non            | Intervalle avec lequel le client qui peut vérifier l’état.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>Webhook SaaS

Un Webhook SaaS est utilisé pour notifier les modifications de manière proactive au service SaaS. Cette API POST est censée ne pas être authentifié et sera appelée par le service Microsoft. Le service SaaS est censé appeler l’API Opérations pour valider et autoriser avant d’entreprendre une action sur la notification de Webhook. 


*Corps*

``` json
  { 
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "sampleSaaSOffer", // Provided with "Update" action
    "publisherId": "contoso", 
    "planId": "silver",     // Provided with "Update" action
    "action": "Activate", // Activate/Delete/Suspend/Reinstate/Update
    "timeStamp": "2018-12-01T00:00:00"
  }
```

| **Nom du paramètre**     | **Type de données** | **Description**                               |
|------------------------|---------------|-----------------------------------------------|
| id  | Chaîne       | ID unique pour l’opération déclenchée.                |
| activityId   | Chaîne        | Valeur de chaîne unique pour le suivi de la requête du service. Ceci est utilisé pour tous les rapprochements.               |
| subscriptionId                     | Chaîne        | ID de la ressource d’abonnement SaaS dans Azure.    |
| offerId                | Chaîne        | ID de l’offre à laquelle l’utilisateur s’est abonné. Fourni uniquement avec l’action « Update ».        |
| publisherId                | Chaîne        | ID de l’éditeur de l’offre SaaS         |
| planId                 | Chaîne        | ID du plan auquel l’utilisateur s’est abonné. Fourni uniquement avec l’action « Update ».          |
| action                 | Chaîne        | Action qui déclenche cette notification. Valeurs possibles : Activate, Delete, Suspend, Reinstate, Update          |
| timeStamp                 | Chaîne        | Valeur d’horodatage UTC correspondant au moment où cette notification a été déclenchée.          |
|  |  |  |
