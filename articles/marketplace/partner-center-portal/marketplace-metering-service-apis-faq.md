---
title: API de service de mesure de la Place de marché - Questions fréquentes (FAQ) | Place de marché Azure
description: Émettez l’utilisation d’une offre SaaS dans la place de marché Azure.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6e5b691a41ef283449f9eeeb90e9d01a91616146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275779"
---
# <a name="marketplace-metering-service-apis---faq"></a>API de service de mesure de la Place de marché - Questions fréquentes (FAQ)

Une fois qu’un utilisateur Azure s’est abonné à un service SaaS qui comprend la facturation limitée, vous devez effectuer le suivi de la consommation pour chaque dimension de facturation utilisée par le client. Si la consommation dépasse les quantités incluses définies pour le terme sélectionné par le client, votre service émet des événements d’utilisation à Microsoft.

## <a name="emit-usage-events"></a>Émettre des événements d’utilisation

>[!Note]
>Cette section ne s’applique qu’aux offres SaaS dont au moins l’un des plans a des dimensions de service de comptage définies au moment de la publication de l’offre.

![Émettre des événements d’utilisation](media/isv-emits-usage-event.png)

Pour plus d’informations sur le contrat d’API pour émettre des événements d’utilisation, consultez [API d’événements d’utilisation de lots SaaS](./marketplace-metering-service-apis.md#batch-usage-event).

### <a name="how-often-is-it-expected-to-emit-usage"></a>À quelle fréquence est-il supposé émettre l’utilisation ?

Dans l’idéal, vous devez émettre l’utilisation toutes les heures pour la dernière heure, uniquement s’il existe une utilisation au cours de l’heure précédente.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Quel est le délai maximal entre le moment où un événement se produit et le moment où un événement d’utilisation est émis à Microsoft ?

Dans l’idéal, l’événement d’utilisation est émis toutes les heures pour les événements qui se sont produits au cours de la dernière heure. Toutefois, des retards sont attendus. Le délai maximal autorisé est de 24 heures, après quoi les événements d’utilisation ne sont pas acceptés.

Par exemple, si un événement d’utilisation se produit à 13 h dans une journée, vous avez jusqu’à 13 h le lendemain pour émettre un événement d’utilisation associé à cet événement. Cela signifie que dans le cas où le système émetteur de l’utilisation a un temps d’arrêt, il peut récupérer et ensuite envoyer l’événement d’utilisation pour l’intervalle d’heure dans lequel l’utilisation a eu lieu, sans perte de fidélité.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>Que se passe-t-il lorsque vous envoyez plusieurs événements d’utilisation dans la même heure ?

Un seul événement d’utilisation est accepté pour l’intervalle d’heure. L’intervalle d’heure commence à la minute 0 et se termine à la minute 59.  Si plusieurs événements d’utilisation sont émis dans le même intervalle d’heure, tous les événements d’utilisation suivants sont abandonnés en tant que doublons.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Que se passe-t-il lorsque vous émettez une utilisation pour un abonnement SaaS qui a déjà été annulé ?

Tout événement d’utilisation émis sur la plateforme de la Place du marché n’est pas accepté après la suppression d’un abonnement SaaS.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Pouvez-vous obtenir une liste de tous les abonnements SaaS, y compris les abonnements actifs et désabonnés ?

Oui, lorsque vous appelez l’API `GET /saas/subscriptions`, elle comprend une liste de tous les abonnements SaaS. Le champ d’état dans la réponse pour chaque abonnement SaaS indique si l’abonnement est actif ou non. L’appel à la liste des abonnements renvoie un maximum de 100 abonnements à la fois.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations, consultez [API du service de mesure de la consommation de la Place de marché](./marketplace-metering-service-apis.md).
