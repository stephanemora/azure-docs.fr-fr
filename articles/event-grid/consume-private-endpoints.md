---
title: Remise d’événements à l’aide du service de liaison privée
description: Cet article explique la procédure de contournement de l’impossibilité de remettre des événements à l’aide du service de liaison privée.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 4343740ea6c34c9ae282723b79007f7035785b04
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548610"
---
# <a name="deliver-events-using-private-link-service"></a>Remise d’événements à l’aide du service de liaison privée
Actuellement, il n’est pas possible de remettre des événements à l’aide de [points de terminaison privés](../private-link/private-endpoint-overview.md). Autrement dit, il n’y a pas de prise en charge si vous avez des exigences strictes en matière d’isolement réseau lorsque le trafic des événements remis ne doit pas sortir de l’espace IP privé. 

## <a name="use-managed-identity"></a>Utiliser l’identité managée
Toutefois, si vos exigences comprennent une méthode sécurisée pour envoyer des événements à l’aide d’un canal chiffré et une identité connue de l’expéditeur (dans ce cas, Event Grid) utilisant un espace d’adressage IP public, vous pouvez remettre des événements au service Event Hubs, Service Bus ou Stockage Azure à l’aide d’une rubrique ou d’un domaine personnalisés Azure Event Grid avec une identité managée par le système. Pour plus d’informations sur la remise d’événements à l’aide d’une identité managée, consultez [Remise d’événement à l’aide d’une identité managée](managed-service-identity.md). 

Ensuite, vous pouvez utiliser une liaison privée configurée dans Azure Functions ou votre webhook déployé sur votre réseau virtuel pour extraire des événements. Voir l’exemple : [Connectez-vous à des points de terminaison privés avec Azure Functions](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).


:::image type="content" source="./media/consume-private-endpoints/deliver-private-link-service.svg" alt-text="Remise via un service de liaison privée":::


Dans cette configuration, le trafic transite par l’adresse IP publique ou Internet d’Event Grid vers le service Event Hubs, Service Bus ou Stockage Azure, mais le canal peut être chiffré et une identité managée d’Event Grid est utilisée. Si vous configurez votre Azure Functions ou webhook déployé sur votre réseau virtuel pour utiliser un stockage Event Hubs, Service Bus ou stockage Azure via une liaison privée, cette section du trafic restera évidemment dans Azure.

## <a name="deliver-events-to-event-hubs-using-managed-identity"></a>Remettre des événements à Event Hubs à l’aide d’une identité managée
Pour remettre des événements à Event Hubs dans votre espace de noms Event Hubs à l’aide d’une identité managée, procédez comme suit :

1. [Activez une identité attribuée par le système pour un sujet ou un domaine](managed-service-identity.md#create-a-custom-topic-or-domain-with-an-identity). 
1. [Ajoutez l’identité au rôle **Expéditeur de données Azure Event Hubs** dans l’espace de noms Event Hubs](../event-hubs/authenticate-managed-identity.md#to-assign-azure-roles-using-the-azure-portal).
1. [Activez le paramètre **Autoriser les services Microsoft approuvés à contourner ce pare-feu** dans votre espace de noms Event hubs](../event-hubs/event-hubs-service-endpoints.md#trusted-microsoft-services). 
1. [Configurez l’abonnement aux événements](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) qui utilise un hub d’événements comme point de terminaison pour utiliser l’identité attribuée au système.

## <a name="deliver-events-to-service-bus-using-managed-identity"></a>Remettre des événements à Service Bus à l’aide d’une identité managée
Pour remettre des événements à des files d’attente ou des rubriques Service Bus dans votre espace de noms Service Bus à l’aide d’une identité managée, procédez comme suit :

1. [Activez une identité attribuée par le système pour un sujet ou un domaine](managed-service-identity.md#create-a-custom-topic-or-domain-with-an-identity). 
1. Ajouter l’identité au rôle [Expéditeur de données Azure Service Bus](/service-bus-messaging/service-bus-managed-service-identity.md#azure-built-in-roles-for-azure-service-bus) dans l’espace de noms Service Bus
1. [Activez le paramètre **Autoriser les services Microsoft approuvés à contourner ce pare-feu** dans votre espace de noms Service Bus](../service-bus-messaging/service-bus-service-endpoints.md#trusted-microsoft-services). 
1. [Configurez l’abonnement aux événements](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) qui utilise une file d’attente ou une rubrique Service Bus comme point de terminaison pour utiliser l’identité affectée au système.

## <a name="deliver-events-to-storage"></a>Remettre des événements au stockage 
Pour remettre des événements dans les files d’attente de stockage à l’aide d’une identité managée, procédez comme suit :

1. [Activez une identité attribuée par le système pour un sujet ou un domaine](managed-service-identity.md#create-a-custom-topic-or-domain-with-an-identity).
1. Ajoutez l’identité au rôle [Expéditeur de messages en file d’attente de stockage](../storage/common/storage-auth-aad-rbac-portal.md) dans la file d’attente de stockage Azure.
1. [Configurez l’abonnement aux événements](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) qui utilise une file d’attente ou une rubrique Service Bus comme point de terminaison pour utiliser l’identité attribuée au système.


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la remise d’événements à l’aide d’une identité managée, consultez [Remise d’événement à l’aide d’une identité managée](managed-service-identity.md). 
