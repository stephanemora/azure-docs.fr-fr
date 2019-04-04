---
title: Résoudre les erreurs d’AMQP dans Azure Service Bus | Microsoft Docs
description: Fournit une liste d’erreurs AMQP vous pouvez recevoir à l’aide d’Azure Service Bus et provoquer de ces erreurs.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2019
ms.author: aschhab
ms.openlocfilehash: 85d24a9e7c753ec5dba80c4f259dd3fb51d9c14b
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58910044"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Erreurs d’AMQP dans Azure Service Bus
Cet article fournit certaines des erreurs que s’affiche à l’utilisation d’AMQP avec Azure Service Bus. Ils sont tous les comportements standards du service. Vous pouvez les éviter en effectuant des appels d’envoi/réception sur le/lien de connexion, ce qui recrée automatiquement le lien de connexion /.

## <a name="link-is-closed"></a>Lien est fermé. 
Vous voyez l’erreur suivante lors de la connexion AMQP et un lien sont actives, mais aucun appel (par exemple, envoi ou réception) sont établies en utilisant le lien pendant 10 minutes. Par conséquent, le lien est fermé. La connexion est toujours ouverte.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>Connexion est fermée.
Vous voyez l’erreur suivante sur la connexion AMQP lorsque tous les liens dans la connexion ont été fermés, car il y a aucune activité (inactif) et un nouveau lien n’a pas été créé en 5 minutes.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Lien n’est pas créé. 
Vous voyez cette erreur quand une nouvelle connexion AMQP est créée, mais un lien n’est pas créé dans la minute de la création de la connexion AMQP.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur AMQP et Service Bus, consultez les liens suivants :

* [Vue d’ensemble d’AMQP de service Bus]
* [Guide du protocole AMQP 1.0]
* [AMQP dans Service Bus pour Windows Server]

[Vue d’ensemble d’AMQP de service Bus]: service-bus-amqp-overview.md
[Guide du protocole AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP dans Service Bus pour Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
