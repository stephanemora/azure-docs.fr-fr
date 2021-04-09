---
title: Résoudre les problèmes liés à Event Grid
description: Cet article propose différentes solutions pour résoudre les problèmes liés à Azure Event Grid.
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: d30b8464de90474ad74853cc423de700b41226a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720557"
---
# <a name="troubleshoot-azure-event-grid-issues"></a>Résoudre les problèmes liés à Azure Event Grid
Cet article fournit des informations qui vous aideront à résoudre les problèmes liés à Azure Event Grid. 

## <a name="diagnostic-logs"></a>Journaux de diagnostic
Activez les paramètres de diagnostic des rubriques ou domaines Event Grid pour capturer et afficher les journaux des échecs de publication et de remise. Pour plus d'informations, consultez [Journaux de diagnostic](enable-diagnostic-logs-topic.md).

## <a name="metrics"></a>Mesures
Vous pouvez consulter les métriques des rubriques et abonnements Event Grid, et créer des alertes correspondantes. Pour plus d'informations, consultez [Métriques Event Grid](monitor-event-delivery.md).

## <a name="alerts"></a>Alertes
Créez des alertes sur les opérations des journaux d'activité et les métriques Azure Event Grid. Pour plus d'informations, consultez [Alertes sur les journaux d'activité et les métriques Event Grid](set-alerts.md).

## <a name="subscription-validation-issues"></a>Problèmes liés à la validation de l'abonnement
Lors de la création de l'abonnement aux événements, vous pouvez recevoir un message d'erreur indiquant que la validation du point de terminaison fourni a échoué. Pour résoudre les problèmes liés à la validation de l'abonnement, consultez [Résoudre les problèmes de validation de l'abonnement Event Grid](troubleshoot-subscription-validation.md). 

## <a name="network-connectivity-issues"></a>Problèmes de connectivité réseau
Il existe plusieurs raisons pour lesquelles des applications clientes peuvent ne pas parvenir à se connecter à une rubrique/un domaine Event Grid. Les problèmes de connectivité que vous rencontrez peuvent être permanents ou transitoires. Pour plus d'informations sur la résolution de ces problèmes, consultez [Résoudre les problèmes de connectivité](troubleshoot-network-connectivity.md).

## <a name="error-codes"></a>Codes d’erreur
Si vous recevez des messages d'erreur avec des codes d'erreur tels que 400, 409 et 403, consultez [Résoudre les erreurs liées à Event Grid](troubleshoot-errors.md). 

## <a name="distributed-tracing-net"></a>Suivi distribué (.NET)
La bibliothèque Event Grid .NET prend en charge la distribution du suivi. Pour se conformer aux [directives de la spécification CloudEvents](https://github.com/cloudevents/spec/blob/master/extensions/distributed-tracing.md) sur la distribution du suivi, la bibliothèque définit les attributs `traceparent` et `tracestate` sur les attributs [ExtensionAttributes](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventgrid/Azure.Messaging.EventGrid/src/Customization#L126) d'un `CloudEvent` lorsque le suivi distribué est activé. Pour en savoir plus sur l'activation du suivi distribué dans votre application, consultez la [documentation consacrée au suivi distribué](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#Distributed-tracing) du kit de développement logiciel (SDK) Azure.

### <a name="sample"></a>Exemple
Consultez l'[exemple de compteur de lignes](/samples/azure/azure-sdk-for-net/line-counter/). Cet exemple d'application illustre l'utilisation des clients Stockage, Event Hubs et Event Grid, ainsi que l'intégration d'ASP.NET Core, le suivi distribué et les services hébergés. Cette application permet aux utilisateurs de charger un fichier dans un blob, ce qui déclenche un événement Event Hubs contenant le nom du fichier. Le processeur Event Hubs reçoit l'événement, puis l'application télécharge le blob et compte le nombre de lignes du fichier. L'application affiche un lien vers une page contenant le nombre de lignes. Lorsque l'utilisateur clique sur le lien, un CloudEvent contenant le nom du fichier est publié à l'aide d'Event Grid.

## <a name="next-steps"></a>Étapes suivantes
Si vous avez besoin d’une aide supplémentaire, publiez votre problème sur le forum [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou ouvrez un [ticket de support](https://azure.microsoft.com/support/options/). 
