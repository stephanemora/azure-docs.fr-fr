---
title: Résoudre des erreurs AMQP dans Azure Event Hubs | Microsoft Docs
description: Fournit une liste des erreurs AMQP qui peuvent s’afficher lors de l’utilisation d’Azure Event Hubs, ainsi que la cause de ces erreurs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 51b96792f6921bae9364212c6e5f9c987ff05e2a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466063"
---
# <a name="amqp-errors-in-azure-event-hubs"></a>Erreurs AMQP dans Azure Event Hubs
Cet article présente certaines des erreurs qui s’affichent lors de l’utilisation d’AMQP avec Azure Event Hubs. Il s’agit de comportements standard du service. Vous pouvez les éviter en établissant des appels d’envoi ou de réception sur la connexion/liaison, ce qui a pour effet de recréer celles-ci automatiquement.

## <a name="link-is-closed"></a>La liaison est fermée 
L’erreur suivante s’affiche lorsque la connexion et la liaison AMQP sont actives mais qu’aucun appel (d’envoi ou de réception) n’est établi à l’aide de la liaison pendant 30 minutes. La liaison est alors fermée. Mais la connexion est toujours ouverte.

« AMQP:link: detach-forced: La liaison "G2:7223832 : user.tenant0.cud_00000000000-0000-0000-0000-00000000000000" est détachée de force par le répartiteur en raison d’erreurs qui se sont produites dans l’éditeur (link164614). Origine du détachement : AmqpMessagePublisher.IdleTimerExpired: Délai d’inactivité : 00:30:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp : 2/16/2018 11:10:40 PM »

## <a name="connection-is-closed"></a>La connexion est fermée
L’erreur suivante s’affiche sur la connexion AMQP lorsque toutes les liaisons de celle-ci ont été fermées parce qu’il n’y a eu aucune activité et qu’aucune nouvelle liaison n’a été créée en l’espace de cinq minutes.

« Error{condition=amqp:connection:forced, description="La connexion a été inactive pendant plus de 300 000 millisecondes et est fermée par le conteneur ‘LinkTracker’. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp : 2019-03-06T17:32:00", info=null} »

## <a name="link-isnt-created"></a>La liaison n’est pas créée 
Cette erreur s’affiche quand une nouvelle connexion AMQP est créée, mais qu’aucune liaison n’est créée dans la minute suivant la création de la connexion AMQP.

« Error{condition=amqp:connection:forced, description="La connexion a été inactive pendant plus de 60 000 millisecondes et est fermée par le conteneur ‘LinkTracker’. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp : 2019-03-06T18:41:51", info=null} »

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur AMQP, consultez le [guide du protocole AMQP 1.0](../service-bus-messaging/service-bus-amqp-protocol-guide.md).
