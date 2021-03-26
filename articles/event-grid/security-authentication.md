---
title: Authentifier la remise des événements aux gestionnaires d’événements (Azure Event Grid)
description: Cet article décrit les différentes façons d’authentifier la remise auprès de gestionnaires d’événements dans Azure Event Grid.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 98d7a4a0dee6c355ec340668bef7d8b306f97496
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633118"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>Authentifier la remise des événements aux gestionnaires d’événements (Azure Event Grid)
Cet article fournit des informations sur l’authentification de la remise d’événements auprès de gestionnaires d’événements. Il vous montre également comment sécuriser les points de terminaison webhook utilisés pour recevoir des événements d’Event Grid à l’aide d’Azure Active Directory (Azure AD) ou d’un secret partagé.

## <a name="use-system-assigned-identities-for-event-delivery"></a>Utiliser des identités attribuées par le système pour la remise d’événements
Vous pouvez activer une identité gérée attribuée par le système pour une rubrique ou un domaine et utiliser cette identité pour transférer des événements vers des destinations prises en charge, comme les files d’attente et les rubriques Service Bus, les concentrateurs d’événements et les comptes de stockage.

Voici la procédure à suivre : 

1. Créer une rubrique ou un domaine avec une identité affectée par le système, ou mettre à jour une rubrique ou un domaine existants pour activer une identité. 
1. Ajouter l’identité à un rôle approprié (par exemple, expéditeur de données Service Bus) sur la destination (par exemple, une file d’attente Service Bus).
1. Lors de la création d’abonnements à des événements, activer l’utilisation de l’identité pour remettre des événements à la destination. 

Pour obtenir des instructions pas à pas détaillées, consultez [Remise d’événements avec une identité gérée](managed-service-identity.md).


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Authentifier la remise des événements aux points de terminaison webhook
Les sections suivantes décrivent comment authentifier la remise d’événements aux points de terminaison webhook. L’emploi d’un mécanisme de négociation de validation est obligatoire, quelle que soit la méthode que vous utilisez. Pour plus d’informations, consultez [Remise d’événements webhook](webhook-event-delivery.md). 


### <a name="using-azure-active-directory-azure-ad"></a>Utilisation d’Azure Active Directory (Azure AD)
Vous pouvez sécuriser le point de terminaison webhook utilisé pour recevoir des événements d’Event Grid à l’aide d’Azure AD. Vous devez créer une application Azure AD, créer un rôle et un principal de service dans votre application qui autorisent Event Grid, et configurer l’abonnement à un événement pour utiliser l’application Azure AD. Découvrez comment [Configurer Azure Active Directory avec Event Grid](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Utilisation d’un secret client comme paramètre de requête
Vous pouvez également sécuriser votre point de terminaison webhook en ajoutant des paramètres de requête à l’URL de destination webhook spécifiée lors de la création d’un abonnement à un événement. Définissez l’un des paramètres de requête en tant que clé secrète client, par exemple un [jeton d’accès](https://en.wikipedia.org/wiki/Access_token) ou un secret partagé. Le service Event Grid inclut tous les paramètres de requête dans chaque demande de remise d’événement au webhook. Le service webhook peut récupérer et valider le secret. Si le secret client est mis à jour, l’abonnement aux événements doit également être mis à jour. Pour éviter les échecs de remise pendant cette rotation de secret, faites en sorte que le webhook accepte l’ancien et le nouveau secrets pendant une durée limitée avant de mettre à jour l’abonnement à l’événement avec le nouveau secret. 

Comme les paramètres de requête peuvent contenir des secrets clients, ils doivent être utilisés avec une prudence particulière. Ils sont stockés sous forme chiffrée hors de portée des opérateurs de service. Ils ne sont pas enregistrés dans les journaux/traces de service. Lors de la récupération des propriétés de l’abonnement à l’événement, par défaut, les paramètres de requête de destination ne sont pas retournés. Par exemple, le paramètre [--include-full-endpoint-url](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-show) doit être utilisé dans [Azure CLI](/cli/azure).

Pour plus d’informations sur la remise d’événements à des webhooks, consultez [Remise d’événements webhook](webhook-event-delivery.md)

> [!IMPORTANT]
Azure Event Grid ne prend en charge que les points de terminaison webhook **HTTPS**. 

## <a name="endpoint-validation-with-cloudevents-v10"></a>Validation de point de terminaison avec CloudEvents v1.0
Si vous êtes déjà familiarisé avec Event Grid, vous pouvez être conscient de l’établissement d'une liaison de validation de point de terminaison pour empêcher les abus. CloudEvents v1.0 implémente sa propre [sémantique de protection contre les abus](webhook-event-delivery.md) à l’aide de la méthode **HTTP OPTIONS**. Pour en savoir plus à ce sujet, consultez [Webhooks HTTP 1.1 pour la remise d’événements (version 1.0)](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Lorsque vous utilisez le schéma CloudEvents pour la sortie, Event Grid l’utilise avec la protection contre les abus CloudEvents v1.0 à la place du mécanisme d’événement de validation Event Grid. Pour plus d’informations, consultez [Utiliser le schéma CloudEvents v1.0 avec Event Grid](cloudevents-schema.md). 


## <a name="next-steps"></a>Étapes suivantes
Consultez [Authentifier les clients de publication](security-authenticate-publishing-clients.md) pour en savoir plus sur l’authentification des événements de publication des clients dans des rubriques ou des domaines. 
