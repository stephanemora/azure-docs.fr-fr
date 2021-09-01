---
title: Authentifier la remise des événements aux gestionnaires d’événements (Azure Event Grid)
description: Cet article décrit les différentes façons d’authentifier la remise auprès de gestionnaires d’événements dans Azure Event Grid.
ms.topic: conceptual
ms.date: 06/28/2021
ms.openlocfilehash: 01383809e6aab895ff4ed42763c57004a6ee02a8
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003230"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>Authentifier la remise des événements aux gestionnaires d’événements (Azure Event Grid)
Cet article fournit des informations sur l’authentification de la remise d’événements auprès de gestionnaires d’événements. 

## <a name="overview"></a>Vue d'ensemble
Azure Event Grid utilise différentes méthodes d’authentification pour remettre des événements aux gestionnaires d’événements. `

| Méthode d'authentification | Gestionnaires pris en charge | Description  |
|--|--|--|
Clé d’accès | <p>Event Hubs</p><p>Service Bus</p><p>Files d’attente de stockage</p><p>Connexions hybrides Relay</p><p>Azure Functions</p><p>Objets blob de stockage (DeadLetter)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</p> | Les clés d’accès sont extraites à l’aide des informations d’identification du principal de service Event Grid. Les autorisations sont accordées à Event Grid lorsque vous inscrivez le fournisseur de ressources Event Grid dans son abonnement Azure. |  
Identité système managée <br/>&<br/> Contrôle d’accès en fonction du rôle | <p>Event Hubs</p><p>Service Bus</p><p>Files d’attente de stockage</p><p>Objets blob de stockage (DeadLetter)</p></li></ul> | Activez l’identité système managée pour la rubrique et ajoutez-la au rôle approprié sur la destination. Pour plus de détails, consultez [Utiliser des identités attribuées par le système pour la remise d’événements](#use-system-assigned-identities-for-event-delivery).  |
|Authentification du jeton du porteur avec un webhook Azure AD protégé | webhook | Voir la section [Authentifier la livraison d’événements aux points de terminaison des webhooks](#authenticate-event-delivery-to-webhook-endpoints) pour plus de détails. |
Secret client comme paramètre de requête | webhook | Voir la section [Utilisation d’un secret client comme paramètre de requête](#using-client-secret-as-a-query-parameter) pour plus de détails. |

## <a name="use-system-assigned-identities-for-event-delivery"></a>Utiliser des identités attribuées par le système pour la remise d’événements
Vous pouvez activer une identité gérée attribuée par le système pour une rubrique ou un domaine et utiliser cette identité pour transférer des événements vers des destinations prises en charge, comme les files d’attente et les rubriques Service Bus, les concentrateurs d’événements et les comptes de stockage.

Voici la procédure à suivre : 

1. Créer une rubrique ou un domaine avec une identité affectée par le système, ou mettre à jour une rubrique ou un domaine existants pour activer une identité. Pour plus d’informations, voir [Activer l’identité managée pour une rubrique système](enable-identity-system-topics.md) ou [Activer l’identité managée pour une rubrique personnalisée ou un domaine](enable-identity-custom-topics-domains.md).
1. Ajouter l’identité à un rôle approprié (par exemple, expéditeur de données Service Bus) sur la destination (par exemple, une file d’attente Service Bus). Pour plus d’informations, consultez [Accorder à l’identité l’accès à la destination Event Grid](add-identity-roles.md).
1. Lors de la création d’abonnements à des événements, activer l’utilisation de l’identité pour remettre des événements à la destination. Pour plus d’informations, consultez [Créer un abonnement à l’événement utilisant l’identité](managed-service-identity.md). 

Pour obtenir des instructions pas à pas détaillées, consultez [Remise d’événements avec une identité gérée](managed-service-identity.md).


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Authentifier la remise des événements aux points de terminaison webhook
Les sections suivantes décrivent comment authentifier la remise d’événements aux points de terminaison webhook. Utilisez un mécanisme de négociation de validation, quelle que soit la méthode que vous utilisez. Pour plus d’informations, consultez [Remise d’événements webhook](webhook-event-delivery.md). 


### <a name="using-azure-active-directory-azure-ad"></a>Utilisation d’Azure Active Directory (Azure AD)
Vous pouvez sécuriser le point de terminaison webhook utilisé pour recevoir des événements d’Event Grid à l’aide d’Azure AD. Vous devez créer une application Azure AD, créer un rôle et un principal de service dans votre application qui autorisent Event Grid, et configurer l’abonnement à un événement pour utiliser l’application Azure AD. Découvrez comment [Configurer Azure Active Directory avec Event Grid](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Utilisation d’un secret client comme paramètre de requête
Vous pouvez également sécuriser votre point de terminaison webhook en ajoutant des paramètres de requête à l’URL de destination webhook spécifiée lors de la création d’un abonnement à un événement. Définissez l’un des paramètres de requête en tant que clé secrète client, par exemple un [jeton d’accès](https://en.wikipedia.org/wiki/Access_token) ou un secret partagé. Le service Event Grid inclut tous les paramètres de requête dans chaque demande de remise d’événement au webhook. Le service webhook peut récupérer et valider le secret. Si le secret client est mis à jour, l’abonnement aux événements doit également être mis à jour. Pour éviter les échecs de remise pendant cette rotation de secret, faites en sorte que le webhook accepte l’ancien et le nouveau secrets pendant une durée limitée avant de mettre à jour l’abonnement à l’événement avec le nouveau secret. 

Comme les paramètres de requête peuvent contenir des secrets clients, ils doivent être utilisés avec une prudence particulière. Ils sont stockés sous forme chiffrée hors de portée des opérateurs de service. Ils ne sont pas enregistrés dans les journaux/traces de service. Lors de la récupération des propriétés de l’abonnement à l’événement, par défaut, les paramètres de requête de destination ne sont pas retournés. Par exemple, le paramètre [--include-full-endpoint-url](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_show) doit être utilisé dans [Azure CLI](/cli/azure).

Pour plus d’informations sur la remise d’événements à des webhooks, consultez [Remise d’événements webhook](webhook-event-delivery.md)

> [!IMPORTANT]
> Azure Event Grid ne prend en charge que les points de terminaison webhook **HTTPS**. 

## <a name="endpoint-validation-with-cloudevents-v10"></a>Validation de point de terminaison avec CloudEvents v1.0
Si vous êtes déjà familiarisé avec Event Grid, vous pouvez être conscient de l’établissement d'une liaison de validation de point de terminaison pour empêcher les abus. CloudEvents v1.0 implémente sa propre [sémantique de protection contre les abus](webhook-event-delivery.md) à l’aide de la méthode **HTTP OPTIONS**. Pour en savoir plus à ce sujet, consultez [Webhooks HTTP 1.1 pour la remise d’événements (version 1.0)](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Lorsque vous utilisez le schéma CloudEvents pour la sortie, Event Grid l’utilise avec la protection contre les abus CloudEvents v1.0 à la place du mécanisme d’événement de validation Event Grid. Pour plus d’informations, consultez [Utiliser le schéma CloudEvents v1.0 avec Event Grid](cloudevents-schema.md). 


## <a name="next-steps"></a>Étapes suivantes
Consultez [Authentifier les clients de publication](security-authenticate-publishing-clients.md) pour en savoir plus sur l’authentification des événements de publication des clients dans des rubriques ou des domaines. 
