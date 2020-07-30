---
title: API de traitement SaaS v2 dans la Place de marché commerciale Microsoft
description: Découvrez comment créer et gérer une offre SaaS sur Microsoft AppSource et la Place de marché Azure à l’aide des API de traitement version 2.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: f40da30ff0d702078861367dea810cc8ca1ab91b
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87305140"
---
# <a name="saas-fulfillment-apis-version-2-in-microsoft-commercial-marketplace"></a>API de traitement SaaS version 2 dans la Place de marché commerciale Microsoft

Cet article décrit les API qui permettent aux partenaires de vendre leurs offres SaaS dans la Microsoft AppSource et la Place de marché Azure. Un éditeur doit implémenter l’intégration avec ces API pour publier une offre SaaS négociable dans l’Espace partenaires.

## <a name="managing-the-saas-subscription-life-cycle"></a>Gestion du cycle de vie des abonnements SaaS

La Place de marché Azure gère l’ensemble du cycle de vie d’un abonnement SaaS suite à son achat par le client final.  Elle utilise la page d’accueil, les API de traitement, les API d’opérations et le webhook comme mécanisme pour piloter l’activation et l’utilisation de l’abonnement SaaS, ainsi que les mises à jour et l’annulation de l’abonnement.  La facture du client final se base sur l’état de l’abonnement SaaS managé par Microsoft. 

### <a name="states-of-a-saas-subscription"></a>États d’un abonnement SaaS

Les états d’un abonnement SaaS et les actions applicables s’affichent.

![Cycle de vie d’un abonnement SaaS dans la Place de marché](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Acheté mais pas encore activé (*PendingFulfillmentStart*)

Lorsqu’un client final (ou CSP) a acheté une offre SaaS sur la Place de marché, l’éditeur doit être informé de l’achat afin de pouvoir créer et configurer un nouveau compte SaaS pour le client final côté éditeur.

Pour la création du compte :

1. Le client doit cliquer sur le bouton **Configurer** disponible pour une offre SaaS après son achat dans Microsoft AppSource ou sur le portail Azure. Ou dans l’e-mail que le client recevra peu après l’achat.
2. Microsoft avertit ensuite le partenaire de l’achat en ouvrant, dans le nouvel onglet de navigateur, l’URL de la page d’accueil avec le paramètre de jeton (le jeton d’identification d’achat de la Place de marché).

Un exemple de ce type d’appel est `https://contoso.com/signup?token=<blob>`, tandis que l’URL de la page d’accueil de cette offre SaaS dans l’Espace partenaires est configurée comme `https://contoso.com/signup`. Ce jeton fournit à l’éditeur un ID qui identifie de façon unique l’achat SaaS et le client.

>[!NOTE]
>L’éditeur n’est pas informé de l’achat SaaS tant que le client n’a pas initié le processus de configuration côté Microsoft.

L’URL de la page d’accueil doit être disponible 24 h/24 et 7 j/7 et prête à recevoir à tout moment de nouveaux appels de Microsoft. Si la page d’accueil n’est plus disponible, les clients ne pourront pas s’inscrire au service SaaS et commencer à l’utiliser.

Le *jeton* doit ensuite être renvoyé à Microsoft à partir de l’éditeur en appelant l’[API de résolution SaaS](#resolve-a-purchased-subscription), en tant que valeur du paramètre d’en-tête `x-ms-marketplace-token header`.  Après l’appel de l’API de résolution, le jeton est échangé pour recevoir les détails de l’achat SaaS, notamment l’ID unique de l’achat, l’ID de l’offre achetée, l’ID du plan acheté, etc.

Sur la page d’accueil, le client doit être connecté au compte SaaS nouveau ou existant via l’authentification unique (SSO) Azure Active Directory (AAD).

L’éditeur doit implémenter la connexion SSO pour fournir l’expérience utilisateur requise par Microsoft pour ce processus.  Veillez à utiliser l’application Azure AD mutualisée et à autoriser les comptes professionnels et scolaires ou les comptes Microsoft personnels lors de la configuration de l’authentification unique.  Cette exigence s’applique uniquement à la page d’accueil et aux utilisateurs redirigés vers le service SaaS lorsqu’ils sont déjà connectés avec des informations d’identification Microsoft. Elle ne s’applique pas à toutes les connexions au service SaaS.

> [!NOTE]
>Si la connexion SSO requiert qu’un administrateur accorde une autorisation à une application, la description de l’offre dans l’Espace partenaires doit autoriser cet accès requis au niveau de l’administrateur. Cette procédure est nécessaire pour se conformer aux [stratégies de certification de Place de marché Azure](https://docs.microsoft.com/legal/marketplace/certification-policies#10003-authentication-options).

Une fois connecté, le client doit effectuer la configuration SaaS côté éditeur. L’éditeur doit ensuite appeler [Activer l’API d’abonnement](#activate-a-subscription) pour envoyer un signal à Place de marché indiquant que le provisionnement du compte SaaS est terminé.
Cette étape démarre le cycle de facturation du client. Si l’appel de l’API Activer l’abonnement échoue, le client n’est pas facturé pour l’achat.


![Appels d’API pour un scénario de provisionnement](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>Actif (abonné)

Cet état est l’état stable d’un abonnement SaaS provisionné. Une fois l'appel de l’[API Activer l’abonnement](#activate-a-subscription) traité côté Microsoft, l’abonnement SaaS est marqué comme étant Abonné. Le service SaaS est maintenant prêt à être utilisé par le client côté éditeur, et le client est alors facturé.

Si l’abonnement SaaS est déjà actif et que le client choisit de **gérer** l’expérience SaaS à partir du portail Azure ou du centre d’administration M365, l’**URL de la page d’accueil** est de nouveau appelée par Microsoft avec le paramètre de *jeton*, comme dans le processus d’activation.  L’éditeur doit faire la distinction entre les nouveaux achats et la gestion de comptes SaaS existants, et gérer cet appel d’URL de page d’accueil en conséquence.

#### <a name="being-updated-subscribed"></a>En cours de mise à jour (abonné)

Cette action signifie qu’une mise à jour d’un abonnement SaaS actif existant est traitée à la fois par Microsoft et par l’éditeur. Une telle mise à jour peut être lancée par :

* le client à partir de la Place de marché
* le CSP à partir de la Place de marché
* le client à partir du site SaaS de l’éditeur (ne s’applique pas aux achats CSP effectués)

Deux types de mises à jour sont disponibles pour un abonnement SaaS :

1. Mettre à jour le plan lorsque le client choisit un autre plan pour l’abonnement.
1. Mettre à jour la quantité lorsque le client modifie le nombre de postes achetés pour l’abonnement

Un seul abonnement actif peut être mis à jour. Pendant la mise à jour de l’abonnement, son état reste actif côté Microsoft.

##### <a name="update-initiated-from-the-marketplace"></a>Mise à jour lancée à partir de la Place de marché

Dans ce processus, le client modifie le plan d’abonnement ou le nombre de postes à partir du centre d’administration M365.  

1. Lorsqu’une mise à jour est entrée, Microsoft appelle l’URL du webhook de l’éditeur, configurée dans le champ **Webhook de connexion** de l’Espace partenaires, avec une valeur appropriée pour l’*action* et d’autres paramètres pertinents.  
1. Le côté éditeur doit apporter les modifications requises au service SaaS et notifier Microsoft lorsque la modification est terminée en appelant l’[API Mettre à jour l’état de l’opération](#update-the-status-of-an-operation).
1. Si le correctif est envoyé avec l’état d’échec, le processus de mise à jour ne sera pas effectué côté Microsoft.  L’abonnement SaaS conservera le plan et la quantité de postes existants.

La séquence d’appels d’API pour un scénario de mise à jour initié par la Place de marché est illustrée ci-dessous.

![Appels d’API pour une mise à jour lancée par la Place de marché](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>Mise à jour lancée à partir de l’éditeur

Dans ce processus, le client modifie le plan d’abonnement ou le nombre de postes directement à partir du service SaaS. 

1. Le code de l’éditeur doit appeler l’[API Modifier le plan](#change-the-plan-on-the-subscription) et/ou l’[API Modifier la quantité](#change-the-quantity-of-seats-on-the-saas-subscription) avant d’effectuer la modification demandée côté éditeur. 

1. Microsoft appliquera la modification à l’abonnement, puis notifiera l’éditeur via **Webhook de connexion** pour appliquer la même modification.  

1. Seul l’éditeur doit apporter la modification requise à l’abonnement SaaS, puis notifier Microsoft lorsque la modification est terminée en appelant l’[API Mettre à jour l’état de l’opération](#update-the-status-of-an-operation).

Séquence des appels d’API pour le scénario de mise à jour initié côté éditeur.

![Appels d’API pour une mise à jour initiée par l’éditeur](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>Interrompu (*Interrompu*)

Cet état indique que le paiement d’un client n’a pas été reçu pour le service SaaS. L’éditeur sera informé du changement de l’état de l’abonnement SaaS par Microsoft. La notification est effectuée via un appel au webhook avec le paramètre *action* défini sur *Interrompu*.

L’éditeur peut choisir d’apporter ou non des modifications au service SaaS côté éditeur. Nous recommandons que l’éditeur mette ces informations à la disposition du client suspendu et limite ou bloque l’accès du client au service SaaS.  Il existe une probabilité que le paiement ne soit jamais reçu.

Microsoft accorde au client une période de grâce de 30 jours avant l’annulation automatique de l’abonnement. Quand un abonnement affiche l’état Interrompu :

* Le compte SaaS doit être conservé dans un état récupérable par l’éditeur de logiciels indépendant (ISV). La fonctionnalité complète peut être restaurée sans aucune perte de données ou de paramètres.
* Attendez-vous à recevoir une demande de réactivation de cet abonnement si le paiement est reçu au cours de la période de grâce, ou une demande de déprovisionnement à la fin de la période de grâce, chaque fois via le mécanisme webhook.

L’état de l’abonnement affiche Interrompu côté Microsoft tant que l’éditeur n’a pas effectué une action. Seuls les abonnements actifs peuvent être interrompus.

#### <a name="reinstated-suspended"></a>Réactivé (*Interrompu*)

L’abonnement est en cours de réactivation.

Cette action indique que l’instrument de paiement du client est redevenu valide et qu’un paiement est effectué pour l’abonnement SaaS.  L’abonnement est en cours de réactivation. Dans ce cas : 

1. Microsoft appelle webhook avec un paramètre *action* défini sur la valeur *Reinstate* (Réactiver).  
1. L’éditeur s’assure que cet abonnement est entièrement opérationnel de son côté.
1. L’éditeur appelle l’[API Opération Patch](#update-the-status-of-an-operation) avec un état de réussite.  
1. La réactivation sera réussie et le client sera de nouveau facturé pour l’abonnement SaaS. 
1. Si le correctif est envoyé avec l’état d’échec, le processus de réactivation ne sera pas effectué côté Microsoft. L’abonnement restera à l’état Interrompu.

Si le correctif est envoyé avec l’état d’échec, le processus de réactivation ne sera pas effectué côté Microsoft.  L’abonnement restera à l’état Interrompu.

Seul un abonnement Interrompu peut être réactivé.  Lorsqu’un abonnement SaaS est réactivé, son état affiche toujours Interrompu.  Une fois cette opération terminée, l’état de l’abonnement affiche Active.

#### <a name="renewed-subscribed"></a>Renouvelé (*Abonné*)

À la fin de la période d’abonnement (après un mois ou une année), l’abonnement SaaS est automatiquement renouvelé par Microsoft.  La valeur par défaut du paramètre de renouvellement automatique est *true* pour tous les abonnements SaaS. Les abonnements SaaS actifs continuent d’être renouvelés à un rythme régulier. Microsoft n’informe pas l’éditeur lorsqu’un abonnement est en cours de renouvellement. Un client peut désactiver le renouvellement automatique d’un abonnement SaaS via le portail d’administration M365 ou le portail Azure.  Dans ce cas, l’abonnement SaaS sera automatiquement annulé à la fin de la période de facturation actuelle.  Les clients peuvent également annuler l’abonnement SaaS à tout moment.

Seuls les abonnements actifs sont automatiquement renouvelés.  Les abonnements restent actifs pendant le processus de renouvellement et si le renouvellement automatique a échoué.  Après le renouvellement, les dates de début et de fin de la période d’abonnement seront mises à jour selon les dates du nouveau terme.

Si un renouvellement automatique échoue en raison d’un problème de paiement, l’abonnement affiche l’état Interrompu.  L’éditeur en sera averti.

#### <a name="canceled-unsubscribed"></a>Annulé (*Désabonné*) 

Les abonnements atteignent cet état en réponse à une action explicite de la part du client ou CSP par annulation d’un abonnement depuis le site de l’éditeur, le portail Azure ou le centre d’administration M365.  Un abonnement peut également être annulé implicitement en raison d’un défaut de paiement de la cotisation, après avoir été à l’état Interrompu pendant 30 jours.

Lors de la réception d’un appel webhook d’annulation, l’éditeur doit conserver les données client pour la récupération à la demande pendant au moins sept jours. Les données client pourront uniquement être supprimées après ce délai.

Un abonnement SaaS peut être annulé à tout moment durant son cycle de vie. Une fois annulé, un abonnement ne peut pas être réactivé.

## <a name="api-reference"></a>Informations de référence sur l'API

Cette section documente l’API Subscription et l’API Operations SaaS.

Les **API d’abonnement** doivent être utilisées pour gérer le cycle de vie de l’abonnement SaaS, de l’achat à l’annulation.

Les **API d’opérations** doivent être utilisées pour :

* vérifier et accuser réception des appels webhook traités
* obtenir la liste des applications comportant des opérations en attente d’accusé de réception par l’éditeur

### <a name="enforcing-tls-12-note"></a>Note concernant la mise en œuvre de TLS 1.2

La version 1.2 de TLS sera bientôt mise en œuvre en tant que version minimale pour les communications HTTPS. Veillez à utiliser cette version de TLS dans votre code.  Les versions 1.0 et 1.1 de TLS seront bientôt obsolètes.

### <a name="subscription-apis"></a>API d’abonnement

#### <a name="resolve-a-purchased-subscription"></a>Résoudre un abonnement acheté

Le point de terminaison de résolution permet à l’éditeur d’échanger le jeton d’identification d’achat de la Place de marché (appelé *jeton* dans [Acheté mais pas encore activé](#purchased-but-not-yet-activated-pendingfulfillmentstart)) à un ID d’abonnement SaaS acheté de manière permanente et ses détails.

Lorsqu’un client est redirigé vers l’URL de la page d’accueil du partenaire, le jeton d’identification du client est transmis comme paramètre *jeton* dans cet appel d’URL. Le partenaire doit utiliser ce jeton et effectuer une requête pour le résoudre. La réponse de l’API de résolution contient l’ID d’abonnement SaaS et d’autres détails permettant d’identifier l’achat de manière unique. Le *jeton* fourni avec l’appel d’URL de la page d’accueil est généralement valide 24 heures. Si le *jeton* que vous recevez a déjà expiré, nous vous recommandons de fournir les instructions suivantes au client final :

« Nous n’avons pas pu identifier cet achat. Veuillez rouvrir cet abonnement SaaS dans le portail Azure ou dans le Centre d’administration M365, puis cliquer à nouveau sur le bouton « Configurer un compte » ou « Gérer un compte ». »

L’appel de l’API de résolution renverra les détails de l’abonnement et l’état des abonnements SaaS dans tous les états pris en charge.

##### <a name="posthttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Utilisez 2018-08-31.   |

*En-têtes de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  `x-ms-correlationid` |  Valeur de chaîne unique pour l’opération sur le client. Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `authorization`     |  Jeton d’accès unique qui identifie le serveur de publication qui effectue cet appel d’API. Le format est `"Bearer <accessaccess_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |
|  `x-ms-marketplace-token`  | Le paramètre *jeton* d’identification d’achat de la Place de marché à résoudre.  Le jeton est transmis dans l’appel de l’URL de la page d’accueil lorsque le client est redirigé vers le site Web du partenaire SaaS (par exemple : `https://contoso.com/signup?token=<token><authorization_token>`). <br> <br>  *Remarque :* La valeur codée du *jeton*  fait partie de l’URL de la page d’accueil et doit donc être décodée avant d’être utilisée en tant que paramètre dans cet appel d’API.  <br> <br> L’exemple d’une chaîne codée dans l’URL ressemble à ceci : `contoso.com/signup?token=ab%2Bcd%2Fef`, où le jeton est `ab%2Bcd%2Fef`.  Le même jeton décodé sera : `Ab+cd/ef` |
| | |

*Codes de réponse :*

Code : 200 Retourne des identificateurs d’abonnement SaaS uniques en fonction de la valeur `x-ms-marketplace-token` fournie.

Exemple de corps de réponse :

```json
{
    "id": "<guid>",  // purchased SaaS subscription ID 
    "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name 
    "offerId": "offer1", // purchased offer ID
    "planId": "silver", // purchased offer's plan ID
    "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
    "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
"pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
"pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
                   startDate": "2019-05-31", 
   "endDate": "2019-06-29",
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": [
      "Delete",
      "Update",
      "Read"
    ],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

Code : 400 Requête incorrecte. `x-ms-marketplace-token` est manquant, incorrect, non valide ou arrivé à expiration.

Code : 403 Interdit. Le jeton d’autorisation n’est pas valide, a expiré ou n’est pas fourni.  La demande tente d’accéder à un abonnement SaaS pour une offre publiée avec un ID d’application Azure AD différent de celui utilisé pour créer le jeton d’autorisation.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte.

Code : 500 Erreur interne du serveur.  Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="activate-a-subscription"></a>Activer un abonnement

Une fois le compte SaaS configuré pour un client final, l’éditeur doit appeler l’API Activer l’abonnement côté Microsoft.  Le client ne sera facturé que si cet appel d’API réussit.

##### <a name="posthttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Post`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Utilisez 2018-08-31.   |
| `subscriptionId` | Identificateur unique de l’abonnement SaaS acheté.  Cet ID est obtenu après résolution du jeton d’autorisation de la Place de marché à l’aide de l’[API de résolution](#resolve-a-purchased-subscription).
 |

*En-têtes de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| `x-ms-correlationid` |  Valeur de chaîne unique pour l’opération sur le client.  Cette chaîne sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| `authorization`      |  Jeton d’accès unique qui identifie le serveur de publication qui effectue cet appel d’API. Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Exemple de demande de charge utile :*

```json
{ // needed for validation of the activation request
    "planId": "gold", // purchased plan, cannot be empty
    "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*Codes de réponse :*

Code : 200 L’abonnement a été marqué comme Abonné côté Microsoft.

Il n’y a aucun corps de réponse pour cet appel.

Code : 400 Demande incorrecte : échec de la validation.

* `planId` n’existe pas dans la charge utile de la demande.
* `planId` dans la charge utile de la demande ne correspond pas à l’achat effectué.
* `quantity` dans la charge utile de la demande ne correspond pas à l’achat effectué
* L’abonnement SaaS affiche l’état Abonné ou Interrompu.

Code : 403 Interdit. Le jeton d’autorisation n’est pas valide, a expiré ou n’est pas fourni. La demande tente d’accéder à un abonnement SaaS pour une offre publiée avec un ID d’application Azure AD différent de celui utilisé pour créer le jeton d’autorisation.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte.

Code : 404 Introuvable. L’abonnement SaaS affiche l’état Désabonné.

Code : 500 Erreur interne du serveur.  Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-list-of-all-subscriptions"></a>Obtenir la liste de tous les abonnements

Récupère la liste de tous les abonnements SaaS achetés pour toutes les offres publiées par l’éditeur dans la Place de marché.  Les abonnements SaaS avec tous les états possibles sont retournés. Les abonnements SaaS désabonnés sont également retournés car ces informations ne sont pas supprimées côté Microsoft.

Cette API renvoie des résultats paginés. La taille de la page est 100.

##### <a name="gethttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Utilisez 2018-08-31.  |
| `continuationToken`  | Paramètre facultatif. Pour récupérer la première page de résultats, laissez vide.  Utilisez la valeur retournée dans le paramètre `@nextLink` pour récupérer la page suivante. |

*En-têtes de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| `x-ms-correlationid` |  Valeur de chaîne unique pour l’opération sur le client.  Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| `authorization`      |  Jeton d’accès unique qui identifie le serveur de publication qui effectue cet appel d’API.  Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Codes de réponse :*

Code : 200 Retourne la liste de tous les abonnements existants pour toutes les offres de cet éditeur, en fonction du jeton d’autorisation de l’éditeur.

*Exemple de corps de réponse :*

```json
{
  "subscriptions": [
      {
          "id": "<guid>", // purchased SaaS subscription ID
          "name": "Contoso Cloud Solution", // SaaS subscription name
          "publisherId": "contoso", // publisher ID
          "offerId": "offer1", // purchased offer ID
          "planId": "silver", // purchased plan ID
          "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
          "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
              "emailId": " test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
          "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
              "emailId": " test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
            "term": { // The period for which the subscription was purchased. 
                "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
                "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
                "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
          },
          "allowedCustomerOperations": [
              "Read", "Update", "Delete" 
          ], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
          "sessionMode": "None", // not relevant
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
          "isTest": false, // not relevant
          "sandboxType": "None", // not relevant
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
      },
// next SaaS subscription details, might be a different offer
{
          "id": "<guid1>", 
          "name": "Contoso Cloud Solution1", 
          "publisherId": "contoso", 
          "offerId": "offer2", 
          "planId": "gold", 
          "quantity": "", 
          "beneficiary": {
              "emailId": " test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
          "purchaser": { 
              "emailId": "purchase@csp.com ",
              "objectId": "<guid>",
              "tenantId": "<guid>",
               "pid": "<ID of the user>"
          },
            "term": { 
                "startDate": "2019-05-31",
                "endDate": "2020-04-30",
                "termUnit": "P1Y"
          },
          "allowedCustomerOperations": [
              "Read" 
          ], 
          "sessionMode": "None",
          "isFreeTrial": false,
          "isTest": false,
          "sandboxType": "None",
          "saasSubscriptionStatus": "Suspended"
      }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

Si aucun abonnement SaaS acheté n’est trouvé pour cet éditeur, un corps de réponse vide est retourné.

Code : 403 Interdit. Le jeton d'autorisation n'est pas disponible, n’est pas valide ou a expiré.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte. 

Code : 500 Erreur interne du serveur. Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-subscription"></a>Obtenir un abonnement

Récupère un abonnement SaaS acheté spécifié pour une offre SaaS publiée sur la Place de marché par l’éditeur. Utilisez cet appel pour obtenir toutes les informations disponibles pour un abonnement SaaS spécifique par son ID au lieu d’appeler l’API pour obtenir la liste de tous les abonnements.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   Utilisez 2018-08-31. |
| `subscriptionId`     |  Identificateur unique de l’abonnement SaaS acheté.  Cet ID est obtenu après résolution du jeton d’autorisation de la Place de marché à l’aide de l’API de résolution. |

*En-têtes de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  `x-ms-correlationid` |  Valeur de chaîne unique pour l’opération sur le client.  Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  `authorization`     | Jeton d’accès unique qui identifie le serveur de publication qui effectue cet appel d’API. Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Codes de réponse :*

Code : 200 Retourne les détails d’un abonnement SaaS basé sur la valeur `subscriptionId` fournie.

*Exemple de corps de réponse :*

```json
{
        "id":<guid>, // purchased SaaS subscription ID
        "name":"Contoso Cloud Solution", // SaaS subscription name
         "publisherId": "contoso", // publisher ID
          "offerId": "offer1", // purchased offer ID
          "planId": "silver", // purchased plan ID
          "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
         "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
              "emailId": "test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
          "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
              "emailId": "test@test.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
        "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
        "sessionMode": "None", // not relevant
        "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
          "isTest": false, // not relevant
          "sandboxType": "None", // not relevant
          "saasSubscriptionStatus": " Subscribed " // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
          "term": { // the period for which the subscription was purchased 
            "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
            "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
            "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
        }
}
```

Code : 403 Interdit. Le jeton d’autorisation n’est pas valide, a expiré et n’est pas fourni. La demande tente d’accéder à un abonnement SaaS pour une offre publiée avec un ID d’application Azure AD différent de celui utilisé pour créer le jeton d’autorisation.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte. 

Code : 404 Introuvable.  L’abonnement SaaS avec la valeur `subscriptionId` spécifiée est introuvable.

Code : 500 Erreur interne du serveur.  Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="list-available-plans"></a>Liste des plans disponibles

Récupère tous les plans pour une offre SaaS identifiée par la valeur `subscriptionId` d’un achat spécifique de cette offre.  Utilisez cet appel pour obtenir une liste de tous les plans privés et publics que le bénéficiaire d’un abonnement SaaS peut mettre à jour pour l’abonnement.  Les plans retournés seront disponibles dans la même zone géographique que le plan déjà acheté.

Cet appel renvoie une liste des plans disponibles pour ce client en plus de celui que vous avez déjà acheté.  La liste peut être présentée à un client final sur le site de l’éditeur.  Un client final peut modifier le plan d’abonnement au profit d’un des plans de la liste renvoyée.  La modification du plan au profit d’un plan qui n’est pas répertorié dans la liste échoue.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Utilisez 2018-08-31.  |
|  `subscriptionId`    |  Identificateur unique de l’abonnement SaaS acheté.  Cet ID est obtenu après résolution du jeton d’autorisation de la Place de marché à l’aide de l’API de résolution. |

*En-têtes de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  `x-ms-correlationid`  |  Valeur de chaîne unique pour l’opération sur le client.  Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  `authorization`     |  Jeton d’accès unique qui identifie le serveur de publication qui effectue cet appel d’API.  Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Codes de réponse :*

Code : 200 Renvoie la liste de tous les plans disponibles pour un abonnement SaaS existant, y compris celui déjà acheté.

Exemple de corps de réponse :

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
        "isPrivate": true //true or false
    },
{ 
        "planId": "gold",
        "displayName": "Gold plan for Contoso", 
        "isPrivate": false //true or false
    }
]
}
```

Si `subscriptionId` est introuvable, un corps de réponse vide est retourné.

Code : 403 Interdit. Le jeton d’autorisation n’est pas valide, a expiré ou n’est pas fourni.  La demande tente peut-être d’accéder à un abonnement SaaS pour une offre qui est publiée avec un ID Azure AD App différent de celui utilisé pour créer le jeton d’autorisation.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte. 

Code : 500 Erreur interne du serveur.  Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="change-the-plan-on-the-subscription"></a>Modifier le plan de l’abonnement

Mettez à jour le plan existant acheté pour un abonnement SaaS avec un nouveau plan (public ou privé).  L’éditeur doit appeler cette API lorsqu’un plan est modifié côté éditeur pour un abonnement SaaS acheté sur la Place de marché.

Cette API peut être uniquement appelée pour des abonnements à l’état Actif.  Tout plan peut être remplacé par un autre plan existant (public ou privé), mais pas par lui-même.  Pour les plans privés, le locataire du client doit être défini dans le cadre de l’audience du plan dans l’Espace partenaires.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Utilisez 2018-08-31.  |
| `subscriptionId`     | Identificateur unique de l’abonnement SaaS acheté.  Cet ID est obtenu après résolution du jeton d’autorisation de la Place de marché à l’aide de l’API de résolution. |

*En-têtes de requête :*
 
|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `x-ms-correlationid`  | Valeur de chaîne unique pour l’opération sur le client.  Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `authorization`     |  Jeton d’accès unique qui identifie le serveur de publication qui effectue cet appel d’API.  Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Exemple de demande de charge utile :*

```json
{
    "planId": "gold" // the ID of the new plan to be purchased
}
```

*Codes de réponse :*

Code : 202 La demande de modification du plan a été acceptée et traitée de façon asynchrone.  Le partenaire doit interroger l’**URL Operation-Location** pour déterminer la réussite ou l’échec de la demande de changement de plan.  L’interrogation doit être effectuée à intervalles réguliers (quelques secondes) jusqu’à ce que l’état final d’échec, de réussite ou de conflit s’affiche pour l’opération.  L’état final de l’opération doit être retourné rapidement, mais peut prendre plusieurs minutes dans certains cas.

Le partenaire recevra également une notification de webhook lorsque l’action sera prête à se terminer sur la Place de marché.  Et ce n’est qu’après avoir reçu cette notification que l’éditeur devra modifier le plan côté éditeur.

*En-têtes de réponse :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  URL pour obtenir l’état de l’opération.  Par exemple : `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Code : 400 Demande incorrecte : échecs de validation.

* Le nouveau plan n’existe pas ou n’est pas disponible pour cet abonnement SaaS spécifique.
* Tentative de modification du même plan.
* L’état de l’abonnement SaaS n’est pas Abonné.
* L’opération de mise à jour pour un abonnement SaaS n’est pas incluse dans `allowedCustomerOperations`.

Code : 403 Interdit. Le jeton d’autorisation n’est pas valide, a expiré ou n’est pas fourni.  La demande tente d’accéder à un abonnement SaaS pour une offre publiée avec un ID d’application Azure AD différent de celui utilisé pour créer le jeton d’autorisation.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte.

Code : 404 Introuvable.  L’abonnement SaaS avec `subscriptionId` est introuvable.

Code : 500 Erreur interne du serveur.  Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

>[!NOTE]
>Le plan ou la quantité de postes peut être modifié(e), mais pas simultanément.

>[!Note]
>Cette API peut être appelée uniquement après avoir obtenu l’approbation explicite du client final pour la modification.

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>Modifier la quantité de postes de l’abonnement SaaS

Mettez à jour (augmentez ou diminuez) la quantité de postes achetés pour un abonnement SaaS.  L’éditeur doit appeler cette API lorsque le nombre de postes est modifié côté éditeur pour un abonnement SaaS créé dans la Place de marché.

La quantité de postes ne peut pas être supérieure à ce qui est autorisé dans le plan actuel.  Dans ce cas, le plan doit être modifié avant la modification de la quantité.

##### <a name="patchhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Utilisez 2018-08-31.  |
|  `subscriptionId`     | Identificateur unique de l’abonnement SaaS acheté.  Cet ID est obtenu après résolution du jeton d’autorisation de la Place de marché à l’aide de l’API de résolution.  |

*En-têtes de requête :*
 
|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `x-ms-correlationid`  | Valeur de chaîne unique pour l’opération sur le client.  Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `authorization`     | Jeton d’accès unique qui identifie le serveur de publication qui effectue cet appel d’API.  Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Exemple de demande de charge utile :*

```json
{
    "quantity": 5 // the new amount of seats to be purchased
}
```

*Codes de réponse :*

Code : 202 La demande de modification de la quantité a été acceptée et traitée de façon asynchrone. Le partenaire doit interroger l’**URL Operation-Location** pour déterminer la réussite ou l’échec de la demande de changement de quantité.  L’interrogation doit être effectuée à intervalles réguliers (quelques secondes) jusqu’à ce que l’état final d’échec, de réussite ou de conflit s’affiche pour l’opération.  L’état final de l’opération doit être retourné rapidement, mais peut prendre plusieurs minutes dans certains cas.

Le partenaire recevra également une notification de webhook lorsque l’action sera prête à se terminer sur la Place de marché.  Et ce n’est qu’après avoir reçu cette notification que l’éditeur devra modifier la quantité côté éditeur.

*En-têtes de réponse :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Lien vers une ressource pour obtenir le statut de l’opération.  Par exemple : `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Code : 400 Demande incorrecte : échecs de validation.

* La nouvelle quantité est supérieure ou inférieure à la limite actuelle du plan.
* La nouvelle quantité est manquante.
* Tentative de modification de la même quantité.
* L’état de l’abonnement SaaS n’est pas Abonné.
* L’opération de mise à jour pour un abonnement SaaS n’est pas incluse dans `allowedCustomerOperations`.

Code : 403 Interdit.  Le jeton d’autorisation n’est pas valide, a expiré ou n’est pas fourni.  La demande tente d’accéder à un abonnement qui n’appartient pas à l’éditeur actuel.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte. 

Code : 404 Introuvable.  L’abonnement SaaS avec `subscriptionId` est introuvable.

Code : 500 Erreur interne du serveur.  Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

>[!Note]
>Un plan ou une quantité peut être modifié(e), mais pas les deux simultanément.

>[!Note]
>Cette API peut être appelée uniquement après avoir obtenu l’approbation explicite du client final pour la modification.

#### <a name="cancel-a-subscription"></a>Annuler un abonnement

Désabonnez-vous d’un abonnement SaaS spécifié.  L’éditeur n’a pas besoin d’utiliser cette API et nous vous recommandons de diriger les clients vers la Place de marché afin d’annuler les abonnements SaaS.

Si l’éditeur décide d’implémenter l’annulation de l’abonnement SaaS acheté dans la Place de marché côté éditeur, il doit appeler cette API.  Une fois cet appel terminé, l’état de l’abonnement passe à *Désabonné* côté Microsoft.

Si un abonnement est annulé dans les périodes de grâce suivantes, le client ne sera pas facturé :

* 24 heures pour un abonnement mensuel après activation.
* 14 jours pour un abonnement annuel après activation.

Le client est facturé si un abonnement est annulé après les périodes de grâce indiquées ci-dessus.  Une fois l’annulation effectuée, le client perd immédiatement l’accès à l’abonnement SaaS côté Microsoft.

##### <a name="deletehttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Delete`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Utilisez 2018-08-31.  |
|  `subscriptionId`     | Identificateur unique de l’abonnement SaaS acheté.  Cet ID est obtenu après résolution du jeton d’autorisation de la Place de marché à l’aide de l’API de résolution.  |

*En-têtes de requête :*
 
|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `x-ms-correlationid`  | Valeur de chaîne unique pour l’opération sur le client.  Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `authorization`     |  Jeton d’accès unique qui identifie le serveur de publication qui effectue cet appel d’API.  Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Codes de réponse :*

Code : 202 La demande de désabonnement a été acceptée et traitée de façon asynchrone.  Le partenaire doit interroger l’**URL Operation-Location** pour déterminer la réussite ou l’échec de cette demande.  L’interrogation doit être effectuée à intervalles réguliers (quelques secondes) jusqu’à ce que l’état final d’échec, de réussite ou de conflit s’affiche pour l’opération.  L’état final de l’opération doit être retourné rapidement, mais peut prendre plusieurs minutes dans certains cas.

Le partenaire recevra également une notification de webhook lorsque l’action sera terminée sur la Place de marché.  Et ce n’est qu’après avoir reçu cette notification que l’éditeur devra annuler l’abonnement côté éditeur.

*En-têtes de réponse :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Lien vers une ressource pour obtenir le statut de l’opération.  Par exemple : `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Code : 400 Requête incorrecte.  Le paramètre Delete ne fait pas partie de la liste `allowedCustomerOperations` pour cet abonnement SaaS.

Code : 403 Interdit.  Le jeton d’autorisation n’est pas valide, a expiré ou n’est pas disponible. La demande tente d’accéder à un abonnement SaaS pour une offre publiée avec un ID d’application Azure AD différent de celui utilisé pour créer le jeton d’autorisation.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte.

Code : 404 Introuvable.  L’abonnement SaaS avec `subscriptionId` est introuvable.

Code : 500 Erreur interne du serveur. Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

### <a name="operations-apis"></a>API d’opérations

#### <a name="list-outstanding-operations"></a>Liste des opérations en attente 

Obtenir la liste des opérations en attente pour l’abonnement SaaS spécifié.  Les opérations retournées doivent être acceptées par l’éditeur en appelant l’[API d’opérations Patch](#update-the-status-of-an-operation).

Actuellement, seules les **opérations de réactivation** sont retournées comme réponse pour cet appel d’API.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  Utilisez 2018-08-31.         |
|    `subscriptionId` | Identificateur unique de l’abonnement SaaS acheté.  Cet ID est obtenu après résolution du jeton d’autorisation de la Place de marché à l’aide de l’API de résolution.  |

*En-têtes de requête :*
 
|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `x-ms-correlationid` |  Valeur de chaîne unique pour l’opération sur le client.  Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `authorization`     |  Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Codes de réponse :*

Code : 200 Retourne l’opération de réactivation en attente sur l’abonnement SaaS spécifié.

*Exemple de charge utile de réponse :*

```json
{"operations": [{
    "id": "<guid>",  //Operation ID, should be provided in the operations patch API call
    "activityId": "<guid>", //not relevant
    "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
    "offerId": "offer1",  // purchased offer ID
    "publisherId": "contoso",  
    "planId": "silver",  // purchased plan ID
    "quantity": "20", // purchased amount of seats, will be empty is not relevant
    "action": "Reinstate", 
    "timeStamp": "2018-12-01T00:00:00",  // UTC
    "status": "InProgress" // the only status that can be returned in this case
}]
}
```

Retourne un objet json vide si aucune opération de réactivation n’est en attente.

Code : 400 Demande incorrecte : échecs de validation.

Code : 403 Interdit. Le jeton d’autorisation n’est pas valide, a expiré ou n’est pas fourni.  La demande tente d’accéder à un abonnement SaaS pour une offre publiée avec un ID d’application Azure AD différent de celui utilisé pour créer le jeton d’autorisation.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte. 

Code : 404 Introuvable.  L’abonnement SaaS avec `subscriptionId` est introuvable.

Code : 500 Erreur interne du serveur. Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-operation-status"></a>Obtenir l’état d’une opération

Permet à l’éditeur de suivre l’état de l’opération asynchrone spécifiée :  **Unsubscribe**, **ChangePlan** ou **ChangeQuantity**.

La valeur `operationId` pour cet appel d’API peut être récupérée à partir de la valeur retournée par **Operation-Location**, l’appel d’API pour obtenir les opérations en attente, ou la valeur de paramètre `<id>` reçue dans un appel webhook.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Utilisez 2018-08-31.  |
|  `subscriptionId`    |  Identificateur unique de l’abonnement SaaS acheté.  Cet ID est obtenu après résolution du jeton d’autorisation de la Place de marché à l’aide de l’API de résolution. |
|  `operationId`       |  Identificateur unique de l'opération en cours de récupération. |

*En-têtes de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  `x-ms-correlationid` |  Valeur de chaîne unique pour l’opération sur le client.  Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse.  |
|  `authorization`     |  Jeton d’accès unique qui identifie le serveur de publication qui effectue cet appel d’API.  Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Codes de réponse :*

Code : 200 Obtient les détails de l’opération SaaS spécifiée. 

*Exemple de charge utile de réponse :*

```json
Response body:
{
    "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
    "activityId": "<guid>", //not relevant
    "subscriptionId":"<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
    "offerId": "offer1", // purchased offer ID
    "publisherId": "contoso",  
    "planId": "silver", // purchased plan ID
    "quantity": "20", // purchased amount of seats 
    "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
    "timeStamp": "2018-12-01T00:00:00", // UTC
    "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeed, Conflict (new quantity / plan is the same as existing)

"errorStatusCode": "",
"errorMessage": ""
}
```

Code : 403 Interdit. Le jeton d’autorisation n’est pas valide, a expiré ou n’est pas fourni.  La demande tente d’accéder à un abonnement SaaS pour une offre publiée avec un ID d’application Azure AD différent de celui utilisé pour créer le jeton d’autorisation.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte. 

Code : 404 Introuvable.  

* L’abonnement avec `subscriptionId` est introuvable.
* L’opération avec `operationId` est introuvable.

Code : 500 Erreur interne du serveur.  Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="update-the-status-of-an-operation"></a>Mettre à jour l’état d’une opération

Mettre à jour l’état d’une opération en attente pour indiquer la réussite ou l’échec de l’opération côté éditeur.

La valeur `operationId` pour cet appel d’API peut être récupérée à partir de la valeur retournée par **Operation-Location**, l’appel d’API pour obtenir les opérations en attente, ou la valeur de paramètre `<id>` reçue dans un appel webhook.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Paramètres de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  Utilisez 2018-08-31.  |
|   `subscriptionId`   |  Identificateur unique de l’abonnement SaaS acheté.  Cet ID est obtenu après résolution du jeton d’autorisation de la Place de marché à l’aide de l’API de résolution.  |
|   `operationId`      |  Identificateur unique de l’opération terminée. |

*En-têtes de requête :*

|  Paramètre         | Valeur             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|   `x-ms-correlationid` |  Valeur de chaîne unique pour l’opération sur le client.  Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur.  Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
|  `authorization`     |  Jeton d’accès unique qui identifie le serveur de publication qui effectue cet appel d’API.  Le format est `"Bearer <access_token>"` lorsque la valeur de jeton est récupérée par le serveur de publication, comme expliqué dans [Obtenir un jeton basé sur l’application Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Exemple de demande de charge utile :*

```json
{ 
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*Codes de réponse :*

Code : 200 Appel pour informer de la fin d’une opération côté partenaire.  Par exemple, cette réponse peut signaler la fin de la modification de postes ou de plans côté éditeur.

Code : 403 Interdit.  Le jeton d'autorisation n'est pas disponible, n’est pas valide ou a expiré. La demande tente peut-être d’accéder à un abonnement qui n’appartient pas à l’éditeur actuel.
Interdit.  Le jeton d’autorisation n’est pas valide, a expiré ou n’est pas fourni.  La demande tente d’accéder à un abonnement SaaS pour une offre publiée avec un ID d’application Azure AD différent de celui utilisé pour créer le jeton d’autorisation.

Cette erreur est souvent le symptôme de l’absence d’une inscription [SaaS](pc-saas-registration.md) correcte.

Code : 404 Introuvable.

* L’abonnement avec `subscriptionId` est introuvable.
* L’opération avec `operationId` est introuvable.

Code : 409 Conflit.  Par exemple, une mise à jour plus récente est déjà traitée.

Code : 500 Erreur interne du serveur.  Renouvelez l’appel d’API.  Si l’erreur persiste, contactez le [support technique Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implémentation d’un webhook sur le service SaaS

Lors de la création d’une offre SaaS qui peut être utilisée dans l’Espace partenaires, le partenaire fournit l’URL **Connexion webhook** à utiliser comme point de terminaison HTTP.  Ce webhook est appelé par Microsoft à l’aide de l’appel POST HTTP pour notifier le côté éditeur des événements suivants qui se produisent côté Microsoft :

* Lorsque l’abonnement SaaS affiche l’état Abonné :
    * ChangePlan 
    * ChangeQuantity
    * Interrompre
    * Se désabonner
* Quand un abonnement SaaS affiche l’état Interrompu :
    * Reinstate
    * Se désabonner

L’éditeur doit implémenter un webhook dans le service SaaS pour garantir la cohérence de l’état de l’abonnement SaaS avec le côté Microsoft.  Le service SaaS doit appeler l’API d’opération GET pour valider et autoriser l’appel du webhook et les données de la charge utile avant de prendre des mesures en fonction de la notification du webhook.  L’éditeur doit retourner HTTP 200 à Microsoft dès que l’appel de webhook est traité.  Cette valeur confirme que l’éditeur a bien reçu l’appel du webhook.

>[!Note]
>Le service de l’URL du webhook doit être disponible 24 h/24 et 7 j/7 et prêt à recevoir à tout moment de nouveaux appels de Microsoft.  Microsoft ne propose aucune stratégie de nouvelle tentative pour l’appel du webhook (500 nouvelles tentatives sur 8 heures), mais si l’éditeur n’accepte pas l’appel et renvoie une réponse, l’opération notifiée par le webhook finira par échouer côté Microsoft.

*Exemples de charge utile webhook :*

```json
// end customer changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": <guid>, // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": " 25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success  
}
```

```json
// end customer's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": <guid>, 
  "activityId": <guid>, 
  "subscriptionId": "guid", 
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold", 
  "quantity": " 20", 
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "In Progress" 
} 
```

## <a name="development-and-testing"></a>Développement et test

Pour démarrer le processus de développement, nous vous recommandons de créer des réponses d’API factices côté éditeur.  Ces réponses peuvent reposer sur des exemples de réponses fournis dans ce document.

Lorsque l’éditeur est prêt pour le test de bout en bout : 

* Publiez une offre SaaS sur une audience en préversion limitée et conservez-la en phase de préversion.
* Cette offre doit inclure un plan à un tarif de 0 pour éviter toute facturation de frais réels lors des tests.  Une autre solution consiste à définir un prix différent de 0 et à annuler tous les achats de test pendant 24 heures. 
* Vérifiez que tous les flux sont appelés de bout en bout, exactement comme si un client achetait l’offre. 
* Si le partenaire souhaite tester le flux complet d’achat et de facturation, utilisez une offre dont le prix est supérieur à 0.  L’achat sera facturé et une facture sera établie.

Un flux d’achat peut être déclenché à partir du portail Azure ou de Microsoft AppSource, selon l’endroit où l’offre est publiée.

Les actions de *modification du plan*, *modification de quantité* et de *désabonnement* sont testées côté éditeur.  Côté Microsoft, l’opération de *désabonnement* peut être déclenchée à partir du portail Azure ou centre d’administration (le portail où les achats Microsoft AppSource sont gérés).  Les opérations de *modification de quantité et de plan* peuvent uniquement être déclenchées à partir du centre d’administration.

## <a name="get-support"></a>Obtenir de l’aide

Pour afficher les options de support pour les éditeurs, consultez [Support technique pour le programme commercial de la Place de marché dans l’Espace partenaires](support.md).


## <a name="next-steps"></a>Étapes suivantes

Consultez les [API du service de mesure](marketplace-metering-service-apis.md) de la Place de marché pour plus d’options concernant les offres SaaS de la Place de marché.

Examinez et utilisez le [Kit de développement logiciel (SDK) SaaS](https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK) basé sur les API décrites dans ce document.
