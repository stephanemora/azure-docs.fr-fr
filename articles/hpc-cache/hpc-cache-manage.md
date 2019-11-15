---
title: Gérer et mettre à jour Azure HPC Cache
description: Comment gérer et mettre à jour Azure HPC Cache à l’aide du Portail Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 62b54bfe120acdde1fd22c4a0d04165ea7243b50
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582196"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Gérer votre cache à partir du Portail Azure

La page aperçu du cache du Portail Azure affiche les détails du projet, l’état du cache et les statistiques de base de votre cache. Elle dispose également de contrôles pour supprimer le cache, vider les données dans un stockage à long terme ou mettre à jour des logiciels.

Pour ouvrir la page d’aperçu, sélectionnez votre ressource de cache dans le Portail Azure. Par exemple, chargez la page **Toutes les ressources** et cliquez sur le nom du cache.

![capture d’écran d’une page d’aperçu de l’instance de cache Azure HPC](media/hpc-cache-overview.png) <!-- placeholder is identical to hpc-cache-new-overview.png; replace with better image (showing graphs, full sidebar) when available -->

Les boutons situés en haut de la page peuvent vous aider à gérer le cache :

* [**Vider**](#flush-cached-data) : écrit toutes les données en cache dans les cibles de stockage
* [**Mettre à niveau**](#upgrade-cache-software) : met à jour le logiciel de cache
* **Actualiser** : recharge la page d’aperçu
* [**Supprimer**](#delete-the-cache) : supprime définitivement le cache

Apprenez-en davantage sur ces options ci-dessous.

## <a name="flush-cached-data"></a>Vider les données en cache

Le bouton **Vider** de la page d’aperçu indique au cache d’écrire immédiatement toutes les données modifiées stockées dans le cache sur les cibles de stockage back-end. Le cache enregistre régulièrement les données sur les cibles de stockage. il n’est donc pas nécessaire de le faire manuellement, sauf si vous souhaitez vous assurer que le système de stockage back-end est à jour. Par exemple, vous pouvez utiliser **Vider** avant de créer un instantané de stockage ou de vérifier la taille du jeu de données.

> [!NOTE]
> Pendant le processus de vidage, le cache ne peut pas traiter les demandes des clients. L’accès au cache est suspendu et reprend une fois l’opération terminée.

Lorsque vous démarrez l’opération de vidage du cache, le cache cesse d’accepter les requêtes du client et l’état du cache sur la page d’aperçu passe à **Vidage**.

Les données du cache sont enregistrées dans les cibles de stockage appropriées. Le processus peut prendre quelques minutes ou peut prendre une heure ou plus, selon la quantité de données qui a été écrite récemment dans le cache.

Une fois que toutes les données ont été enregistrées sur les cibles de stockage, le cache recommence automatiquement à réexécuter les requêtes des clients. L’état du cache revient à **Sain**.

## <a name="upgrade-cache-software"></a>Mettre à niveau le logiciel du cache

Si une nouvelle version du logiciel est disponible, le bouton **Mettre à niveau** devient actif. Un message peut également s’afficher en haut de la page sur la mise à jour du logiciel.

![capture d’écran de la première ligne de boutons avec le bouton Mettre à niveau activé](media/hpc-cache-upgrade-button.png)

L’accès client n’est pas interrompu pendant une mise à niveau de logiciel, mais les performances du cache ralentissent. Planifiez la mise à niveau des logiciels pendant les heures d’utilisation creuses ou pendant une période de maintenance planifiée.

La mise à jour de logiciel peut prendre plusieurs heures. Les caches configurés avec un débit plus élevé prennent plus de temps à mettre à niveau les caches avec des valeurs de débit de pic plus faibles.

Quand une mise à niveau de logiciel est disponible, vous avez plusieurs jours pour l’appliquer manuellement. La date de fin est indiquée dans le message de mise à niveau. Si vous n’effectuez pas de mise à niveau pendant cette période, Azure applique automatiquement la mise à jour à votre cache. Le minutage de la mise à niveau automatique n’est pas configurable. Si vous vous inquiétez de l’impact sur les performances du cache, vous devez vous-même mettre à niveau le logiciel avant l’expiration du délai.

Cliquez sur le bouton **Mettre à niveau** pour lancer la mise à jour de logiciel. L’état du cache prend la valeur **Mise à niveau** jusqu’à ce que l’opération se termine.

## <a name="delete-the-cache"></a>Supprimer le cache

Le bouton **Supprimer** détruit le cache. Lorsque vous supprimez un cache, toutes ses ressources sont détruites et n’occasionnent plus de frais de compte.

Les cibles de stockage ne sont pas affectées lorsque vous supprimez le cache. Vous pouvez les ajouter ultérieurement à un cache futur ou les désactiver séparément.

Le cache vide automatiquement toutes les données non enregistrées sur les cibles de stockage dans le cadre de son arrêt final.

## <a name="cache-metrics-and-monitoring"></a>Mesures et supervision du cache

La page d’aperçu affiche des graphiques pour des statistiques de cache de base, le débit du cache, les opérations par seconde et la latence.

![capture d’écran de trois graphiques linéaires montrant les statistiques mentionnées ci-dessus pour un exemple de cache](media/hpc-cache-overview-stats.png)

Ces graphiques font partie des outils de surveillance et d’analyse intégrés d’Azure. Des outils et des alertes supplémentaires sont disponibles à partir des pages sous l’en-tête **Surveillance** dans la barre latérale du portail. Pour plus d’informations, consultez la section portail de la [documentation Surveillance Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Étapes suivantes

<!-- * Learn more about metrics and statistics for hpc cache -->
* En savoir plus sur les [outils de mesures et statistiques Azure](../azure-monitor/index.yml)
* Obtenir [de l’aide avec Azure HPC Cache](hpc-cache-support-ticket.md)
