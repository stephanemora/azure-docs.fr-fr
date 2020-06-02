---
title: API de traitement SaaS v2 dans la Place de marché commerciale Microsoft
description: Découvrez comment créer et gérer une offre SaaS sur Microsoft AppSource et la Place de marché Azure à l’aide des API de traitement version 2.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/18/2019
ms.author: dsindona
ms.openlocfilehash: 8f452349c0d636bc74a3722e2526623c955093f7
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854196"
---
# <a name="saas-fulfillment-apis-version-2-in-microsoft-commercial-marketplace"></a>API de traitement SaaS version 2 dans la Place de marché commerciale Microsoft

Cet article décrit les API de traitement qui permettent aux partenaires de vendre leurs applications SaaS dans la Microsoft AppSource et la Place de marché Azure. Ces API sont requises pour les offres SaaS qui peuvent être proposées.

## <a name="managing-the-saas-subscription-life-cycle"></a>Gestion du cycle de vie des abonnements SaaS

L’application Azure SaaS gère le cycle de vie complet d’un achat d’abonnement SaaS. Elle utilise les API de traitement comme mécanisme pour gérer l’exécution effective, les modifications apportées aux plans et la suppression de l’abonnement avec le partenaire. La facture du client se base sur l’état de l’abonnement SaaS managé par Microsoft. Le diagramme suivant illustre les états et les opérations qui pilotent les modifications entre les états.

![États du cycle de vie d’un abonnement SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>États d’un abonnement SaaS

Le tableau suivant répertorie les états de provisionnement d’un abonnement SaaS. Un diagramme de séquence et une description sont inclus pour chaque état (si applicable). 

#### <a name="provisioning"></a>Approvisionnement

Lorsqu’un client initie un achat, le partenaire reçoit ces informations dans un code d’autorisation sur une page web interactive cliente utilisant un paramètre d’URL. Par exemple : `https://contoso.com/signup?token=..`, alors que l’URL de page d’accueil dans l’Espace partenaires est `https://contoso.com/signup`. Le code d’autorisation peut être validé et échangé contre les détails du service de provisionnement en appelant l’API Resolve.  Une fois qu’un service SaaS a terminé le provisionnement, il envoie un appel d’activation pour signaler que le traitement est terminé et que le client peut être facturé. 

Le diagramme suivant illustre la séquence d’appels de l’API pour un scénario de provisionnement.  

![Appels de l’API pour le provisionnement d’un service SaaS](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>approvisionné

Cet état est l’état stable d’un service provisionné.

##### <a name="provisioning-for-update"></a>Provisionnement pour mise à jour 

Cet état indique que la mise à jour d’un service existant est en attente. Une telle mise à jour peut être lancée par le client sur la Place de marché ou sur le service SaaS (uniquement pour les transactions directes au client).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Provisionnement pour mise à jour (en cas de lancement depuis la Place de marché)

Le diagramme suivant montre la séquence d’actions effectuées lorsqu’une mise à jour est lancée à partir de la Place de marché.

![Appels d’API lorsque la mise à jour est lancée à partir de la Place de marché](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Provisionnement pour mise à jour (en cas de lancement depuis le service SaaS)

Le diagramme suivant montre les actions effectuées quand une mise à jour est lancée à partir du service SaaS. (L’appel webhook est remplacé par une mise à jour de l’abonnement lancée par le service SaaS.) 

![Appels d’API lorsque la mise à jour est lancée à partir du service SaaS](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Interrompu

Cet état indique que le paiement d’un client n’a pas été reçu. Nous offrons au client une période de grâce avant d’annuler l’abonnement. Quand un abonnement a cet état : 

- En tant que partenaire, vous pouvez choisir de réduire ou de bloquer l’accès de l’utilisateur au service.
- L’abonnement doit être conservé dans un état récupérable dont toutes les fonctionnalités peuvent être restaurées sans perte de données ou de paramètres. 
- Attendez-vous à recevoir une demande de réactivation pour cet abonnement par le biais de l’API de traitement ou une demande de déprovisionnement à la fin de la période de grâce. 

#### <a name="unsubscribed"></a>Désabonné 

Les abonnements atteignent cet état en réponse à une demande client explicite ou au non-paiement de ce qui est dû. Il est attendu du partenaire que les données du client soient conservées pour récupération sur demande durant un certain nombre de jours, puis supprimées. 


## <a name="api-reference"></a>Informations de référence sur l'API

Cette section documente *l’API Subscription* et *l’API Operations* SaaS.  La valeur du paramètre `api-version` pour la version 2 des API est `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Définitions de paramètre et d’entité

Le tableau suivant répertorie les définitions des entités et paramètres courants utilisés par les API de traitement.

|     Entité/Paramètre     |     Définition                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Identificateur GUID d’une ressource SaaS.  |
| `name`                   | Nom convivial fourni pour cette ressource par le client. |
| `publisherId`            | Identificateur de chaîne unique pour chaque éditeur (par exemple « contoso »). |
| `offerId`                | Identificateur de chaîne unique pour chaque offre (par exemple « offer1 »).  |
| `planId`                 | Identificateur de chaîne unique pour chaque plan/référence SKU (par exemple « silver »). |
| `operationId`            | Identificateur GUID d’une opération spécifique.  |
|  `action`                | Action effectuée sur une ressource (`Unsubscribe`, `Suspend`, `Reinstate` ou `ChangePlan`, `ChangeQuantity`, `Transfer`). |
|   |   |

Les identificateurs globaux uniques ([GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) sont des nombres de 128 bits (32 chiffres hexadécimaux) générés automatiquement. 

#### <a name="resolve-a-subscription"></a>Résoudre un abonnement 

Le point de terminaison de résolution permet à l’éditeur d’associer un jeton de place de marché à un ID de ressource persistant. L’ID de ressource est l’identificateur unique d’un abonnement SaaS. Lorsqu’un utilisateur est redirigé vers le site web d’un partenaire, les paramètres de requête figurant dans l’URL contiennent un jeton. Le partenaire doit utiliser ce jeton et effectuer une requête pour le résoudre. La réponse contient l’ID d’abonnement SaaS unique, le nom, l’ID d’offre et le plan associé à la ressource. Ce jeton est valide pendant une heure uniquement. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Paramètres de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Version de l’opération à utiliser pour cette requête.  |

*En-têtes de requête :*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  x-ms-correlationid |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  autorisation     |  [Obtenir le jeton du porteur web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Par exemple : « `Bearer <access_token>` ». |
|  x-ms-marketplace-token  |  Paramètre de requête de jeton présent dans l’URL lorsque l’utilisateur est redirigé d’Azure vers le site web du partenaire SaaS (par exemple, `https://contoso.com/signup?token=..`). *Remarque :* L’URL décode la valeur du jeton à partir du navigateur avant de l’utiliser.  |

*Codes de réponse :*

Code : 200<br>
Résout le jeton opaque sur un abonnement SaaS. Corps de réponse :
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Code : 400<br>
Demande incorrecte. x-ms-marketplace-token est manquant, incorrect ou a expiré.

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide ou la demande tente d’accéder à une acquisition qui n’appartient pas à l’éditeur actif.

Code : 404<br>
Introuvable.

Code : 500<br>
Erreur interne du serveur.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>API d’abonnement

L’API d’abonnement prend en charge les opérations HTTPS suivantes : **Get**, **Post**, **Patch** et **Delete**.


#### <a name="list-subscriptions"></a>Liste des abonnements

Répertorie tous les abonnements SaaS pour un éditeur.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Obtenir<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Paramètres de requête :*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  Version de l’opération à utiliser pour cette requête.  |

*En-têtes de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| x-ms-correlationid |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
| autorisation      |  [Obtenir le jeton du porteur web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Par exemple : « `Bearer <access_token>` ».  |

*Codes de réponse :*

Code : 200 <br/>
Obtenir l’éditeur et les abonnements correspondants pour toutes les offres de l’éditeur, selon le jeton d’authentification.

>[!Note]
>Les [API factices](#mock-apis) sont utilisées quand vous développez l’offre pour la première fois, tandis que les API réelles doivent être utilisées lors de la publication de l’offre.  Les API réelles et les API factices diffèrent par la première ligne du code.  L’API réelle contient la section `subscription`, qui n’existe pas pour l’API factice.

Charge utile de la réponse pour l’API factice :<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": "true", // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```
Et pour l’API réelle : <br>

```json
{
  "subscriptions": [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant, object id and email address for which SaaS subscription is purchased.
              "emailId": "<email>",
              "objectId": "<guid>",                     
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant, object id and email address that purchased the SaaS subscription. These could be different for reseller scenario
              "emailId": "<email>",
              "objectId": "<guid>",                      
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.(optional field - default false)
          "isTest": false, //indicating whether the current subscription is a test asset
          "sandboxType": "None", // Possible Values: None, Csp (Csp sandbox purchase)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "@nextLink": ""
}
```
Le jeton de continuation n’est présent que s’il existe plus de « pages » de plans à récupérer. 

Code : 403 <br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide ou la demande tente d’accéder à une acquisition qui n’appartient pas à l’éditeur actif. 

Code : 500<br>
Erreur interne du serveur.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>Obtenir un abonnement

Obtient l’abonnement SaaS spécifié. Utilisez cet appel pour obtenir des informations sur la licence et des informations sur le plan.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Obtenir<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Paramètres de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Identificateur unique de l’abonnement SaaS obtenu après résolution du jeton via l’API Resolve.   |
|  ApiVersion        |   Version de l’opération à utiliser pour cette requête.   |

*En-têtes de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  x-ms-correlationid |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  autorisation     |  [Obtenir le jeton du porteur web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Par exemple : « `Bearer <access_token>` ».  |

*Codes de réponse :*

Code : 200<br>
Obtient l’abonnement SaaS à partir de l’identificateur. Charge utile de réponse :<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "isFreeTrial": "true", // true - customer subscription is currently in free trial, false - customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide ou la demande tente d’accéder à une acquisition qui n’appartient pas à l’éditeur actif.

Code : 404<br>
Introuvable.<br> 

Code : 500<br>
Erreur interne du serveur.<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Liste des plans disponibles

Utilisez cet appel pour déterminer s’il existe des offres publiques ou privées pour l’éditeur actif.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Obtenir<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Paramètres de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Version de l’opération à utiliser pour cette requête.  |

*En-têtes de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  x-ms-correlationid  | Valeur de chaîne unique pour l’opération sur le client. Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  autorisation     |  [Obtenir le jeton du porteur web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Par exemple : « `Bearer <access_token>` ». |

*Codes de réponse :*

Code : 200<br>
Permet d’obtenir la liste des plans disponibles pour un client. Corps de réponse :

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

Code : 404<br>
Introuvable.<br> 

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide ou la demande tente d’accéder à une acquisition qui n’appartient pas à l’éditeur actif. <br> 

Code : 500<br>
Erreur interne du serveur.<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Activer un abonnement

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Post<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Paramètres de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Version de l’opération à utiliser pour cette requête.  |
| subscriptionId     | Identificateur unique de l’abonnement SaaS obtenu après résolution du jeton via l’API Resolve.  |

*En-têtes de requête :*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  x-ms-correlationid  | Valeur de chaîne unique pour l’opération sur le client. Cette chaîne sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  autorisation     |  [Obtenir le jeton du porteur web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Par exemple : « `Bearer <access_token>` ». |

*Charge utile de demande :*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Codes de réponse :*

Code : 200<br>
Active l’abonnement.<br>

Code : 400<br>
Demande incorrecte : échecs de validation.

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide ou la demande tente d’accéder à une acquisition qui n’appartient pas à l’éditeur actif.

Code : 404<br>
Introuvable.

Code : 500<br>
Erreur interne du serveur.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>Modifier le plan de l’abonnement

Mettre à jour le plan de l’abonnement.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Correctif<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Paramètres de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Version de l’opération à utiliser pour cette requête.  |
| subscriptionId     | Identificateur unique de l’abonnement SaaS obtenu après résolution du jeton via l’API Resolve.  |

*En-têtes de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  x-ms-correlationid  |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.    |
| autorisation      |  [Obtenir le jeton du porteur web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Par exemple : « `Bearer <access_token>` ».  |

*Charge utile de demande :*

```json
Request Body:
{
    "planId": "gold"
}
```

*En-têtes de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Lien vers une ressource pour obtenir le statut de l’opération.   |

*Codes de réponse :*

Code : 202<br>
La demande de modification de plan a été acceptée. Le partenaire doit interroger Operation-Location pour déterminer la réussite ou l’échec. <br>

Code : 400<br>
Demande incorrecte : échecs de validation.

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide ou la demande tente d’accéder à une acquisition qui n’appartient pas à l’éditeur actif.

Code : 404<br>
Introuvable.

Code : 500<br>
Erreur interne du serveur.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Un plan ou une quantité peut être corrigé(e), mais pas les deux simultanément. Modifie un abonnement où **Mise à jour** n’est pas dans `allowedCustomerOperations`.

#### <a name="change-the-quantity-on-the-subscription"></a>Modifier la quantité de l’abonnement

Mettre à jour la quantité de l’abonnement.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch :<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Paramètres de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Version de l’opération à utiliser pour cette requête.  |
| subscriptionId     | Identificateur unique de l’abonnement SaaS obtenu après résolution du jeton via l’API Resolve.  |

*En-têtes de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  x-ms-correlationid  |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.    |
| autorisation      |  [Obtenir le jeton du porteur web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Par exemple : « `Bearer <access_token>` ».  |

*Charge utile de demande :*

```json
Request Body:
{
    "quantity": 5
}
```

*En-têtes de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Lien vers une ressource pour obtenir le statut de l’opération.   |

*Codes de réponse :*

Code : 202<br>
La demande de modification de quantité a été acceptée. Le partenaire doit interroger Operation-Location pour déterminer la réussite ou l’échec. <br>

Code : 400<br>
Demande incorrecte : échecs de validation.


Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide ou la demande tente d’accéder à une acquisition qui n’appartient pas à l’éditeur actif.

Code : 404<br>
Introuvable.

Code : 500<br>
Erreur interne du serveur.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Un plan ou une quantité peut être corrigé(e), mais pas les deux simultanément. Modifie un abonnement où **Mise à jour** n’est pas dans `allowedCustomerOperations`.

#### <a name="delete-a-subscription"></a>Supprimer un abonnement

Annuler l’abonnement et supprimer l’abonnement spécifié.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>DELETE<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Paramètres de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Version de l’opération à utiliser pour cette requête.  |
| subscriptionId     | Identificateur unique de l’abonnement SaaS obtenu après résolution du jeton via l’API Resolve.  |

*En-têtes de requête :*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.   |
|  x-ms-correlationid  |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.   |
|  autorisation     |  [Obtenir le jeton du porteur web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Par exemple : « `Bearer <access_token>` ».  |

*Codes de réponse :*

Code : 202<br>
Le partenaire a lancé un appel pour annuler un abonnement SaaS.<br>

Code : 400<br>
Supprimer un abonnement où **Supprimer** n’est pas dans `allowedCustomerOperations`.

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide ou la demande tente d’accéder à une acquisition qui n’appartient pas à l’éditeur actif.

Code : 404<br>
Introuvable.

Code : 500<br>
Erreur interne du serveur.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>API Operations

L’API d’opérations prend en charge les opérations Patch et Get suivantes.

#### <a name="list-outstanding-operations"></a>Liste des opérations en attente 

Répertorie les opérations en attente pour l’éditeur actif. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Obtenir<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Paramètres de requête :*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   Version de l’opération à utiliser pour cette requête.                |
| subscriptionId     | Identificateur unique de l’abonnement SaaS obtenu après résolution du jeton via l’API Resolve.  |

*En-têtes de requête :*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  x-ms-correlationid |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  autorisation     |  [Obtenir le jeton du porteur web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Par exemple : « `Bearer <access_token>` ».  |

*Codes de réponse :*

Code : 200<br> Obtient la liste des opérations en attente sur un abonnement. Charge utile de réponse :

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```


Code : 400<br>
Demande incorrecte : échecs de validation.

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide ou la demande tente d’accéder à une acquisition qui n’appartient pas à l’éditeur actif.

Code : 404<br>
Introuvable.

Code : 500<br>
Erreur interne du serveur.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Obtenir l’état d’une opération

Permet à l’éditeur de suivre l’état de l’opération asynchrone déclenchée spécifiée (comme `Subscribe`, `Unsubscribe`, `ChangePlan` ou `ChangeQuantity`).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Obtenir<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Paramètres de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Version de l’opération à utiliser pour cette requête.  |

*En-têtes de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  x-ms-correlationid |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  autorisation     |  [Obtenir le jeton du porteur web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Par exemple : « `Bearer <access_token>` ».  |

*Codes de réponse :*<br>

Code : 200<br> Obtient l’opération SaaS en attente spécifiée. Charge utile de réponse :

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

Code : 400<br>
Demande incorrecte : échecs de validation.

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide ou la demande tente d’accéder à une acquisition qui n’appartient pas à l’éditeur actif.
 
Code : 404<br>
Introuvable.

Code : 500<br> Erreur interne du serveur.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Mettre à jour l’état d’une opération

Mettre à jour l’état d’une opération pour indiquer la réussite ou l’échec avec les valeurs fournies.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Correctif<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Paramètres de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  Version de l’opération à utiliser pour cette requête.  |
| subscriptionId     | Identificateur unique de l’abonnement SaaS obtenu après résolution du jeton via l’API Resolve.  |
|  operationId       | L’opération en cours. |

*En-têtes de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  x-ms-correlationid |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  autorisation     |  [Obtenir le jeton du porteur web JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Par exemple : « `Bearer <access_token>` ».  |

*Charge utile de demande :*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Codes de réponse :*

Code : 200<br> Appel pour informer de la fin d’une opération côté partenaire. Par exemple, cette réponse peut signaler la modification de postes ou de plans.

Code : 400<br>
Demande incorrecte : échecs de validation.

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide ou la demande tente d’accéder à une acquisition qui n’appartient pas à l’éditeur actif.

Code : 404<br>
Introuvable.

Code : 409<br>
Conflit. Par exemple, une transaction plus récente est déjà traitée.

Code : 500<br> Erreur interne du serveur.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implémentation d’un webhook sur le service SaaS

L’éditeur doit implémenter un webhook dans ce service SaaS pour informer les utilisateurs des changements dans son service. Le service SaaS est censé appeler l’API Opérations pour valider et autoriser avant d’entreprendre une action sur la notification de Webhook.


```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher's name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Il peut s’agir de l’une des actions suivantes : 
- `Unsubscribe` (lorsque la ressource a été supprimée)
- `ChangePlan` (lorsque l’opération de changement de plan est terminée)
- `ChangeQuantity` (lorsque l’opération de changement de quantité est terminée)
- `Suspend` (lorsque la ressource a été suspendue)
- `Reinstate` (lorsque ressource a été rétablie après suspension)

Il peut s’agir de l’un des états suivants : 
- **NotStarted** <br>
 - **InProgress** <br>
- **Réussi** <br>
- **Échec** <br>
- **Conflict** <br>

Dans une notification webhook, les états exploitables sont **Succeeded** and **Failed**. Le cycle de vie d’une opération va de **NotStarted** jusqu’à un état terminal comme **Succeeded**, **Failed** ou **Conflict**. Si vous recevez **NotStarted** ou **InProgress**, continuez à demander l’état via l’API GET jusqu’à ce que l’opération atteigne un état terminal avant d’effectuer une action. 

## <a name="mock-apis"></a>API factices

Vous pouvez utiliser nos API factices pour vous aider à bien démarrer avec le développement, en particulier pour le prototypage et le test d’un projet. 

Point de terminaison hôte : `https://marketplaceapi.microsoft.com/api` (aucune authentification requise)<br/>
Version d’API : `2018-09-15`<br/>
URI d’exemple : `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Les chemins d’accès au point de terminaison de l’API sont les mêmes pour les API factices et réelles, mais les versions d’API sont différentes. La version est `2018-09-15` pour l’API factice et `2018-08-31` pour la version de production. 

Les appels d’API dans cet article peut être adressés au point de terminaison hôte factice. En général, attendez-vous à obtenir des données factices en réponse. Les appels aux méthodes de mise à jour d’abonnement sur l’API factice retournent toujours 500. 

## <a name="next-steps"></a>Étapes suivantes

Les [API REST du portail Microsoft Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) permettent aux développeurs de récupérer et de manipuler programmatiquement des charges de travail, des offres et des profils d’éditeur.
