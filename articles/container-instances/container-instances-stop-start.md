---
title: Arrêter ou démarrer des conteneurs dans Azure Container Instances manuellement
description: Découvrez comment arrêter ou démarrer un groupe de conteneurs dans Azure Container Instances manuellement.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 50f3ecf69561313a5bda67827cfb02d2f61d461f
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610218"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Arrêter ou démarrer des conteneurs dans Azure Container Instances manuellement

Le [stratégie de redémarrage](container-instances-restart-policy.md) d’un groupe conteneur détermine comment les instances de conteneur démarrer ou arrêter par défaut. Vous pouvez remplacer le paramètre par manuellement l’arrêt ou le démarrage d’un groupe de conteneurs par défaut.

## <a name="stop"></a>Arrêter

Arrêtez manuellement un groupe de conteneurs en cours d’exécution - par exemple, à l’aide de la [stop de conteneur az] [ az-container-stop] commande ou le portail Azure. Pour certaines charges de travail de conteneur, vous souhaiterez peut-être arrêter un groupe de conteneurs longue après une période définie pour réduire les coûts. 

*Lorsqu’un groupe de conteneurs entre en état arrêté, il se termine et recycle tous les conteneurs dans le groupe. Il ne conserve pas l’état du conteneur.*

Bien que les conteneurs dans un groupe de conteneurs arrêté sont recyclées, le [ressources](container-instances-container-groups.md#resource-allocation) restent allouées pour votre usage. Par conséquent, la facturation continue pour un groupe de conteneurs arrêté.

L’action d’arrêt n’a aucun effet si le groupe de conteneurs est déjà terminée (est en état d’une opération réussie ou échec). Par exemple, un groupe de conteneurs avec les tâches de conteneur d’exécution unique qui a été correctement exécutée se termine dans l’état de réussite. Tente d’arrêter le groupe dans la mesure état ne changent pas l’état. 

## <a name="start"></a>Démarrer

Quand un groupe de conteneurs est arrêté - soit parce que les conteneurs s’est arrêté sur leurs propres ou que vous avez arrêté manuellement le groupe - vous pouvez démarrer les conteneurs. Par exemple, utiliser le [début du conteneur az] [ az-container-start] commande ou le portail Azure pour démarrer manuellement les conteneurs dans le groupe. Si l’image d’un conteneur est mise à jour, une nouvelle image est extraite. 

Le démarrage d’un groupe de conteneurs commence un nouveau déploiement avec la même configuration de conteneur. Cette action peut vous aider à réutiliser rapidement une configuration de groupe de conteneurs connue qui fonctionne comme prévu. Vous n’avez pas besoin de créer un nouveau groupe de conteneurs pour exécuter la même charge de travail.

Tous les conteneurs dans un groupe de conteneurs sont démarrés par cette action. Impossible de démarrer un conteneur spécifique dans le groupe.

Après que vous avez démarré ou redémarré manuellement un groupe de conteneurs, le groupe de conteneurs fonctionne conformément à la stratégie de redémarrage configurée.
  
## <a name="restart"></a>Redémarrer

Vous pouvez redémarrer un groupe de conteneurs en cours - par exemple, à l’aide de la [redémarrage du conteneur az] [ az-container-restart] commande. Cette action redémarre tous les conteneurs du groupe de conteneurs. Si l’image d’un conteneur est mise à jour, une nouvelle image est extraite. 

Le redémarrage d’un groupe de conteneurs est utile lorsque vous souhaitez résoudre un problème de déploiement. Par exemple, si une limitation temporaire des ressources empêche le bon fonctionnement de vos conteneurs, le redémarrage du groupe peut résoudre le problème.

Tous les conteneurs dans un groupe de conteneurs sont redémarrés par cette action. Vous ne pouvez pas redémarrer un conteneur spécifique dans le groupe.

Après avoir redémarré manuellement un groupe de conteneurs, les exécutions de groupe conteneur en fonction de la stratégie de redémarrage.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [les paramètres de stratégie de redémarrage](container-instances-restart-policy.md) dans Azure Container Instances.

Outre manuellement l’arrêt et démarrage d’un groupe de conteneur avec la configuration existante, vous pouvez [mettre à jour les paramètres](container-instances-update.md) d’un groupe de conteneurs en cours d’exécution.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
