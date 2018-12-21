---
title: Qu’arrive-t-il à Azure Batch AI ? | Microsoft Docs
description: Découvrez ce qui se passe pour Azure Batch AI et l’option de calcul Azure Machine Learning service.
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 12/14/2018
ms.author: garye
ms.openlocfilehash: 6803a47ae77c072eff05df65e37156c90aabf3e6
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436872"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Qu’arrive-t-il à Azure Batch AI ?

**Le service Azure Batch AI sera mis hors service en mars.** Les fonctionnalités de formation et de notation à l’échelle de Batch AI sont désormais disponibles dans [Azure Machine Learning service](../machine-learning/service/overview-what-is-azure-ml.md), qui a été mis à la disposition générale le 4 décembre 2018.

Entre autres fonctionnalités de Machine Learning, Azure Machine Learning service inclut une cible de calcul gérée sur le cloud pour les modèles Machine Learning de formation, de déploiement et de notation. Cette cible de calcul est appelée [Capacité de calcul Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Commencez à migrer et à l’utiliser dès aujourd’hui](#migrate). Vous pouvez interagir avec Azure Machine Learning service via ses [Kits de développement logiciel (SDK) Python](../machine-learning/service/quickstart-create-workspace-with-python.md), son interface de ligne de commande et le [portail Azure](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Chronologie de prise en charge

| Date | Détails de prise en charge du service Batch AI |
| ---- |-----------------|
| &nbsp;14&nbsp; décembre 2018| Continuez à utiliser vos abonnements Azure Batch AI existants comme avant. Toutefois, l’inscription de **nouveaux abonnements** n’est plus possible et aucun nouvel investissement ne sera engagé sur ce service.|
| &nbsp;31&nbsp; mars 2019 | Après cette date, les abonnements Batch AI existants ne fonctionneront plus. |

<a name="migrate"></a>
## <a name="how-do-i-migrate"></a>Comment effectuer la migration ?

Pour éviter les interruptions de vos applications et tirer parti des dernières fonctionnalités, procédez comme suit avant le 31 mars 2019 :

1. Créer un espace de travail Azure Machine Learning service et le prendre en main :
    + [Démarrage rapide basé sur Python](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Démarrage rapide basé sur le portail Azure](../machine-learning/service/quickstart-get-started.md)

1. Configurez [Capacité de calcul Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) pour l’entraînement de modèle.

1. Mettez à jour vos scripts pour utiliser Capacité de calcul Machine Learning.

## <a name="support"></a>Support

Une prise en charge est disponible pour les clients existants souhaitant migrer vers l’[Azure Machine Learning service](https://aka.ms/aml-docs) le plus complet.

Si Azure Machine Learning service ne répond pas à votre besoin tel qu’une fonctionnalité prise en charge existe dans le service Batch AI, formulez une requête de prise en charge Batch AI auprès de l’équipe de support pour votre abonnement en liste verte afin de pouvoir utiliser Batch AI jusqu’à ce que la suppression du service.

## <a name="next-steps"></a>Étapes suivantes

+ Lisez la [Vue d’ensemble d’Azure Machine Learning service](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Configurez une cible de calcul pour l’entraînement de modèle](../machine-learning/service/how-to-set-up-training-targets.md) avec Azure Machine Learning service.

+ Passez en revue la [feuille de route Azure](https://azure.microsoft.com/updates/) pour découvrir d’autres mises à jour du service Azure.
