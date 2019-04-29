---
title: Comment configurer la géoréplication pour le Cache Azure pour Redis | Microsoft Docs
description: Découvrez comment répliquer vos instances Cache Azure pour Redis dans des régions géographiques.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: 4254175955c3560c7bd0fdd08c6b60c318238b76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60552357"
---
# <a name="how-to-configure-geo-replication-for-azure-cache-for-redis"></a>Comment configurer la géoréplication pour le Cache Azure pour Redis

La géoréplication fournit un mécanisme permettant de lier deux instances de Cache Azure pour Redis de niveau Premium. Un cache est choisi en tant que le cache lié principal et l’autre en tant que le cache lié secondaire. Le cache lié secondaire est en lecture seule et les données écrites dans le cache principal sont répliquées vers le cache lié secondaire. Cette fonctionnalité peut être utilisée pour répliquer un cache entre des régions Azure. Cet article fournit un guide de configuration de la géoréplication pour vos instances de Cache Azure pour Redis de niveau Premium.

## <a name="geo-replication-prerequisites"></a>Conditions préalables à la géoréplication

Pour configurer la géoréplication entre deux caches, les conditions préalables suivantes doivent être remplies :

- Les deux caches sont [niveau Premium](cache-premium-tier-intro.md) met en cache.
- Les deux caches se trouvent dans le même abonnement Azure.
- Le cache lié secondaire est la même taille de cache ou une taille de cache supérieure au cache lié principal.
- Les deux caches sont créés et en cours d’exécution.

Certaines fonctionnalités ne sont pas pris en charge avec la géo-réplication :

- Persistance n’est pas pris en charge avec la géo-réplication.
- Le clustering est pris en charge si les deux caches ont le clustering est activé et ont le même nombre de partitions.
- Caches dans le même réseau virtuel sont pris en charge.
- Les caches de différents réseaux virtuels sont prises en charge avec les mises en garde. Consultez [puis-je utiliser géo-réplication avec mes caches dans un réseau virtuel ?](#can-i-use-geo-replication-with-my-caches-in-a-vnet) pour plus d’informations.

Une fois la géoréplication configurée, les restrictions suivantes s’appliquent à votre paire de caches liés :

- Le cache lié secondaire est en lecture seule. Il n’est pas possible d’y écrire des données. 
- Toutes les données présentes dans le cache lié secondaire avant l’ajout du lien sont supprimées. Si la géo-réplication est ultérieure supprimés en revanche, les données répliquées restent dans le cache lié secondaire.
- Vous ne pouvez pas [mise à l’échelle](cache-how-to-scale.md) aucun des caches, tandis que les caches sont liés.
- Vous ne pouvez pas [modifier le nombre de partitions](cache-how-to-premium-clustering.md) si le clustering est activé.
- Vous ne pouvez activer la persistance sur aucun des caches.
- Vous pouvez [exporter](cache-how-to-import-export-data.md#export) à partir d’un cache.
- Vous ne pouvez pas [importation](cache-how-to-import-export-data.md#import) dans le cache lié secondaire.
- Vous ne pouvez pas supprimer les caches liés ou le groupe de ressources qui les contient, jusqu'à ce que vous dissocier les caches. Pour plus d’informations, consultez [Pourquoi ma tentative de suppression de mon cache lié a-t-elle échoué ?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Si les caches se trouvent dans différentes régions, les frais de sortie de réseau s’appliquent aux données déplacées entre les régions. Pour plus d’informations, consultez [Combien coûte la réplication de mes données entre des régions Azure ?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Le basculement automatique ne se produit entre le cache lié principal et secondaire. Pour plus d’informations et des informations sur comment basculer une application cliente, consultez [comment fonctionne le basculement vers le cache lié secondaire ?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Ajouter un lien de géoréplication

1. Pour lier deux caches pour la géo-réplication, premier clic **géo-réplication** dans le menu ressource du cache que vous avez l’intention de primaire lié du cache. Ensuite, cliquez sur **ajouter le lien de réplication du cache** à partir de la **géo-réplication** panneau.

    ![Ajouter un lien](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Cliquez sur le nom de votre cache secondaire souhaité à partir de la **caches compatibles** liste. Si votre cache secondaire n’est pas affiché dans la liste, vérifiez que le [conditions préalables de géo-réplication](#geo-replication-prerequisites) pour le cache secondaire sont remplies. Pour filtrer les caches par région, cliquez sur la région dans la carte pour afficher uniquement les caches figurant dans le **caches compatibles** liste.

    ![Caches compatibles avec la géoréplication](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Vous pouvez également démarrer le processus de liaison ou afficher des détails sur le cache secondaire à l’aide du menu contextuel.

    ![Menu contextuel de la géoréplication](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Cliquez sur **Lier** pour lier les deux caches et commencer le processus de réplication.

    ![Lier des caches](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Vous pouvez voir la progression du processus de réplication sur le panneau **Géoréplication**.

    ![État du lien](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Vous pouvez également voir l’état du lien dans le panneau **Vue d’ensemble** des caches principal et secondaire.

    ![État du cache](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Une fois le processus de réplication terminé, l’**État du lien** devient **Réussi**.

    ![État du cache](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Le cache lié principal reste disponible pour une utilisation pendant le processus de liaison. Le cache lié secondaire n’est pas disponible jusqu'à ce que le processus de liaison est terminée.

## <a name="remove-a-geo-replication-link"></a>Supprimer un lien de géoréplication

1. Pour supprimer le lien entre deux caches et arrêter la géoréplication, dans le panneau **Géoréplication**, cliquez sur **Dissocier les caches**.
    
    ![Dissocier les caches](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Une fois le processus de dissociation terminé, le cache secondaire est disponible tant en lecture qu’en écriture.

>[!NOTE]
>En cas de suppression du lien de géoréplication, les données répliquées à partir du cache lié principal restent dans le cache secondaire.
>
>

## <a name="geo-replication-faq"></a>FAQ concernant la géoréplication

- [Puis-je utiliser la géoréplication avec un cache de niveau Standard ou De base ?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Mon cache est-il disponible pendant le processus de liaison ou de dissociation ?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Puis-je lier plus de deux caches ?](#can-i-link-more-than-two-caches-together)
- [Puis-je lier deux caches d’abonnements Azure différents ?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Puis-je lier deux caches de tailles différentes ?](#can-i-link-two-caches-with-different-sizes)
- [Puis-je utiliser la géoréplication quand le clustering est activé ?](#can-i-use-geo-replication-with-clustering-enabled)
- [Puis-je utiliser la géoréplication avec mes caches dans un réseau virtuel ?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Quelle est la planification de réplication pour la géoréplication Redis ?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Quelle est la durée de réplication pour la géoréplication ?](#how-long-does-geo-replication-replication-take)
- [Y a-t-il un point de récupération de la réplication garanti ?](#is-the-replication-recovery-point-guaranteed)
- [Puis-je utiliser PowerShell ou Azure CLI pour gérer la géoréplication ?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Combien coûte la réplication de mes données entre des régions Azure ?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Pourquoi ma tentative de suppression de mon cache lié a-t-elle échoué ?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Quelle région dois-je utiliser pour mon cache lié secondaire ?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Comment fonctionne le basculement vers le cache lié secondaire ?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Puis-je utiliser la géoréplication avec un cache de niveau Standard ou De base ?

Non, la géoréplication est disponible uniquement pour des caches de niveau Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Mon cache est-il disponible pendant le processus de liaison ou de dissociation ?

- Lors de la liaison, le cache lié principal reste disponible pendant le processus de liaison.
- Lors de la liaison, le cache lié secondaire n’est pas disponible jusqu'à ce que le processus de liaison est terminée.
- Lors de l’annulation de la liaison, les deux caches restent disponibles pendant le processus de dissociation terminé.

### <a name="can-i-link-more-than-two-caches-together"></a>Puis-je lier plus de deux caches ?

Non, vous pouvez uniquement lier deux caches.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Puis-je lier deux caches d’abonnements Azure différents ?

Non, les deux caches doivent figurer dans le même abonnement Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Puis-je lier deux caches de tailles différentes ?

Oui, pour autant que le cache lié secondaire soit plus grand que le cache lié principal.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Puis-je utiliser la géoréplication quand le clustering est activé ?

Oui, pour autant que les deux caches aient le même nombre de partitions.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Puis-je utiliser la géoréplication avec mes caches dans un réseau virtuel ?

Oui, géo-réplication des caches dans des réseaux virtuels est prise en charge avec les mises en garde :

- La géoréplication entre caches figurant dans un même réseau virtuel est prise en charge.
- Géoréplication entre caches figurant dans différents réseaux virtuels est également prise en charge.
  - Si les réseaux virtuels sont dans la même région, vous pouvez les connecter à l’aide de [homologation](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ou un [connexion de passerelle VPN pour réseau](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V).
  - Si les réseaux virtuels sont dans différentes régions, géo-réplication à l’aide de réseau virtuel homologation n’est pas pris en charge en raison d’une contrainte avec les équilibreurs de charge interne de base. Pour plus d’informations sur les contraintes de l’homologation de réseau virtuel, consultez [- homologation de réseaux virtuels - exigences et contraintes](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints). La solution recommandée consiste à utiliser une connexion de passerelle VPN au réseau.

À l’aide de [ce modèle Azure](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/), vous pouvez rapidement déployer deux caches géo-répliquée dans un réseau virtuel connecté avec une connexion de passerelle VPN au réseau.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Quelle est la planification de réplication pour la géoréplication Redis ?

La réplication est continue et asynchrone et ne se produit selon une planification spécifique. Toutes les écritures effectuées sur le serveur principal sont répliquées instantanément et de façon asynchrone sur le serveur secondaire.

### <a name="how-long-does-geo-replication-replication-take"></a>Quelle est la durée de réplication pour la géoréplication ?

La réplication est continue, asynchrone et incrémentielle et la durée n’est pas très différente de la latence entre les régions. Dans certaines circonstances, le cache secondaire peut-être être nécessaires pour effectuer une synchronisation complète des données depuis le serveur principal. Durée de la réplication dans ce cas dépend de plusieurs facteurs tels que : la charge sur le cache principal, la bande passante réseau disponible et la latence d’entre plusieurs région. Nous avons trouvé l’heure de la réplication pour une paire de géo-répliquée complète de 53 Go peut être n’importe où entre 5 et 10 minutes.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Y a-t-il un point de récupération de la réplication garanti ?

Pour les caches en mode géorépliqué, la persistance est désactivée. Si une paire de géo-répliqué est dissociée, telle qu’un basculement effectué par le client, le cache lié secondaire conserve ses données synchronisées jusqu'à ce point de temps. Dans ce cas, aucun point de récupération n’est garantie.

Pour obtenir un point de récupération, [exporter](cache-how-to-import-export-data.md#export) à partir d’un cache. Vous pouvez ultérieurement [importation](cache-how-to-import-export-data.md#import) dans le cache lié principal.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Puis-je utiliser PowerShell ou Azure CLI pour gérer la géoréplication ?

Oui, géo-réplication peut être gérée à l’aide du portail Azure, PowerShell ou Azure CLI. Pour plus d’informations, consultez le [PowerShell docs](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) ou [docs d’Azure CLI](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Combien coûte la réplication de mes données entre régions Azure ?

Lorsque vous utilisez la géoréplication, les données du cache lié principal sont répliquées vers le cache lié secondaire. Il n’existe aucun frais pour le transfert de données si les deux caches liés se trouvent dans la même région. Si les deux caches liés se trouvent dans différentes régions, les frais de transfert de données sont le coût de sortie de réseau de déplacement sur une région de données. Pour plus d'informations, consultez [Détails de la tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Pourquoi ma tentative de suppression de mon cache lié a-t-elle échoué ?

Impossible de supprimer la géo-répliqué les caches et leurs groupes de ressources lorsque lié jusqu'à ce que vous supprimez le lien de géo-réplication. Si vous tentez de supprimer le groupe de ressources contenant l’un des caches liés ou les deux, les autres ressources du groupe sont supprimées, mais le groupe de ressources reste dans l’état `deleting` et les caches liés dans le groupe de ressources restent dans l’état `running`. Pour supprimer complètement le groupe de ressources et des caches liés qu’il contient, dissocier les caches, comme décrit dans [supprimer un lien de géo-réplication](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Quelle région dois-je utiliser pour mon cache lié secondaire ?

En règle générale, il est recommandé pour votre cache existe dans la même région Azure que l’application qui y accède. Pour les applications avec les régions principales et de secours distinctes, il est recommandé de que vos caches principales et secondaires existent dans ces mêmes régions. Pour plus d’informations sur les régions liées, consultez [Meilleures pratiques : régions Azure liées](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Comment fonctionne le basculement vers le cache lié secondaire ?

Le basculement automatique entre les régions Azure n’est pas pris en charge pour les caches de géo-répliquée. Dans un scénario de récupération d’urgence, les clients doivent activer la pile de l’ensemble de l’application de manière coordonnée dans leur région de sauvegarde. Ce qui permet d’application individuelle décider de composants quand basculer vers leurs sauvegardes peut nuire aux performances. Un des principaux avantages de Redis est qu’il est un magasin de très faible latence. Si l’application du client principal est dans une région différente de celle de son cache, le temps d’aller-retour ajouté aurait un impact perceptible sur les performances. Pour cette raison, nous permet d’éviter le basculement s’effectue automatiquement en raison de problèmes temporaires de disponibilité.

Pour démarrer un basculement effectué par le client, tout d’abord dissocier les caches. Ensuite, changez votre client Redis pour utiliser le point de terminaison de connexion du cache secondaire (précédemment lié). Lorsque les deux caches sont dissociés, le cache secondaire redevient un cache en lecture-écriture régulière et accepte les demandes directement à partir de clients Redis.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [niveau Premium du Cache Azure pour Redis](cache-premium-tier-intro.md).
