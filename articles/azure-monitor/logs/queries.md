---
title: Utilisation des requêtes dans Azure Monitor Log Analytics
description: Vue d’ensemble des requêtes de journal dans Azure Monitor Log Analytics, notamment les différents types de requêtes et exemples de requêtes que vous pouvez utiliser.
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 05/20/2021
ms.openlocfilehash: 552e79a11da001d7834cb9640bb77c08f9491f99
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482743"
---
# <a name="using-queries-in-azure-monitor-log-analytics"></a>Utilisation des requêtes dans Azure Monitor Log Analytics
Quand vous ouvrez Log Analytics, vous avez accès à des requêtes de journal existantes. Vous pouvez soit utiliser ces requêtes sans les modifier, soit vous en servir comme point de départ pour créer vos propres requêtes. Les requêtes disponibles comprennent des exemples fournis par Azure Monitor et des requêtes enregistrées par votre organisation. Cet article décrit les requêtes disponibles et la façon dont vous pouvez les découvrir et les utiliser.


## <a name="queries-interface"></a>Interface de requêtes
Sélectionnez des requêtes à partir de l’interface de requête qui est disponible à partir de deux emplacements différents dans Log Analytics.

### <a name="queries-dialog"></a>Boîte de dialogue requêtes

Quand vous ouvrez Log Analytics, la boîte de dialogue *Requêtes* s’affiche automatiquement. Si vous ne souhaitez pas que cette boîte de dialogue s’affiche automatiquement, désactivez le commutateur **Toujours afficher Requêtes**.

![Écran requêtes](media/queries/query-start.png)


Chaque requête est représentée par une carte. Vous pouvez rapidement parcourir les requêtes pour trouver ce dont vous avez besoin. Vous pouvez exécuter la requête directement à partir de la boîte de dialogue ou choisir de la charger dans l’éditeur de requêtes pour la modifier.

Vous pouvez également y accéder en cliquant en haut à droite de l’écran sur **Requêtes**.

[![Bouton Requêtes](media/queries/queries-button.png)](media/queries/queries-button.png#lightbox)

### <a name="query-sidebar"></a>Encadré de requête

Les mêmes fonctionnalités de l’expérience de la boîte de dialogue sont accessibles à partir du volet des requêtes dans la barre latérale gauche de Log Analytics. Vous pouvez pointer sur un nom de requête pour obtenir la description de la requête et des fonctionnalités supplémentaires.

[![Encadré de requête](media/queries/query-sidebar.png)](media/queries/query-sidebar.png#lightbox)

## <a name="finding-and-filtering-queries"></a>Recherche et filtrage des requêtes

Les options de cette section sont disponibles à la fois dans la boîte de dialogue et dans la barre latérale, mais avec une interface utilisateur légèrement différente.  


### <a name="group-by"></a>Regrouper par

Modifiez le regroupement des requêtes en cliquant sur la liste déroulante *Regrouper par*. Les valeurs de regroupement agissent également comme une table des matières active. Cliquez sur l’une des valeurs à gauche de l’écran pour faire défiler l’affichage des requêtes jusqu’à l’élément sur lequel vous avez cliqué. Si votre organisation a créé des packs de requêtes avec des balises, les balises personnalisées sont incluses dans cette liste.

[![Écran d’exemples de requêtes GroupBy](media/queries/example-query-groupby.png)](media/queries/example-query-groupby.png#lightbox)



### <a name="filter"></a>Filtrer

Vous pouvez également filtrer les requêtes en fonction des valeurs **group by** mentionnées précédemment. Dans la boîte de dialogue des exemples de requêtes, les filtres se trouvent en haut.

[![Écran des filtres d’exemples de requêtes](media/queries/example-query-filter.png)](media/queries/example-query-filter.png#lightbox)

### <a name="combining-group-by-and-filter"></a>Combinaison du regroupement et du filtrage

Les fonctionnalités de filtrage et de regroupement sont conçues pour fonctionner en tandem. Elles offrent une certaine flexibilité quant à la façon dont les requêtes sont organisées. Par exemple, si vous utilisez un groupe de ressources composé de plusieurs ressources, vous pouvez appliquer un filtre pour un type de ressource spécifique et organiser les requêtes résultantes par rubrique.

## <a name="query-properties"></a>Propriétés de la requête
Chaque requête a plusieurs propriétés qui vous aident à les regrouper et à les trouver. Ces propriétés sont disponibles pour le tri et le filtrage, et vous pouvez en définir plusieurs lors de [l’enregistrement de votre propre requête](save-query.md). Les types de propriétés sont les suivants :

- **Type de ressource** : ressource telle que définie dans Azure, comme une machine virtuelle. Pour une cartographie complète des tables Azure Monitor Logs/Log Analytics par type de ressource, consultez la [référence des tables Azure Monitor](/azure/azure-monitor/reference/tables/tables-resourcetype).  
- **Catégorie** : type d’informations telles que *Sécurité* ou *Audit*. Les catégories sont identiques à celles définies dans le volet latéral Tables. Pour obtenir la liste complète des catégories, consultez la [référence des tables Azure Monitor](/azure/azure-monitor/reference/tables/tables-category).  
- **Solution** : solution Azure Monitor associée aux requêtes.
- **Rubrique** : rubrique de l’exemple de requête, telle que *Journaux d’activité* ou *Journaux d’application*. La propriété Rubrique est unique aux exemples de requêtes et peut différer selon le type de ressource spécifique.
- **Étiquettes** : étiquettes personnalisées que vous pouvez définir et attribuer quand vous [enregistrez votre propre requête](save-query.md).
- **Balises** : propriétés personnalisées qui peuvent être définies quand vous [créez un pack de requêtes](query-packs.md). Les balises permettent à votre organisation de créer ses propres taxonomies pour organiser les requêtes.

## <a name="favorites"></a>Favoris
Vous pouvez mettre en favoris les requêtes les plus fréquemment utilisées pour obtenir un accès plus rapide. Cliquez sur l’étoile à côté de la requête pour l’ajouter aux **Favoris**. Affichez vos requêtes favorites à partir de l’option **Favoris** dans l’interface de requête.

## <a name="types-of-queries"></a>Types de requête
L’interface de requête est remplie avec les types de requêtes suivants :

**Exemples de requêtes :** ils peuvent fournir des informations instantanées sur une ressource et offrent un excellent moyen de commencer à apprendre le KQL et à l’utiliser, réduisant ainsi le temps nécessaire pour commencer à utiliser Log Analytics. Nous avons collecté et organisé plus de 500 exemples de requêtes conçus pour vous fournir une valeur instantanée, et ce nombre d’exemples de requêtes croît continuellement.

**Packs de requêtes :** un [pack de requêtes](query-packs.md) contient une collection de requêtes de journal, notamment des requêtes que vous enregistrez vous-même. Cela comprend le [pack de requête par défaut](query-packs.md#default-query-pack) et tous les autres packs de requêtes que votre organisation a pu créer dans l’abonnement.

**Requêtes héritées :** requêtes précédemment enregistrées dans l’explorateur de requêtes et requêtes que les solutions Azure ont installées dans l’espace de travail. Celles-ci sont répertoriées dans la boîte de dialogue des requêtes sous **Requêtes héritées**.

## <a name="effect-of-query-scope"></a>Effet de l’étendue de la requête
Les requêtes qui sont disponibles quand vous ouvrez Log Analytics sont déterminées par la [requête actuelle](scope.md).

- Pour un **espace de travail** : tous les exemples de requêtes et les requêtes issues de packs de requêtes. Requêtes héritées dans l’espace de travail.
- Pour une **ressource unique** : exemples de requêtes et requêtes des packs de requêtes pour le type de ressource. 
- Pour un **groupe de ressources** : exemples de requêtes et requêtes issues de packs de requêtes pour les types de ressources dans le groupe de ressources. 

> [!TIP]
> Plus vous avez de ressources dans votre étendue, plus le portail a besoin de temps pour filtrer et afficher la boîte de dialogue des exemples de requêtes.


## <a name="next-steps"></a>Étapes suivantes

[Prise en main des requêtes KQL](get-started-queries.md)

