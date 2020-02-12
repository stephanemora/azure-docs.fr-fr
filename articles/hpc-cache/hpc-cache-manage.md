---
title: Gérer et mettre à jour Azure HPC Cache
description: Comment gérer et mettre à jour Azure HPC Cache à l’aide du Portail Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: rohogue
ms.openlocfilehash: 9ad6348e15c8a25f721a89be7eab3e17c58ae17c
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988849"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Gérer votre cache à partir du Portail Azure

La page aperçu du cache du Portail Azure affiche les détails du projet, l’état du cache et les statistiques de base de votre cache. Elle dispose également de contrôles pour arrêter ou démarrer le cache, supprimer le cache, vider les données dans un stockage à long terme et mettre à jour les logiciels.

Pour ouvrir la page d’aperçu, sélectionnez votre ressource de cache dans le Portail Azure. Par exemple, chargez la page **Toutes les ressources** et cliquez sur le nom du cache.

![capture d’écran d’une page d’aperçu de l’instance de cache Azure HPC](media/hpc-cache-overview.png)

Les boutons situés en haut de la page peuvent vous aider à gérer le cache :

* **Démarrer** et [**Arrêter**](#stop-the-cache) : interrompt l’opération de mise en cache
* [**Vider**](#flush-cached-data) : écrit les données modifiées dans les cibles de stockage
* [**Mettre à niveau**](#upgrade-cache-software) : met à jour le logiciel de cache
* **Actualiser** : recharge la page d’aperçu
* [**Supprimer**](#delete-the-cache) : supprime définitivement le cache

Apprenez-en davantage sur ces options ci-dessous.

## <a name="stop-the-cache"></a>Arrêter le cache

Vous pouvez arrêter le cache pour réduire les coûts pendant une période inactive. Vous n’êtes pas facturé pour la durée de fonctionnement lorsque le cache est arrêté, mais vous l’êtes pour le stockage sur disque alloué au cache. (Pour plus d’informations, voir la page de [tarification](https://aka.ms/hpc-cache-pricing).)

Un cache arrêté ne répond pas aux requêtes des clients. Vous devez démonter les clients avant d’arrêter le cache.

Le bouton **Arrêter** interrompt un cache actif. Le bouton **Arrêter** est disponible lorsque l’état d’un cache est **Sain** ou **Détérioré**.

![capture d’écran des boutons du haut avec Arrêter mis en surbrillance et un message contextuel décrivant l’action d’arrêt et demandant « voulez-vous continuer ? » avec les boutons Oui (par défaut) et Non](media/stop-cache.png)

Une fois que vous avez cliqué sur Oui pour confirmer l’arrêt du cache, le cache vide automatiquement son contenu dans les cibles de stockage. Ce processus peut prendre un certain temps, mais il garantit la cohérence des données. Enfin, l’état du cache passe à **Arrêté**.

Pour réactiver un cache arrêté, cliquez sur le bouton **Démarrer**. Aucune confirmation n’est nécessaire.

![capture d’écran des boutons du haut avec Démarrer mis en surbrillance](media/start-cache.png)

## <a name="flush-cached-data"></a>Vider les données en cache

Le bouton **Vider** de la page d’aperçu indique au cache d’écrire immédiatement toutes les données modifiées stockées dans le cache sur les cibles de stockage back-end. Le cache enregistre régulièrement les données sur les cibles de stockage. il n’est donc pas nécessaire de le faire manuellement, sauf si vous souhaitez vous assurer que le système de stockage back-end est à jour. Par exemple, vous pouvez utiliser **Vider** avant de créer un instantané de stockage ou de vérifier la taille du jeu de données.

> [!NOTE]
> Pendant le processus de vidage, le cache ne peut pas traiter les demandes des clients. L’accès au cache est suspendu et reprend une fois l’opération terminée.

![capture d’écran des boutons du haut avec Vider mis en surbrillance et un message contextuel décrivant l’action de vidage et demandant « voulez-vous continuer ? » avec les boutons Oui (par défaut) et Non](media/hpc-cache-flush.png)

Lorsque vous démarrez l’opération de vidage du cache, le cache cesse d’accepter les requêtes du client et l’état du cache sur la page d’aperçu passe à **Vidage**.

Les données du cache sont enregistrées dans les cibles de stockage appropriées. Selon la quantité de données qui doit être vidée, le processus peut prendre quelques minutes ou plus d’une heure.

Une fois que toutes les données ont été enregistrées sur les cibles de stockage, le cache recommence automatiquement à réexécuter les requêtes des clients. L’état du cache revient à **Sain**.

## <a name="upgrade-cache-software"></a>Mettre à niveau le logiciel du cache

Si une nouvelle version du logiciel est disponible, le bouton **Mettre à niveau** devient actif. Un message peut également s’afficher en haut de la page sur la mise à jour de logiciel.

![capture d’écran de la première ligne de boutons avec le bouton Mettre à niveau activé](media/hpc-cache-upgrade-button.png)

L’accès client n’est pas interrompu pendant une mise à niveau de logiciel, mais les performances du cache ralentissent. Planifiez la mise à niveau des logiciels pendant les heures d’utilisation creuses ou pendant une période de maintenance planifiée.

La mise à jour de logiciel peut prendre plusieurs heures. Les caches configurés avec un débit plus élevé prennent plus de temps à mettre à niveau les caches avec des valeurs de débit de pic plus faibles.

Lorsqu’une mise à niveau de logiciel est disponible, il faudra une semaine environ pour l’appliquer manuellement. La date de fin est indiquée dans le message de mise à niveau. Si vous n’effectuez pas de mise à niveau pendant cette période, Azure applique automatiquement la mise à jour à votre cache. Le minutage de la mise à niveau automatique n’est pas configurable. Si vous vous inquiétez de l’impact sur les performances du cache, vous devez vous-même mettre à niveau le logiciel avant l’expiration du délai.

Cliquez sur le bouton **Mettre à niveau** pour lancer la mise à jour de logiciel. L’état du cache prend la valeur **Mise à niveau** jusqu’à ce que l’opération se termine.

## <a name="delete-the-cache"></a>Supprimer le cache

Le bouton **Supprimer** détruit le cache. Lorsque vous supprimez un cache, toutes ses ressources sont détruites et n’occasionnent plus de frais de compte.

Les volumes de stockage back-end utilisés comme cibles de stockage ne sont pas affectés quand vous supprimez le cache. Vous pouvez les ajouter ultérieurement à un cache futur ou les désactiver séparément.

> [!NOTE]
> Azure HPC Cache n’écrit pas automatiquement les données modifiées du cache sur les systèmes de stockage back-end avant de supprimer le cache.
>
> Pour vous assurer que toutes les données du cache ont été écrites dans un stockage à long terme, [arrêtez le cache](#stop-the-cache) avant de le supprimer. Assurez-vous qu’il affiche l’état **Arrêté** avant de cliquer sur le bouton de suppression.
<!--... written to long-term storage, follow this procedure:
>
> 1. [Remove](hpc-cache-edit-storage.md#remove-a-storage-target) each storage target from the Azure HPC Cache by using the delete button on the Storage targets page. The system automatically writes any changed data from the cache to the back-end storage system before removing the target.
> 1. Wait for the storage target to be completely removed. The process can take an hour or longer if there is a lot of data to write from the cache. When it is done, a portal notification says that the delete operation was successful, and the storage target disappears from the list.
> 1. After all affected storage targets have been deleted, it is safe to delete the cache.
>
> Alternatively, you can use the [flush](#flush-cached-data) option to save cached data, but there is a small risk of losing work if a client writes a change to the cache after the flush completes but before the cache instance is destroyed.-->

## <a name="cache-metrics-and-monitoring"></a>Mesures et supervision du cache

La page d’aperçu affiche des graphiques pour des statistiques de cache de base, le débit du cache, les opérations par seconde et la latence.

![capture d’écran de trois graphiques linéaires montrant les statistiques mentionnées ci-dessus pour un exemple de cache](media/hpc-cache-overview-stats.png)

Ces graphiques font partie des outils de surveillance et d’analyse intégrés d’Azure. Des outils et des alertes supplémentaires sont disponibles à partir des pages sous l’en-tête **Surveillance** dans la barre latérale du portail. Pour plus d’informations, consultez la section portail de la [documentation Surveillance Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Étapes suivantes

<!-- * Learn more about metrics and statistics for hpc cache -->
* En savoir plus sur les [outils de mesures et statistiques Azure](../azure-monitor/index.yml)
* Obtenir [de l’aide avec Azure HPC Cache](hpc-cache-support-ticket.md)
