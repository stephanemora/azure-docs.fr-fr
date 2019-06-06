---
title: V2 de traitement des commandes API SaaS | Place de marché Azure
description: Explique comment créer et gérer une offre SaaS sur AppSource et de la place de marché Azure à l’aide de l’exécution associée v2 API.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: 1aba0ab7083c437210166d2d5a2d77e7a657afe9
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474587"
---
# <a name="saas-fulfillment-apis-version-2"></a>API de traitement des commandes SaaS version 2 

Cet article explique comment l’API qui permet des éditeurs de logiciels indépendants (ISV) pour vendre leurs applications SaaS dans AppSource et de la place de marché Azure. Cette API est qu'une exigence pour SaaS transactable offre sur AppSource et à la place de marché Azure.

## <a name="managing-the-saas-subscription-lifecycle"></a>La gestion du cycle de vie des abonnement SaaS

Microsoft Service SaaS gère le cycle de vie d’un achat d’abonnement SaaS et utilise l’API de traitement des commandes, un mécanisme pour piloter l’exécution réelle, les modifications apportées à des plans et la suppression de l’abonnement avec l’éditeur de logiciels. Le client est facturé selon l’état de l’abonnement de SaaS Microsoft tient à jour. Le diagramme suivant illustre les États et les opérations qui pilotent les modifications entre les États.

![États du cycle de vie d’un abonnement SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>États d’un abonnement de SaaS

Le tableau suivant répertorie les États d’approvisionnement pour un abonnement SaaS, y compris un diagramme de séquence de description pour chacune (le cas échéant). 

#### <a name="provisioning"></a>Approvisionnement

Lorsqu’un client démarre un achat, l’ISV reçoit ces informations dans un Code d’authentification sur une page web interactive de client à l’aide d’un paramètre d’URL. Par exemple : `https://contoso.com/signup?token=..`, où le fournisseur d’URL de page d’accueil dans Centre partenaires est `https://contoso.com/signup`. Le code d’authentification peut être validé et échangé pour les détails de ce qui doit être mis en service en appelant l’API résoudre.  Une fois le service SaaS d’approvisionnement, il envoie un appel de l’activer pour signaler que l’exécution est terminée et que le client peut être facturé.  Le diagramme suivant illustre la séquence d’appels d’API pour un scénario d’approvisionnement.  

![Appels d’API pour un service SaaS d’approvisionnement.](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>approvisionné

Cet état est l’état stable d’un service configuré.

#### <a name="provisioning-for-update"></a>Mise en service de mise à jour
(à partir de la place de marché) 

Cet état représente une mise à jour à un service est en attente. Une mise à jour peut être lancée par le client sur la place de marché, soit sur le service SaaS (uniquement pour direct pour des transactions client). Le diagramme suivant montre les actions quand une mise à jour est lancée à partir de la place de marché.

![Appels d’API lors de la mise à jour est lancée à partir de la place de marché.](./media/saas-update-api-v2-calls-from-marketplace-a.png)

#### <a name="provisioning-for-update"></a>Mise en service de mise à jour  
(à partir de service SaaS) 

Le diagramme suivant montre les actions lorsqu’une mise à jour est lancée par le service SaaS. (L’appel de webhook est remplacée par une mise à jour à l’abonnement initié par le SaaS Service. 

![Appels d’API lors de la mise à jour est lancée par le service SaaS.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Interrompu

Cet état indique que le paiement d’un client n’a pas été reçu. Par la stratégie, nous fournirons au client une période de grâce avant unfulfilling l’abonnement. Quand un abonnement est dans cet état : 

- En tant qu’ISV, vous pouvez choisir à se dégrader ou bloquer l’accès de l’utilisateur au service. 
- L’abonnement doit être conservée dans un état récupérable qui peut restaurer toutes les fonctionnalités sans perte de données ou de paramètres. 
- Vous pouvez vous attendre à obtenir une demande de réactivation pour cet abonnement par le biais de l’API de traitement ou une demande de mise hors service à la fin de la période de grâce. 

#### <a name="unsubscribed"></a>Désabonné 

Abonnements atteint cet état en réponse à une demande client explicite ou en réponse à non-paiement des droits d’inscription. L’attente de l’éditeur de logiciels est que les données du client sont conservées pour la récupération sur demande pour un minimum de X jours, puis supprimées. 


## <a name="api-reference"></a>Informations de référence sur l'API

Cette section documente le SaaS *abonnement API* et *opérations API*.  La valeur de la `api-version` API de paramètre pour la version 2 sont `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Définitions de paramètre et d’entité

Le tableau suivant répertorie les définitions pour les entités utilisées par les API de traitement des commandes et paramètres courants.

|     Paramètre d’entité     |     Définition                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Identificateur GUID pour une ressource de SaaS  |
| `name`                   | Nom convivial fourni pour cette ressource par le client |
| `publisherId`            | Identificateur de chaîne unique pour chaque serveur de publication, par exemple « contoso » |
| `offerId`                | Identificateur de chaîne unique pour chaque offre, par exemple « offer1 »  |
| `planId`                 | Identificateur de chaîne unique pour chaque plan/référence (SKU), par exemple « silver » |
| `operationId`            | Identificateur GUID pour une opération particulière  |
|  `action`                | L’action effectuée sur une ressource, soit `subscribe`, `unsubscribe`, `suspend`, `reinstate`, ou `changePlan`, `changeQuantity`, `transfer`  |
|   |   |

Identificateurs globaux uniques ([GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) sont des nombres (hexadécimal 32) 128 bits généralement générés automatiquement. 

#### <a name="resolve-a-subscription"></a>Résoudre un abonnement 

Le point de terminaison résolution permet à l’éditeur résoudre un jeton de la place de marché à un ID de ressource persistante. L’ID de ressource est l’identificateur unique de l’abonnement SAAS.  Lorsqu’un utilisateur est redirigé vers le site Web d’un éditeur de logiciels indépendant, les paramètres de requête figurant dans l’URL contiennent un jeton. L’éditeur de logiciels indépendant doit utiliser ce jeton et effectuer une requête pour le résoudre. La réponse contient l’ID d’abonnement SAAS unique, le nom, l’ID d’offre et le plan associé à la ressource. Ce jeton est valide pendant une heure uniquement. 

**Publier :<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`**

*Paramètres de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Version de l’opération à utiliser pour cette demande  |

*En-têtes de requête :*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Valeur de chaîne unique pour le suivi de la demande du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  x-ms-correlationid |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre met en corrélation tous les événements à partir de l’opération du client avec des événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  autorisation     |  [Obtenir un jeton PORTEUR JSON web token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |
|  x-ms-marketplace-token  |  Paramètre de requête de jeton dans l’URL lorsque l’utilisateur est redirigé vers site Web de l’éditeur de logiciels SaaS à partir d’Azure (par exemple : `https://contoso.com/signup?token=..`). *Remarque :* L’URL décode la valeur du jeton à partir du navigateur avant de l’utiliser.  |

*Codes de réponse :*

Code : 200<br>
Résout le jeton opaque à un abonnement de SaaS.<br>

```json
Response body:
{
    "subscriptionId": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Code : 404<br>
Introuvable

Code : 400<br>
Demande incorrecte. x-ms-marketplace-token est manquant, incorrect ou expiré.

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide, ou la demande tente d’accéder une acquisition qui n’appartient pas au serveur de publication en cours.

Code : 500<br>
Erreur interne du serveur

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>API d’abonnement

L’API de l’abonnement prend en charge les opérations HTTPS suivantes : **Obtenir**, **Post**, **correctif**, et **supprimer**.


#### <a name="list-subscriptions"></a>Liste des abonnements

Répertorie tous les abonnements de SaaS pour un serveur de publication.

**Télécharger :<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`**

*Paramètres de requête :*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  Version de l’opération à utiliser pour cette requête.  |

*En-têtes de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Valeur de chaîne unique pour le suivi de la demande du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| x-ms-correlationid |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre met en corrélation tous les événements à partir de l’opération du client avec des événements côté serveur. Si cette valeur n’est pas fournie, une sera générée et fournie dans les en-têtes de réponse.  |
| autorisation      |  [Obtenir un jeton PORTEUR JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Codes de réponse :*

Code : 200 <br/>
Selon le jeton d’authentification, obtenir le serveur de publication et les abonnements correspondants pour les offres de tous les l’éditeur.<br> Charge utile de réponse :<br>

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
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. [Provisioning, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

Le jeton de continuation sera uniquement présent s’il existe plus « pages » de plans pour récupérer. 

Code : 403 <br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide, ou la demande tente d’accéder une acquisition qui n’appartient pas au serveur de publication en cours. 

Code : 500 Erreur interne du serveur

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>S’abonner

Obtient l’abonnement spécifié de SaaS. Utilisez cet appel pour obtenir des informations de licence et informations de plan.

**Télécharger :<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`**

*Paramètres de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Identificateur unique de l’abonnement SaaS obtenu après avoir résolu le jeton via l’API résoudre   |
|  ApiVersion        |   Version de l’opération à utiliser pour cette demande   |

*En-têtes de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Valeur de chaîne unique pour le suivi de la demande du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  x-ms-correlationid |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre met en corrélation tous les événements à partir de l’opération du client avec des événements côté serveur. Si cette valeur n’est pas fournie, une sera générée et fournie dans les en-têtes de réponse.  |
|  autorisation     |  [Obtenir un jeton PORTEUR JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Codes de réponse :*

Code : 200<br>
Obtient l’abonnement de SaaS à partir de l’identificateur<br> Charge utile de réponse :<br>

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
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

Code : 404<br>
Introuvable<br> 

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide, ou la demande tente d’accéder une acquisition qui n’appartient pas au serveur de publication en cours.

Code : 500<br>
Erreur interne du serveur<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Liste des plans disponibles

Utilisez cet appel pour déterminer s’il y a aucune offre publique/privée pour le serveur de publication en cours.

**Télécharger :<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`**

*Paramètres de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Version de l’opération à utiliser pour cette demande  |

*En-têtes de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Valeur de chaîne unique pour le suivi de la demande du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  x-ms-correlationid  | Valeur de chaîne unique pour l’opération sur le client. Ce paramètre met en corrélation tous les événements à partir de l’opération du client avec des événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  autorisation     |  [Obtenir un jeton PORTEUR JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*Codes de réponse :*

Code : 200<br>
Obtenir la liste des plans disponibles pour un client.<br>

Corps de réponse :

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
Introuvable<br> 

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide, ou la demande tente d’accéder une acquisition qui n’appartient pas au serveur de publication en cours. <br> 

Code : 500<br>
Erreur interne du serveur<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Activer un abonnement

**Publier :<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`**

*Paramètres de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Version de l’opération à utiliser pour cette demande  |
| subscriptionId     | Identificateur unique de l’abonnement de SaaS est obtenu après avoir résolu le jeton à l’aide de l’API résoudre  |

*En-têtes de requête :*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Valeur de chaîne unique pour le suivi de la demande du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  x-ms-correlationid  | Valeur de chaîne unique pour l’opération sur le client. Cette chaîne met en corrélation tous les événements à partir de l’opération du client avec des événements côté serveur. Si cette valeur n’est pas fournie, une sera générée et fournie dans les en-têtes de réponse.  |
|  autorisation     |  [Obtenir un jeton PORTEUR JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*Requête :*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Codes de réponse :*

Code : 200<br>
Active l’abonnement.<br>

Code : 404<br>
Introuvable

Code : 400<br>
Échecs de Validation de demande incorrect

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide, ou la demande tente d’accéder une acquisition qui n’appartient pas au serveur de publication en cours.

Code : 500<br>
Erreur interne du serveur

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

**Correctif :<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*Paramètres de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Version de l’opération à utiliser pour cette requête.  |
| subscriptionId     | Identificateur unique de l’abonnement de SaaS est obtenu après avoir résolu le jeton à l’aide de l’API résoudre.  |

*En-têtes de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  x-ms-correlationid  |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre met en corrélation tous les événements à partir de l’opération du client avec des événements côté serveur. Si cette valeur n’est pas fournie, une sera générée et fournie dans les en-têtes de réponse.    |
| autorisation      |  [Obtenir un jeton PORTEUR JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Charge utile de demande :*

```json
Request Body:
{
    "planId": "gold"
}
```

*En-têtes de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Lier à une ressource pour obtenir l’état de l’opération.   |

*Codes de réponse :*

Code : 202<br>
La demande de modification de plan a été acceptée. Les éditeurs de logiciels indépendants doit interroger l’opération-Location pour déterminer la réussite ou l’échec. <br>

Code : 404<br>
Introuvable

Code : 400<br>
Échecs de Validation de demande incorrecte.

>[!Note]
>Seul un plan ou une quantité peut être corrigée en même temps, mais pas les deux. Modifie un abonnement avec **mise à jour** n’est pas dans `allowedCustomerOperations`.

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide, ou la demande tente d’accéder une acquisition qui n’appartient pas au serveur de publication en cours.

Code : 500<br>
Erreur interne du serveur

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-quantity-on-the-subscription"></a>Modifier la quantité sur l’abonnement

Mettre à jour la quantité sur l’abonnement.

**Correctif :<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*Paramètres de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Version de l’opération à utiliser pour cette requête.  |
| subscriptionId     | Identificateur unique de l’abonnement de SaaS est obtenu après avoir résolu le jeton à l’aide de l’API résoudre.  |

*En-têtes de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  x-ms-correlationid  |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre met en corrélation tous les événements à partir de l’opération du client avec des événements côté serveur. Si cette valeur n’est pas fournie, une sera générée et fournie dans les en-têtes de réponse.    |
| autorisation      |  [Obtenir un jeton PORTEUR JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Charge utile de demande :*

```json
Request Body:
{
    "quantity": 5
}
```

*En-têtes de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Lier à une ressource pour obtenir l’état de l’opération.   |

*Codes de réponse :*

Code : 202<br>
Accepté. La demande de modification de quantité a été acceptée. Les éditeurs de logiciels indépendants doit interroger l’opération-Location pour déterminer la réussite ou l’échec. <br>

Code : 404<br>
Introuvable

Code : 400<br>
Échecs de Validation de demande incorrecte.

>[!Note]
>Seul un plan ou une quantité peut être corrigée en même temps, mais pas les deux. Modifie un abonnement avec **mise à jour** n’est pas dans `allowedCustomerOperations`.

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide, ou la demande tente d’accéder une acquisition qui n’appartient pas au serveur de publication en cours.

Code : 500<br>
Erreur interne du serveur

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="delete-a-subscription"></a>Supprimer un abonnement

Annuler l’abonnement et de supprimer l’abonnement spécifié.

**Supprimer :<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`**

*Paramètres de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Version de l’opération à utiliser pour cette requête.  |
| subscriptionId     | Identificateur unique de l’abonnement de SaaS est obtenu après avoir résolu le jeton à l’aide de l’API résoudre.  |

*En-têtes de requête :*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une sera générée et fournie dans les en-têtes de réponse.   |
|  x-ms-correlationid  |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre met en corrélation tous les événements à partir de l’opération du client avec des événements côté serveur. Si cette valeur n’est pas fournie, une sera générée et fournie dans les en-têtes de réponse.   |
|  autorisation     |  [Obtenir un jeton PORTEUR JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Codes de réponse :*

Code : 202<br>
Appel initié par des éditeurs de logiciels pour indiquer vous désabonner d’un abonnement de SaaS.<br>

Code : 404<br>
Introuvable

Code : 400<br>
Supprimer un abonnement avec **supprimer** pas dans `allowedCustomerOperations`.

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide, ou la demande tente d’accéder une acquisition qui n’appartient pas au serveur de publication en cours.

Code : 500<br>
Erreur interne du serveur

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>Opérations d’API

L’API d’opérations prend en charge les opérations suivantes de correctifs et Get.

#### <a name="list-outstanding-operations"></a>Liste des opérations en attente 

Répertorie les opérations en attente pour le serveur de publication en cours. 

**Télécharger :<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`**

*Paramètres de requête :*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   Version de l’opération à utiliser pour cette requête.                |
| subscriptionId     | Identificateur unique de l’abonnement de SaaS est obtenu après avoir résolu le jeton à l’aide de l’API résoudre.  |

*En-têtes de requête :*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  x-ms-correlationid |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre met en corrélation tous les événements à partir de l’opération du client avec des événements côté serveur. Si cette valeur n’est pas fournie, une sera générée et fournie dans les en-têtes de réponse.  |
|  autorisation     |  [Obtenir un jeton PORTEUR JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Codes de réponse :*

Code : 200<br> Obtient la liste des opérations en attente sur un abonnement.<br>
Charge utile de réponse :

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

Code : 404<br>
Introuvable

Code : 400<br>
Échecs de Validation de demande incorrect

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide, ou la demande tente d’accéder une acquisition qui n’appartient pas au serveur de publication en cours.

Code : 500<br>
Erreur interne du serveur

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Obtenir l’état de l’opération

Permet à l’éditeur effectuer le suivi de l’état de l’opération asynchrone déclenchées spécifié (s’abonner / annuler l’abonnement / Modifier planification / Modifier quantité).

**Télécharger :<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Paramètres de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Version de l’opération à utiliser pour cette requête.  |

*En-têtes de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  x-ms-correlationid |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre met en corrélation tous les événements à partir de l’opération du client avec des événements côté serveur. Si cette valeur n’est pas fournie, une sera générée et fournie dans les en-têtes de réponse.  |
|  autorisation     |[Obtenir un jeton PORTEUR JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Codes de réponse :* Code : 200<br> Obtient le texte spécifié en attente de l’opération de SaaS<br>
Charge utile de réponse :

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

Code : 404<br>
Introuvable

Code : 400<br>
Échecs de Validation de demande incorrect

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide, ou la demande tente d’accéder une acquisition qui n’appartient pas au serveur de publication en cours.
 
Code : 500<br> Erreur interne du serveur

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Mettre à jour l’état d’une opération

Mettre à jour l’état d’une opération pour indiquer la réussite/échec avec les valeurs fournies.

**Correctif :<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Paramètres de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  Version de l’opération à utiliser pour cette requête.  |
| subscriptionId     | Identificateur unique de l’abonnement de SaaS est obtenu après avoir résolu le jeton à l’aide de l’API résoudre.  |
|  operationId       | L’opération en cours. |

*En-têtes de requête :*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  x-ms-correlationid |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre met en corrélation tous les événements à partir de l’opération du client avec des événements côté serveur. Si cette valeur n’est pas fournie, une sera générée et fournie dans les en-têtes de réponse. |
|  autorisation     |  [Obtenir un jeton PORTEUR JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Charge utile de demande :*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Codes de réponse :*

Code : 200<br> L’appel à informer de la fin d’une opération sur le côté de l’éditeur de logiciels indépendant. Par exemple, cette réponse pouvant signaler la modification des sièges/plans.

Code : 404<br>
Introuvable

Code : 400<br>
Échecs de Validation de demande incorrect

Code : 403<br>
Non autorisé. Le jeton d’authentification n’a pas été fourni, n’est pas valide, ou la demande tente d’accéder une acquisition qui n’appartient pas au serveur de publication en cours.

Code : 409<br>
Conflit. Par exemple, une transaction la plus récente est déjà remplie

Code : 500<br> Erreur interne du serveur

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="webhook-on-the-saas-service"></a>Webhook sur le service SaaS

Le serveur de publication doit implémenter un webhook dans ce service SaaS pour informer les utilisateurs des changements dans son service. L’API est censée être non authentifié et sera appelée par le service Microsoft SaaS. Le service SaaS est censé appeler les opérations API pour valider et à autoriser avant d’entreprendre une action sur la notification de webhook.

```json
{
  "id": "<this is a Guid operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher’s name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Où action peut prendre l’une d'entre elles : 
- `Subscribe`, (Lorsque la ressource a été activée)
- `Unsubscribe`, (Lorsque la ressource a été supprimée)
- `ChangePlan`, (Lorsque l’opération de plan de modification terminée)
- `ChangeQuantity`, (Lorsque l’opération de quantité de modification terminée),
- `Suspend`, (Lorsque les ressources a été suspendu)
- `Reinstate`, (Lorsque ressource a été rétablie après suspension)

Où état peut prendre l’une d'entre elles : <br>
        -NotStarted, <br>
        -En cours, <br>
        -A réussi, <br>
        -Échec, <br>
        -Conflit <br>

Les États actionnables sont Succeeded et Failed dans une notification webhook. Cycle de vie d’une opération consiste à partir de NotStarted à un état terminal comme réussite/échec/conflit. Si vous recevez n’a ne pas démarré ou en cours, continuez à demander l’état via les API d’opération GET jusqu'à ce que l’opération atteigne un état terminal avant toute action. 

## <a name="mock-api"></a>API factice

Vous pouvez utiliser nos API factice pour vous aider à bien démarrer avec le développement, en particulier le prototypage, et les projets de test. 

Point de terminaison hôte : `https://marketplaceapi.microsoft.com/api` <br/>
Version de l’API : `2018-09-15` <br/>
Aucune authentification requise <br/>
Uri de l’exemple : `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Les chemins d’accès du point de terminaison API sont les mêmes sur le simulacre et API réelle, mais les versions d’API sont différentes. La version est 2018-09-15 pour le simulacre et 2018-08-31 pour la version de production. 

Les appels d’API dans cet article peut devenir au point de terminaison hôte factice. Vous pouvez vous attendre à obtenir des données fictives comme réponse. En règle générale, vous pouvez vous attendre obtenir des données fictives comme réponse. Appels aux méthodes d’abonnement de mise à jour sur l’API factice retournent toujours 500. 

## <a name="next-steps"></a>Étapes suivantes

Les développeurs peuvent également récupérer par programme et les profils de manipulation de charges de travail, des offres et serveur de publication à l’aide de la [API REST de Cloud partenaire portail](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
