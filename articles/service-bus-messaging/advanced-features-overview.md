---
title: Messages Azure Service Bus – Fonctionnalités avancées
description: Cet article donne une vue d’ensemble des fonctionnalités avancées d’Azure Service Bus.
ms.topic: overview
ms.date: 06/11/2021
ms.openlocfilehash: 80fab956c182b6f934a767f2dffc09ca2d0c0214
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112034817"
---
# <a name="azure-service-bus---advanced-features"></a>Azure Service Bus – Fonctionnalités avancées
Certaines fonctionnalités avancées de Service Bus sont utiles pour résoudre des problèmes de messagerie plus complexes. Cet article décrit plusieurs d’entre elles.

## <a name="message-sessions"></a>Sessions de message
Pour créer une garantie FIFO (pour « First In First Out », ou « Premier entré, premier sorti ») dans Service Bus, utilisez des sessions. Les sessions de message permettent un traitement exclusif et chronologique de séquences illimitées de messages associés. Pour permettre la gestion des sessions dans les systèmes à grande échelle et à haute disponibilité, la fonctionnalité de session permet également de stocker l’état de session, ce qui permet aux sessions de se déplacer de façon sécurisée entre les gestionnaires. Si vous souhaitez en savoir plus, veuillez consulter l’article [Sessions de messagerie : premier entré, premier sorti (FIFO)](message-sessions.md).

## <a name="autoforwarding"></a>Transfert automatique
La fonctionnalité de transfert automatique crée une chaîne reliant une file d’attente ou un abonnement à une autre file d’attente ou rubrique à l’intérieur du même espace de noms. Lorsque vous utilisez cette fonctionnalité, Service Bus déplace automatiquement les messages d’une file d’attente ou d’un abonnement dans une file d’attente ou rubrique cible. Tous ces déplacements sont effectués de manière transactionnelle. Si vous souhaitez en savoir plus, veuillez consulter l’article [Chaînage des entités Service Bus avec transfert automatique](service-bus-auto-forwarding.md).

## <a name="dead-letter-queue"></a>File d’attente de lettres mortes
Tous les abonnements des files d’attente et des rubriques Service Bus disposent de files d’attente de lettres mortes associées. Une file d’attente de lettres mortes contient les messages qui répondent aux critères suivants : 

- Ils ne peuvent pas être remis correctement à un destinataire.
- Leur délai d’attente a expiré.
- Ils sont explicitement écartés par l’application destinataire. 

Les messages, dans la file d’attente de lettres mortes, sont annotés selon la raison pour laquelle ils y ont été placés. La file d’attente de lettres mortes possède un point de terminaison particulier, mais agit sinon comme n’importe quelle file d’attente normale. Une application ou un outil peut parcourir une file d’attente de lettres mortes, ou enlever des message de celle-ci. Vous pouvez également procéder à un transfert automatique à partir d’une file d’attente de lettres mortes. Pour en savoir plus, consultez [Vue d’ensemble des files d’attente de lettres mortes Service Bus](service-bus-dead-letter-queues.md).

## <a name="scheduled-delivery"></a>Remise planifiée
Vous pouvez envoyer des messages à une file d’attente ou à une rubrique pour un traitement différé, en définissant l’heure à laquelle le message sera disponible à la consommation. Les messages planifiés peuvent également être annulés. Si vous souhaitez en savoir plus, veuillez consulter la rubrique [Messages planifiés](message-sequencing.md#scheduled-messages).

## <a name="message-deferral"></a>Report de message
Un client de file d’attente ou d’abonnement peut reporter la récupération d’un message reçu à une date ultérieure définie. Le message a peut-être été publié selon un ordre attendu, et le client veut attendre de recevoir un autre message. Les messages différés restent dans la file d’attente ou l’abonnement ; ils doivent être réactivés explicitement au moyen de leur numéro de séquence assigné par le service. Si vous souhaitez en savoir plus, veuillez consulter l’article [Report de message](message-deferral.md).

## <a name="batching"></a>Traitement par lot
Le traitement par lot côté client permet à un client de file d’attente ou de rubrique d’accumuler un ensemble de messages pour les transférer ensemble. Cette pratique est souvent suivie pour économiser la bande passante ou augmenter le débit. Si vous souhaitez en savoir plus, veuillez consulter la rubrique [Traitement par lots côté client](service-bus-performance-improvements.md#client-side-batching).

## <a name="transactions"></a>Transactions
Une transaction regroupe plusieurs opérations dans une étendue d’exécution. Service Bus vous permet de grouper les opérations par rapport à plusieurs entités de messagerie dans l’étendue d’une seule transaction. Une entité de message peut être une file d’attente, une rubrique ou un abonnement. Si vous souhaitez en savoir plus, veuillez consulter l’article [Vue d’ensemble du traitement des transactions Service Bus](service-bus-transactions.md).

## <a name="autodelete-on-idle"></a>Suppression automatique en cas d’inactivité
La fonctionnalité de suppression automatique en cas d’inactivité vous permet de spécifier un intervalle d’inactivité à l’issue duquel une file d’attente ou un abonnement à une rubrique est automatiquement supprimé. La durée minimale est de 5 minutes. 

## <a name="duplicate-detection"></a>Détection des doublons
La fonctionnalité de détection des doublons permet à l’expéditeur de renvoyer le même message une nouvelle fois, et au répartiteur d’éliminer un doublon potentiel. Pour en savoir plus, consultez [Détection des doublons](duplicate-detection.md).

## <a name="geo-disaster-recovery"></a>Géorécupération d’urgence
Lorsqu’une région Azure subit un temps d’arrêt, la fonctionnalité de récupération d’urgence permet de poursuivre le traitement des messages dans une autre région ou un autre centre de données. La fonctionnalité conserve le miroir structurel d’un espace de noms disponible dans la région secondaire, et permet à l’identité de l’espace de noms de basculer vers l’espace de noms secondaire. Les messages déjà publiés sont conservés dans l’ancien espace de noms principal, en vue de la reprise d’activité une fois l’incident de disponibilité éloigné. Pour plus d’informations, consultez [Géorécupération d’urgence Azure Service Bus](service-bus-geo-dr.md).

## <a name="security"></a>Sécurité
Service Bus prend en charge les protocoles standard [AMQP 1.0](service-bus-amqp-overview.md) et [HTTP ou REST](/rest/api/servicebus/) ainsi que leurs installations de sécurité respectives, y compris la sécurité au niveau du transport (TLS). Les clients peuvent être autorisés à accéder par l’intermédiaire de la sécurité basée sur les rôles [Signature d’accès partagé](service-bus-sas.md) ou [Azure Active Directory](service-bus-authentication-and-authorization.md). 

Pour la protection contre le trafic indésirable, Service Bus fournit des [fonctionnalités de sécurité](network-security.md), telles que le pare-feu IP et l’intégration aux réseaux virtuels. 

## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment utiliser ces fonctionnalités de Service Bus, consultez [Exemples de messages Service Bus](service-bus-samples.md).
