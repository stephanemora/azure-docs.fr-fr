---
title: API de traitement SaaS v1 | Place de marché Azure
description: Explique comment créer et gérer une offre SaaS sur la Place de marché Azure à l’aide des API de traitement v1 associées.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 05/23/2019
ms.author: dsindona
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec8373288a2ea5809ee5d349c52c57051586035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288340"
---
# <a name="saas-fulfillment-apis-version-1-deprecated"></a>API de traitement SaaS version 1 (obsolète)

Cet article explique comment créer une offre SaaS avec des API. Les API, composées de méthodes et de points de terminaison REST, sont nécessaires pour permettre les abonnements à votre offre SaaS si vous avez sélectionné Vente via Azure.  

> [!WARNING]
> Cette version initiale de l’API de traitement SaaS est obsolète ; nous vous recommandons d’utiliser plutôt l’[API de traitement SaaS V2](./pc-saas-fulfillment-api-v2.md).  Cette version initiale de l'API est actuellement uniquement maintenue pour servir les serveurs de publication existants. 

Les API suivantes sont fournies pour vous aider à intégrer votre service SaaS auprès d’Azure :

-   Résoudre
-   S'abonner
-   Convertir
-   Se désabonner


## <a name="api-methods-and-endpoints"></a>Méthodes et points de terminaison de l’API

Les sections suivantes décrivent les méthodes d’API et les points de terminaison disponibles pour l’activation des abonnements à une offre SaaS.


### <a name="marketplace-api-endpoint-and-api-version"></a>Point de terminaison de l’API de la Place de marché Azure et version de l’API

Le point de terminaison pour l’API de la Place de marché Azure est `https://marketplaceapi.microsoft.com`.

La version actuelle de l’API est `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Résoudre l’abonnement

L’action POST sur le point de terminaison de résolution permet aux utilisateurs d’associer un jeton de place de marché à un ID de ressource persistant.  L’ID de ressource est l’identificateur unique de l’abonnement SAAS. 

Lorsqu’un utilisateur est redirigé vers le site web d’un éditeur de logiciels indépendant, les paramètres de requête figurant dans l’URL contiennent un jeton. L’éditeur de logiciels indépendant doit utiliser ce jeton et effectuer une requête pour le résoudre. La réponse contient l’ID d’abonnement SAAS unique, le nom, l’ID d’offre et le plan associé à la ressource. Ce jeton est valide pendant une heure uniquement.

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
| x-ms-requestid     | Non           | Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
| x-ms-correlationid | Non           | Valeur de chaîne unique pour l’opération sur le client. Ce champ sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| Content-Type       | Oui          | `application/json`                                        |
| autorisation      | Oui          | Jeton du porteur Web JSON (JWT).                    |
| x-ms-marketplace-token| Oui| Paramètre de requête de jeton présent dans l’URL lorsque l’utilisateur est redirigé d’Azure vers le site web de l’éditeur de logiciels indépendant SaaS. **Remarque :** Ce jeton n’est valide que pendant 1 heure. De plus, une URL décode la valeur du jeton à partir du navigateur avant de l’utiliser.|
|  |  |  |
  

*Corps de réponse*

``` json
{
    "id": "",
    "subscriptionName": "",
    "offerId": "",
    "planId": "",
}
```

| **Nom du paramètre** | **Type de données** | **Description**                       |
|--------------------|---------------|---------------------------------------|
| id                 | String        | ID de l’abonnement SaaS.          |
| subscriptionName| String| Nom de l’abonnement SaaS défini par l’utilisateur dans Azure lors de son abonnement au service SaaS.|
| OfferId            | String        | ID de l’offre à laquelle l’utilisateur s’est abonné. |
| planId             | String        | ID du plan auquel l’utilisateur s’est abonné.  |
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
| x-ms-activityid    | Oui          | Valeur de chaîne unique pour le suivi de la requête du service. Cet ID est utilisé pour tous les rapprochements. |
| Retry-After        | Non           | Cette valeur est définie uniquement pour une réponse 429.                                                                   |
|  |  |  |


### <a name="subscribe"></a>S'abonner

Le point de terminaison d’abonnement permet aux utilisateurs de s’abonner à un service SaaS pour un plan donné et d’activer la facturation dans le système marchand.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ?api-version=2017-04-15**

| **Nom du paramètre**  | **Description**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Id unique de l’abonnement SaaS obtenu après résolution du jeton via l’API Resolve.                              |
| api-version         | Version de l’opération à utiliser pour cette requête. |
|  |  |

*En-têtes*

|  **Clé d’en-tête**        | **Obligatoire** |  **Description**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   Non         | Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| x-ms-correlationid     |   Non         | Valeur de chaîne unique pour l’opération sur le client. Cette valeur sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| If-Match/If-None-Match |   Non         |   Valeur forte d’ETag de validateur.                                                          |
| content-type           |   Oui        |    `application/json`                                                                   |
|  autorisation         |   Oui        |    Jeton du porteur Web JSON (JWT).                                               |
| x-ms-marketplace-session-mode| Non | Drapeau pour activer le mode marche à vide lors de l’abonnement à une offre SaaS. S’il est défini, l’abonnement n’est pas facturé. Cela est utile pour les scénarios de test des éditeurs de logiciels indépendants. Définissez-le sur **« dryrun »** .|
|  |  |  |

*Corps*

``` json
{
    "lanId": "",
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

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ?api-version=2017-04-15**

| **Nom du paramètre**  | **Description**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID d’abonnement SaaS.                              |
| api-version         | Version de l’opération à utiliser pour cette requête. |
|  |  |

*En-têtes*

| **Clé d’en-tête**          | **Obligatoire** | **Description**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | Non           | Valeur de chaîne unique pour le suivi de la requête du client. Recommandez un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.   |
| x-ms-correlationid      | Non           | Valeur de chaîne unique pour l’opération sur le client. Cette valeur sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| If-Match /If-None-Match | Non           | Valeur forte d’ETag de validateur.                              |
| content-type            | Oui          | `application/json`                                        |
| autorisation           | Oui          | Jeton du porteur Web JSON (JWT).                    |
|  |  |  |

*Corps*

```json
{
    "planId": ""
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

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ?api-version=2017-04-15**

| **Nom du paramètre**  | **Description**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID d’abonnement SaaS.                              |
| api-version         | Version de l’opération à utiliser pour cette requête. |
|  |  |

*En-têtes*

| **Clé d’en-tête**     | **Obligatoire** | **Description**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | Non           | Valeur de chaîne unique pour le suivi de la requête du client. Recommandez un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.                                                           |
| x-ms-correlationid | Non           | Valeur de chaîne unique pour l’opération sur le client. Cette valeur sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
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

**https://marketplaceapi.microsoft.com/api/saas/operations/ *{operationId}* ?api-version=2017-04-15**

| **Nom du paramètre**  | **Description**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | ID unique pour l’opération déclenchée.                |
| api-version         | Version de l’opération à utiliser pour cette requête. |
|  |  |

*En-têtes*

| **Clé d’en-tête**     | **Obligatoire** | **Description**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Non           | Valeur de chaîne unique pour le suivi de la requête du client. Recommandez un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.   |
| x-ms-correlationid | Non           | Valeur de chaîne unique pour l’opération sur le client. Cette valeur sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
| autorisation      | Oui          | Jeton du porteur Web JSON (JWT).                    |
|  |  |  | 

*Corps de réponse*

```json
{
    "id": "",
    "status": "",
    "resourceLocation": "",
    "created": "",
    "lastModified": ""
}
```

| **Nom du paramètre** | **Type de données** | **Description**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | String        | ID de l'opération.                                                                      |
| status             | Enum          | État de l’opération, une des valeurs suivantes : `In Progress`, `Succeeded` ou `Failed`.          |
| resourceLocation   | String        | Lien vers l’abonnement qui a été créé ou modifié. Cela permet au client d’obtenir une mise à jour de l’état après l’opération. Cette valeur n’est pas définie pour `Unsubscribe` opérations. |
| created            | DateTime      | Heure de création de l’opération en UTC.                                                           |
| lastModified       | DateTime      | Dernière mise à jour de l’opération en heure UTC.                                                      |
|  |  |  |

*Codes de réponse*

| **Code d’état HTTP** | **Code d’erreur**     | **Description**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Requête get résolue avec succès. Le corps contient la réponse.    |
| 400                  | `BadRequest`         | Soit les en-têtes requis sont manquants, soit une api-version invalide a été spécifiée. |
| 403                  | `Forbidden`          | L’appelant n’est pas autorisé à effectuer cette opération.                      |
| 404                  | `NotFound`           | Abonnement introuvable avec l’ID fourni.                                     |
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

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ?api-version=2017-04-15**

| **Nom du paramètre**  | **Description**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID d’abonnement SaaS.                              |
| api-version         | Version de l’opération à utiliser pour cette requête. |
|  |  |

*En-têtes*

| **Clé d’en-tête**     | **Obligatoire** | **Description**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Non           | Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.                                                           |
| x-ms-correlationid | Non           | Valeur de chaîne unique pour l’opération sur le client. Cette valeur sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| autorisation      | Oui          | Jeton du porteur Web JSON (JWT).                                                                    |
|  |  |  |

*Corps de réponse*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Nom du paramètre**     | **Type de données** | **Description**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | String        | ID de la ressource d’abonnement SaaS dans Azure.    |
| offerId                | String        | ID de l’offre à laquelle l’utilisateur s’est abonné.         |
| planId                 | String        | ID du plan auquel l’utilisateur s’est abonné.          |
| saasSubscriptionName   | String        | Nom de l’abonnement SaaS.                |
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
| Retry-After        | Non           | Intervalle avec lequel le client qui peut vérifier l’état.                                                       |
| eTag               | Oui          | Lien vers une ressource pour obtenir le statut de l’opération.                                                        |
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
| x-ms-requestid     | Non           | Valeur de chaîne unique pour le suivi de la requête du client. Recommandez un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.             |
| x-ms-correlationid | Non           | Valeur de chaîne unique pour l’opération sur le client. Cette valeur sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| autorisation      | Oui          | Jeton du porteur Web JSON (JWT).                    |
|  |  |  |

*Corps de réponse*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Nom du paramètre**     | **Type de données** | **Description**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | String        | ID de la ressource d’abonnement SaaS dans Azure    |
| offerId                | String        | ID de l’offre à laquelle l’utilisateur s’est abonné         |
| planId                 | String        | ID du plan auquel l’utilisateur s’est abonné          |
| saasSubscriptionName   | String        | Nom de l’abonnement SaaS                |
| saasSubscriptionStatus | Enum          | État de l’opération.  Celui-ci peut avoir l'une des valeurs suivantes :  <br/> - `Subscribed` : l’abonnement est actif.  <br/> - `Pending` : l’utilisateur crée la ressource mais elle n’est pas activée par l’ISV.   <br/> - `Unsubscribed` : l’utilisateur a annulé son abonnement.   <br/> - `Suspended` : l’utilisateur a suspendu l’abonnement.   <br/> - `Deactivated` :  l’abonnement Azure est suspendu.  |
| created                | DateTime      | Valeur d’horodatage de création de l’abonnement au format UTC |
| lastModified           | DateTime      | Valeur d’horodatage de création de l’abonnement au format UTC |
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
| Retry-After        | Non           | Intervalle avec lequel le client qui peut vérifier l’état.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>Webhook SaaS

Un Webhook SaaS est utilisé pour notifier les modifications de manière proactive au service SaaS. Cette API POST est censée ne pas être authentifié et sera appelée par le service Microsoft. Le service SaaS est censé appeler l’API Opérations pour valider et autoriser avant d’entreprendre une action sur la notification de Webhook. 

*Corps*

``` json
  {
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "action": "Subscribe", // Subscribe/Unsubscribe/ChangePlan
    "operationRequestSource":"Azure",
    "timeStamp":"2018-12-01T00:00:00"
  }
```

| **Nom du paramètre**     | **Type de données** | **Description**                               |
|------------------------|---------------|-----------------------------------------------|
| id  | String       | ID unique pour l’opération déclenchée.                |
| activityId   | String        | Valeur de chaîne unique pour le suivi de la requête du service. Ceci est utilisé pour tous les rapprochements.               |
| subscriptionId                     | String        | ID de la ressource d’abonnement SaaS dans Azure.    |
| offerId                | String        | ID de l’offre à laquelle l’utilisateur s’est abonné. Fourni uniquement avec l’action « Update ».        |
| publisherId                | String        | ID de l’éditeur de l’offre SaaS         |
| planId                 | String        | ID du plan auquel l’utilisateur s’est abonné. Fourni uniquement avec l’action « Update ».          |
| action                 | String        | Action qui déclenche cette notification. Valeurs possibles : Activate, Delete, Suspend, Reinstate, Update          |
| timeStamp                 | String        | Valeur d’horodatage UTC correspondant au moment où cette notification a été déclenchée.          |
|  |  |  |


## <a name="next-steps"></a>Étapes suivantes

Les [API REST du portail Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) permettent aux développeurs de récupérer et de manipuler programmatiquement des charges de travail, des offres et des profils d’éditeur.
