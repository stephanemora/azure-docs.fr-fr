---
title: Plans et quotas de service pour Azure Spring Cloud
description: Découvrez les quotas de service et les plans de service pour Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b02ccb3acb4546e08e7d58159ab9d85bca2d0eed
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877410"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quotas et plans de service pour Azure Spring Cloud

**Cet article s’applique à :** ✔️ Java ✔️ C#

Tous les services Azure définissent des quotas et des limites par défaut pour les ressources et fonctionnalités.   Azure Spring Cloud offre deux niveaux tarifaires : De base et Standard. Nous allons détailler les limites des deux niveaux dans cet article.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Niveaux de service et limites Azure Spring Cloud

| Ressource | Étendue | De base | Standard
------- | ------- | -------
Processeurs virtuels | par instance d’application | 1 | 4
Mémoire | par instance d’application | 2 Go | 8 Go
Instances de service Azure Spring Cloud | par région et par abonnement | 10 | 10
Nombre total d’instances d’application | par instance de service Azure Spring Cloud | 25 | 500
Domaines personnalisés | par instance de service Azure Spring Cloud | 0 | 25 
Volumes persistants | par instance de service Azure Spring Cloud | 1 Go/application x 10 applications | 50 Go/application x 10 applications

> [!TIP]
> Les limites indiquées pour le nombre total d’instances d’application par instance de service s’appliquent aux applications et aux déploiements dans n’importe quel état, y compris l’état arrêté. Supprimez les applications ou les déploiements qui ne sont pas utilisés.

## <a name="next-steps"></a>Étapes suivantes

Certaines limites par défaut peuvent être augmentées. Si votre configuration nécessite une augmentation, [créez une demande de support](../azure-portal/supportability/how-to-create-azure-support-request.md).
