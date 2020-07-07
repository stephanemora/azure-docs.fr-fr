---
title: Rubriques de partenaire Azure Event Grid
description: Envoyez des événements de partenaires SaaS et PaaS Event Grid tiers directement à des services Azure avec Azure Event Grid.
services: event-grid
author: femila
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: femila
ms.openlocfilehash: 5327efea8af734c723ba76d1a00b72c08f5c88b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84560267"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Rubriques de partenaire dans Azure Event Grid (préversion)
Les rubriques partenaires vous permettent de connecter des sources d’événements tiers directement à Azure Event Grid. Cette intégration vous permet de vous abonner à des événements de partenaires de la même façon que vous vous abonnez à des événements de services Azure. 

## <a name="available-partners"></a>Partenaires disponibles
Le premier partenaire disponible via les rubriques de partenaire Event Grid est Auth0. Vous pouvez utiliser la [rubrique de partenaire Auth0](auth0-overview.md) pour connecter vos comptes Auth0 et Azure. L’intégration vous permet de traiter, journaliser et surveiller les événements Auth0 en temps réel.

[Essayez-le](auth0-how-to.md) en vous connectant à votre compte Auth0 et en créant une intégration Event Grid. Après avoir sélectionné **Créer** dans Auth0, vous voyez une rubrique Auth0 en attente dans votre compte Azure. Sélectionnez **Activer** pour créer des abonnements Event Grid afin d’acheminer, filtrer et remettre vos événements comme pour toute autre source d’événement.

## <a name="pricing"></a>Tarifs
Les rubriques de partenaires sont facturées au même tarif que les rubriques système.

## <a name="limits"></a>limites
Les rubriques de partenaires sont disponibles en préversion publique. Pendant la préversion publique, les rubriques de partenaires sont soumises aux [mêmes limites](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits) que des rubriques système et personnalisées.

## <a name="how-do-i-become-an-event-grid-partner"></a>Comment devenir partenaire Event Grid ?
L’infrastructure créée pour ce lancement permet aux nouveaux partenaires d’intégrer facilement et rapidement leurs capacités de gestion d’événements avec Event Grid. Pour plus d’informations, consultez la [documentation relative à l’intégration des partenaires](partner-onboarding-overview.md).

## <a name="next-steps"></a>Étapes suivantes

- [Rubrique de partenaire Auth0](auth0-overview.md)
- [Comment utiliser la rubrique de partenaire Auth0](auth0-how-to.md)
- [Devenir partenaire Event Grid](partner-onboarding-overview.md)