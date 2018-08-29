---
title: Abonnements Azure Event Grid via le portail
description: Décrit comment créer des abonnements Event Grid via le portail.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: tomfitz
ms.openlocfilehash: 72eaa17e78086a4e5338bb3198ef7471c44b785f
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234691"
---
# <a name="subscribe-to-events-through-portal"></a>S’abonner à des événements via le portail

Cet article décrit comment créer des abonnements Event Grid via le portail.

## <a name="create-event-subscriptions"></a>Créer des abonnements aux événements

Pour créer un abonnement Event Grid pour une des [sources d’événements](event-sources.md) prises en charge, procédez selon les étapes suivantes. Cet article explique comment créer un abonnement Event Grid pour un abonnement Azure.

1. Sélectionnez **Tous les services**.

   ![Sélectionner Tous les services](./media/subscribe-through-portal/select-all-services.png)

1. Recherchez **Abonnements Event Grid**  et sélectionnez cette option parmi celles disponibles.

   ![Recherche](./media/subscribe-through-portal/search.png)

1. Sélectionnez **+ Abonnement aux événements**.

   ![Ajouter un abonnement](./media/subscribe-through-portal/add-subscription.png)

1. Sélectionnez le type d’abonnement que vous voulez créer. Par exemple, pour vous abonner à des événements pour votre abonnement Azure, sélectionnez **Abonnements Azure** et l’abonnement cible.

   ![Sélectionner un abonnement Azure](./media/subscribe-through-portal/azure-subscription.png)

1. Pour vous abonner à tous les types d’événements pour cette source d’événements, conservez l’option **S’abonner à tous les types d’événements** activée. Sinon, sélectionnez les types d’événements pour cet abonnement.

   ![Sélectionner des types d’événements](./media/subscribe-through-portal/select-event-types.png)

1. Spécifiez des détails supplémentaires sur l’abonnement aux événements, comme le point de terminaison pour la gestion des événements et un nom d’abonnement.

   ![Spécifier les détails de l’abonnement](./media/subscribe-through-portal/provide-subscription-details.png)

## <a name="create-subscription-on-resource"></a>Créer un abonnement sur une ressource

Certaines sources d’événements prennent en charge la création d’un abonnement aux événements via l’interface du portail pour cette ressource. Sélectionnez la source d’événements et recherchez **Événements** dans le volet gauche.

![Spécifier les détails de l’abonnement](./media/subscribe-through-portal/resource-events.png)

Le portail présente des options pour la création d’un abonnement aux événements qui se rapportent à cette source.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la remise d’événements et sur les nouvelles tentatives, consultez [Remise et nouvelle tentative de remise de messages avec Azure Grid](delivery-and-retry.md).
* Pour une présentation d’Event Grid, consultez [À propos d’Event Grid](overview.md).
* Pour une prise en main rapide d’Event Grid, consultez [Créer et acheminer des événements personnalisés avec Azure Event Grid](custom-event-quickstart.md).
