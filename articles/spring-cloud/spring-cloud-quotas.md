---
title: Plans et quotas de service pour Azure Spring Cloud
description: Découvrez les quotas de service et les plans de service pour Azure Spring Cloud.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 9/27/2019
ms.author: jeconnoc
ms.openlocfilehash: 89934ee0ab0c901a904a1a5ac2fb620185571a23
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038684"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quotas et plans de service pour Azure Spring Cloud

Tous les services Azure définissent des quotas et des limites par défaut pour les ressources et fonctionnalités.  Pendant la période de préversion, Azure Spring Cloud n’offre qu’un seul plan de service.

Cet article détaille les quotas de service proposés pendant la période de préversion actuelle.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Niveaux de service et quotas Azure Spring Cloud

Pendant la période de préversion, Azure Spring Cloud n’offre qu’un seul niveau de service.

Ressource | Montant
------- | -------
Processeurs virtuels | 4
Mémoire | 8 Go
Abonnement Azure Spring Cloud | 1
Instances du service Azure Spring Cloud par région et par abonnement | 2
Nombre total d’instances d’application par instance du service Azure Spring Cloud | 50
Nombre total d’instances d’application par application Spring | 20
Volumes persistants | 10 x 50 Go

Quand vous atteignez un quota, vous recevez une erreur 400 qui indique : « Le quota dépasse la limite pour l’abonnement *votre abonnement* dans la région *région où votre service Cloud Azure Spring est créé*.

## <a name="next-steps"></a>Étapes suivantes

Certains quotas et limites par défaut peuvent être augmentés. Si votre ressource nécessite une augmentation, envoyez-nous votre demande : azure-spring-cloud@service.microsoft.com.
