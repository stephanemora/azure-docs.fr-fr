---
title: Plans et quotas de service pour Azure Spring Cloud
description: Découvrez les quotas de service et les plans de service pour Azure Spring Cloud.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 0518b13ea228b4834a095a9bf126b131e70a5f45
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851551"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quotas et plans de service pour Azure Spring Cloud

Tous les services Azure définissent des quotas et des limites par défaut pour les ressources et fonctionnalités.  Pendant la période de préversion, Azure Spring Cloud n’offre qu’un seul plan de service.

Cet article détaille les quotas de service proposés pendant la période de préversion actuelle.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Niveaux de service et quotas Azure Spring Cloud

Pendant la période de préversion, Azure Spring Cloud n’offre qu’un seul niveau de service.

Ressource | Montant
------- | -------
Processeurs virtuels | 4 par instance de service
Mémoire | 8 Go par instance de service
Instances du service Azure Spring Cloud par région et par abonnement | 10
Nombre total d’instances d’application par instance du service Azure Spring Cloud | 500
Nombre total d’instances d’application par application Spring | 20
Volumes persistants | 10 x 50 Go

Quand vous atteignez un quota, vous recevez une erreur 400 qui indique : « Le quota dépasse la limite pour l’abonnement *votre abonnement* dans la région *région où votre service Cloud Azure Spring est créé*.

## <a name="next-steps"></a>Étapes suivantes

Certains quotas et limites par défaut peuvent être augmentés. Si votre ressource nécessite une augmentation, [créez une demande de support](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).
