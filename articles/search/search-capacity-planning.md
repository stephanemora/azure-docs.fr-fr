---
title: Partitions de la mise à l’échelle et de réplicas pour interroger et indexer - recherche de Azure
description: Ajustez les ressources informatiques des partitions et des réplicas dans Recherche Azure, où chaque ressource est facturée en unités de recherche facturables.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6879dd975f97ba2746165e87a135e5d90e8b229f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620631"
---
# <a name="scale-partitions-and-replicas-for-query-and-indexing-workloads-in-azure-search"></a>Mettre à l’échelle des partitions et réplicas pour interroger et indexer les charges de travail dans Azure Search
Une fois que vous avez [choisi un niveau tarifaire](search-sku-tier.md) et [approvisionné un service de recherche](search-create-service-portal.md), l’étape suivante, facultative, consiste à augmenter le nombre de réplicas ou de partitions utilisés par votre service. Chaque niveau propose un nombre fixe d’unités de facturation. Cet article explique comment allouer ces unités pour obtenir une configuration optimale par rapport à vos exigences pour l’exécution des requêtes, l’indexation et le stockage.

Configuration de la ressource est disponible lorsque vous configurez un service à la [niveau de base](https://aka.ms/azuresearchbasic) ou l’un de le [niveaux Standard ou stockage optimisé](search-limits-quotas-capacity.md). Pour les services à ces niveaux, la capacité est achetée par incréments *d’unités de recherche* (SU) où chaque partition et chaque réplica est considéré comme une SU. 

La facture est proportionnelle au nombre de SU : moins elles sont nombreuses, plus la facture diminue. La facturation reste en vigueur tant que le service est configuré. Si vous n’utilisez pas temporairement un service, la seule manière d’éviter la facturation consiste à supprimer ce service, puis à le recréer lorsque vous en avez besoin.

> [!Note]
> La suppression d’un service a pour effet de supprimer toutes les données qui s’y trouvent. Il n’existe aucune fonctionnalité dans Azure Search permettant de sauvegarder et restaurer les données de recherche persistantes. Pour redéployer un index existant sur un nouveau service, vous devez exécuter le programme initialement utilisé pour le créer et le charger. 

## <a name="terminology-replicas-and-partitions"></a>Terminologie : réplicas et partitions
Réplicas et les partitions sont les principales ressources qui soutiennent un service de recherche.

| Ressource | Définition |
|----------|------------|
|*Partitions* | Fournissent un stockage des index et des E/S pour les opérations de lecture-écriture (par exemple, lors de la reconstruction ou de l’actualisation d’un index).|
|*Réplicas* | Instances du service de recherche, principalement utilisées pour équilibrer la charge des opérations de requête. Chaque réplica héberge toujours une seule copie d’un index. Si vous avez 12 réplicas, vous aurez 12 copies de chaque index chargées sur le service.|

> [!NOTE]
> Il n’existe aucun moyen de manipuler ou de gérer directement les index qui s’exécutent sur un réplica. Une copie de chaque index sur chaque réplica fait partie de l’architecture de service.
>


## <a name="how-to-allocate-replicas-and-partitions"></a>Comment allouer des réplicas et des partitions
Dans Azure Search, un service se voit initialement allouer un niveau minimal de ressources consistant en une partition et un réplica. Pour les niveaux qui le prennent en charge, vous pouvez ajuster progressivement les ressources de calcul en augmentant les partitions si vous avez besoin de plus de stockage et d’E/S ou de réplicas pour des volumes de requêtes plus importants ou des performances améliorées. Un seul service doit avoir suffisamment de ressources pour gérer toutes les charges de travail (indexation et requêtes). Vous ne pouvez pas subdiviser les charges de travail entre plusieurs services.

Pour augmenter ou modifier l’allocation des réplicas et des partitions, nous vous recommandons l’aide du portail Azure. Le portail applique des limites sur les combinaisons autorisées inférieures aux limites maximales. Si vous avez besoin d’une approche d’approvisionnement basée sur le code ou script, le [Azure PowerShell](search-manage-powershell.md) ou [API REST de gestion](https://docs.microsoft.com/rest/api/searchmanagement/services) sont des solutions alternatives.

En règle générale, les applications de recherche ont besoin de plus de réplicas que de partitions, en particulier lorsque les opérations de service favorisent les charges de travail de requête. La section [Haute disponibilité](#HA) explique pourquoi.

1. Connectez-vous au [portail Azure](https://portal.azure.com/), puis sélectionnez le service de recherche.
2. Dans **paramètres**, ouvrez le **mise à l’échelle** page pour modifier les réplicas et des partitions. 

   La capture d’écran suivante montre un service standard configuré avec un seul réplica et partition. La formule en bas indique combien d’unités de recherche sont utilisés (1). Si le prix unitaire était de 100 $ (pas un prix réel), le coût mensuel de l’exécution de ce service serait 100 $ en moyenne.

   ![Affichage des valeurs actuelles dans la page mise à l’échelle](media/search-capacity-planning/1-initial-values.png "affichage des valeurs actuelles dans la page mise à l’échelle")

3. Utilisez le curseur pour augmenter ou diminuer le nombre de partitions. La formule en bas indique combien d’unités de recherche sont utilisées.

   Cet exemple double la capacité, avec deux réplicas et chacune des partitions. Notez que le nombre d’unités de recherche ; Il est désormais quatre, car la formule de facturation correspond aux réplicas multipliés par les partitions (2 x 2). Doubler la capacité double plus le coût d’exécution du service. Si le coût unitaire de recherche était de 100 $, la nouvelle facture mensuelle devrait désormais être 400 dollars.

   Actuel par coût unitaire de chaque couche, visitez le [page de tarification](https://azure.microsoft.com/pricing/details/search/).

   ![Ajouter des réplicas et des partitions](media/search-capacity-planning/2-add-2-each.png "ajouter des réplicas et des partitions")

3. Cliquez sur **enregistrer** pour confirmer les modifications.

   ![Confirmer les modifications apportées à la mise à l’échelle et la facturation](media/search-capacity-planning/3-save-confirm.png "confirmer les modifications apportées à la mise à l’échelle et la facturation")

   Modifications apportées à la capacité de prendront plusieurs heures. Vous ne pouvez pas annuler une fois que le processus a démarré et il est sans surveillance en temps réel pour les ajustements de réplica et partition. Toutefois, le message suivant reste visible lors de l’exécution de modifications.

   ![Message d’état dans le portail](media/search-capacity-planning/4-updating.png "message d’état dans le portail")


> [!NOTE]
> Une fois qu’un service est configuré, il ne peut pas être mis à niveau vers une référence (SKU) supérieure. Vous devez créer un service de recherche au nouveau niveau et recharger vos index. Pour obtenir des instructions sur l’approvisionnement du service, voir [Créer un service Azure Search dans le portail](search-create-service-portal.md) .
>
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>combinaisons de partitions et de réplicas

Un service basique peut avoir exactement une partition et jusqu’à trois réplicas, pour une limite maximale de trois unités de recherche. Les seules ressources ajustables sont les réplicas. Vous devez disposer d’au moins 2 réplicas pour la haute disponibilité sur des requêtes.

Les services de recherche tous les Standard et stockage optimisé peuvent supposer que les combinaisons suivantes de réplicas et de partitions soumises à la limite de 36-SU. 

|   | **1 partition** | **2 partitions** | **3 partitions** | **4 partitions** | **6 partitions** | **12 partitions** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 réplica** |1 unité de recherche |2 unités de recherche |3 unités de recherche |4 unités de recherche |6 unités de recherche |12 unités de recherche |
| **2 réplicas** |2 unités de recherche |4 unités de recherche |6 unités de recherche |8 unités de recherche |12 unités de recherche |24 unités de recherche |
| **3 réplicas** |3 unités de recherche |6 unités de recherche |9 unités de recherche |12 unités de recherche |18 unités de recherche |36 unités de recherche |
| **4 réplicas** |4 unités de recherche |8 unités de recherche |12 unités de recherche |16 unités de recherche |24 unités de recherche |S.O. |
| **5 réplicas** |5 unités de recherche |10 unités de recherche |15 unités de recherche |20 unités de recherche |30 unités de recherche |S.O. |
| **6 réplicas** |6 unités de recherche |12 unités de recherche |18 unités de recherche |24 unités de recherche |36 unités de recherche |S.O. |
| **12 réplicas** |12 unités de recherche |24 unités de recherche |36 unités de recherche |S.O. |N/A |S.O. |

Les unités de recherche, leur tarification et leur capacité sont détaillées sur le site web Azure. Pour plus d'informations, consultez la rubrique [Tarification](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Le nombre de réplicas et de partitions est divisible par 12 de manière égale (plus précisément, 1, 2, 3, 4, 6, 12). Azure Search divise au préalable chaque index en 12 partitions pour que celles-ci puissent être réparties équitablement sur plusieurs partitions. Par exemple, si votre service comporte trois partitions et que vous créez un index, chaque partition contiendra quatre partitions de l'index. Le partitionnement d’un index réalisé par la Recherche Azure est un détail d'implémentation susceptible d’être modifié dans des futures versions. Le nombre de partitions (12 à l’heure actuelle) peut être, à l’avenir, totalement différent.
>


<a id="HA"></a>

## <a name="high-availability"></a>Haute disponibilité
Nous vous recommandons généralement de démarrer avec une partition et un ou deux réplicas, puis de monter en puissance à mesure que les volumes de requête se créent. Les charges de travail de requêtes s’exécutent principalement sur des réplicas. Si vous nécessitez une haute disponibilité ou un débit plus important, vous aurez probablement besoin de réplicas supplémentaires.

Recommandations générales pour la haute disponibilité :

* Deux réplicas pour la haute disponibilité des charges de travail en lecture seule (requêtes)
* Trois réplicas minimum pour la haute disponibilité des charges de travail en lecture/écriture (les requêtes et l’indexation en tant que documents individuels sont ajoutées, mises à jour ou supprimées)

Les contrats de niveau de service (SLA) pour la Recherche Azure sont ciblés au moment des opérations de requête et des mises à jour d’index qui se composent d’ajout, de mise à jour ou de suppression de documents.

Le niveau De base est plafonné à une partition et trois réplicas. Si vous souhaitez pouvoir répondre immédiatement aux fluctuations de la demande sur le plan de l’indexation et du débit des requêtes, songez à passer à l’un des niveaux Standard.  Si vous trouvez que vos besoins de stockage augmente beaucoup plus rapidement que le débit de votre requête, envisagez l’une des niveaux de stockage optimisé.

### <a name="index-availability-during-a-rebuild"></a>Disponibilité des index lors d’une reconstruction

La haute disponibilité pour Azure Search se rapporte aux requêtes et aux mises à jour d’index qui n’impliquent pas la reconstruction d’un index. Si vous supprimez un champ, modifiez un type de données ou renommez un champ, vous devez reconstruire l’index. Pour reconstruire l’index, vous devez supprimer l’index, le recréer et recharger les données.

> [!NOTE]
> Vous pouvez ajouter de nouveaux champs à un index Recherche Azure sans reconstruire l’index. La valeur du nouveau champ sera Null pour tous les documents déjà présents dans l’index.

Pour maintenir la disponibilité de l’index pendant une reconstruction, vous devez disposer d’une copie de l’index avec un nom différent sur le même service ou d’une copie de l’index de même nom sur un autre service, et fournir la redirection ou la logique de basculement dans votre code.

## <a name="disaster-recovery"></a>Récupération d'urgence
Il n'existe actuellement aucun mécanisme intégré de récupération d'urgence. L’ajout de partitions ou de réplicas ne vous permettra pas d’atteindre les objectifs de récupération d'urgence qui ont été fixés. L’approche la plus courante consiste à intégrer la redondance au niveau du service en configurant un deuxième service de recherche dans une autre région. Comme avec la disponibilité pendant une reconstruction d’index, la redirection ou la logique de basculement doit provenir de votre code.

## <a name="increase-query-performance-with-replicas"></a>Augmenter les performances des requêtes avec des réplicas
La latence des requêtes vous permet de découvrir si des réplicas supplémentaires doivent être ajoutés. En règle générale, la première étape vers l’amélioration des performances des requêtes consiste à ajouter davantage de cette ressource. Lorsque vous ajoutez des réplicas, les copies supplémentaires de l’index sont mises en ligne pour prendre en charge les charges de travail supérieures de requête et équilibrer la charge des requêtes sur plusieurs réplicas.

Nous ne pouvons fournir aucune estimation sur les requêtes par seconde (RPS) : les performances des requêtes dépendent de la complexité de la requête et des charges de travail concurrentes. Bien que l’ajout de réplicas entraîne clairement une amélioration de l’évolutivité et des performances, le résultat final n’est pas strictement linéaire : si vous ajoutez trois réplicas, le débit n’est pas forcément multiplié par trois.

Pour obtenir de l’aide sur l’estimation des requêtes par seconde pour vos charges de travail, consultez [Considérations sur les performances et l’optimisation de Recherche Azure](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Améliorer les performances d’indexation avec des partitions
Les applications de recherche nécessitant une actualisation des données en temps réel ou presque ont proportionnellement besoin de plus de partitions que de réplicas. L’ajout de partitions répartit les opérations de lecture/écriture sur un plus grand nombre de ressources de calcul. Il vous offre également davantage d’espace disque pour stocker des documents et des index supplémentaires.

Plus les index sont grands, plus ils sont longs à interroger. Par conséquent, peut-être constaterez-vous que chaque augmentation incrémentielle des partitions nécessite une augmentation plus faible mais proportionnelle des réplicas. La complexité et le volume de vos requêtes auront une incidence sur la vitesse d’exécution des requêtes.


## <a name="next-steps"></a>Étapes suivantes

[Choisir un niveau de tarification pour Azure Search](search-sku-tier.md)
