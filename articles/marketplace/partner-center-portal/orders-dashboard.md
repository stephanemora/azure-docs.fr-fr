---
title: Tableau de bord des commandes de l’Espace partenaires dans l’analytique de la Place de marché commerciale, Microsoft AppSource et la Place de marché Azure
description: Découvrez comment accéder aux rapports d’analytique concernant vos commandes d’offres de la Place de marché commerciale, dans un format graphique et téléchargeable.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: d5adc1bfe19de48568d0e77bb488bea0e5a02818
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327376"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Tableau de bord Commandes dans l’analytique de la place de marché commerciale

Cet article fournit des informations sur le **tableau de bord Commandes** de l’Espace partenaires. Ce tableau de bord présente des informations sur vos commandes dans un format graphique et téléchargeable.

Pour accéder au **tableau de bord Commandes** de l’Espace partenaires, ouvrez le tableau de bord **[Analyser](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** sous Place de marché commerciale.

>[!NOTE]
> Pour accéder aux définitions détaillées de la terminologie d’analytique, voir [Questions fréquentes et terminologie pour l’analytique de la place de marché commerciale](./faq-terminology.md).

## <a name="orders-dashboard"></a>Tableau de bord des commandes

Le **tableau de bord Commandes** du menu **Analyser** présente les commandes en cours pour l’ensemble de vos offres SaaS. Vous pouvez afficher une représentation graphique des éléments suivants :

- [Résumé des commandes](#order-summary)
- [Commandes par zone géographique](#orders-by-geography)
- [Commandes par offre](#orders-by-offers)
- [Tendance des commandes par site et par siège](#orders-trend-per-site-versus-per-seat)
- [Commandes par plans](#orders-by-plans)
- [Tendance des commandes et des sièges](#orders-and-seats-trend)
- [Tableau des détails des commandes](#order-details-table)

Dans l’Espace partenaires, la latence maximale entre la création des commandes et la création des rapports est de 48 heures.

## <a name="order-dashboard-details"></a>Détails du tableau de bord Commande

Cette section décrit plus en détail les rapports d’analytique.

### <a name="order-summary"></a>Résumé des commandes

La section Résumé des commandes présente le nombre total de commandes passées (hors commandes annulées), de commandes annulées et de sièges.

Le pourcentage situé à côté de Nombre total de commandes représente la progression par rapport à la plage de dates précédente.

![Menu Analyser de l’Espace partenaires – Résumé des commandes](./media/order-summary.png)

- Un triangle vert pointant vers le haut indique une tendance positive.
- Un triangle rouge pointant vers le bas indique une tendance négative par rapport au mois précédent.
- Les tendances de croissance sont représentées par des micrographiques à barres. Vous pouvez afficher la valeur de chaque mois en pointant sur les colonnes du graphe.
- Les commandes annulées correspondent au nombre de commandes passées auparavant, puis annulées pendant la plage de dates sélectionnée.
- Les sièges font référence au nombre de sièges créés au cours de la plage de dates sélectionnée.

### <a name="orders-by-geography"></a>Commandes par zone géographique

La carte thermique **Commandes par zone géographique** présente le nombre de commandes sur une carte du monde et indique les sièges en fonction du pays ou de la région du client. Elle fonctionne de la même façon que la **[carte thermique Client par zone géographique](./customer-dashboard.md#customer-by-geography)** .

![Menu Analyser de l’Espace partenaires – Commandes par zone géographique](./media/orders-by-geography.png)

### <a name="orders-by-offers"></a>Commandes par offre

Le graphique en anneau **Commandes par offre** organise les commandes (commandes annulées incluses) en fonction du nom de l’offre.

- Les premières offres s’affichent dans le graphe et les autres sont regroupées sous « Tout le reste ».
- Vous pouvez sélectionner des offres spécifiques dans la légende pour afficher uniquement ces offres dans le graphique.
- Placez le curseur sur une tranche du graphique pour afficher le nombre de commandes et le pourcentage de cette offre par rapport au nombre total de vos commandes pour toutes les offres.
- La **Tendance des commandes par offre** présente les tendances de croissance mois par mois. La colonne du mois représente le nombre de commandes par nom d'offre. Le graphique en courbes affiche la tendance du pourcentage de croissance tracée sur un axe z.
- Vous pouvez utiliser le curseur en haut du graphique pour effectuer un défilement vers la droite et la gauche le long de l’axe x et vous concentrer sur des points de données spécifiques.
- Vous pouvez afficher le graphique des tendances en sélectionnant un élément spécifique de la légende.
- Vous pouvez également choisir d’afficher les tendances et les données relatives aux **commandes annulées**. Le graphe fonctionne de la même manière que le graphe **Commandes par offre**.

### <a name="orders-trend-per-site-versus-per-seat"></a>Tendance des commandes par site et par siège

Le graphique en anneau **par site et par siège** représente la répartition des commandes SaaS par site et des commandes SaaS par siège passées par les clients (commandes annulées incluses). L’histogramme représente la tendance des commandes SaaS par site et des commandes SaaS par siège passées par les clients (commandes annulées incluses).

### <a name="orders-by-plans"></a>Commandes par plans

Le graphe **Commandes par plans** représente la tendance des commandes au niveau des plans pour toutes vos offres (commandes annulées incluses). Le graphique en anneau représente la répartition des cinq premières commandes par plans et l’histogramme représente la tendance des commandes pour les cinq premiers plans.

### <a name="orders-and-seats-trend"></a>Tendance des commandes et des sièges

Le graphe **Tendance des commandes et des sièges** présente les 50 premières offres par nombre de commandes. Elles sont affichées dans un classement et triées par nombre de commandes et pourcentage de commandes.

- **Commandes par plans** – Sélectionnez une offre pour voir la répartition du nombre de commandes pour les cinq premiers plans dans le graphe.
- **Sièges par plans** – Tendance mensuelle des sièges pour les cinq premiers plans. Si l’offre que vous sélectionnez n’est pas une offre par siège, aucune donnée ne s’affichera.

### <a name="canceled-orders-by-offers"></a>Commandes annulées par offre

Le graphique à secteurs **Commandes annulées par offre** organise l’ensemble des commandes annulées en fonction de leur nom d’offre. Les premières offres s’affichent dans le graphe et les autres sont regroupées sous « Tout le reste ». Dans la légende, vous pouvez sélectionner des offres spécifiques à afficher dans le graphe.

- Placez le curseur sur un secteur du graphique pour afficher le nombre de commandes et le pourcentage de l’offre sélectionnée par rapport au nombre total de commandes, toutes offres confondues.
- L’histogramme indique les tendances mois par mois. Les colonnes représentent le nombre de commandes annulées par nom d’offre. Vous pouvez utiliser le curseur en haut du graphe pour effectuer un défilement vers la droite et la gauche le long de l’axe X et vous concentrer sur des points de données spécifiques. Vous pouvez afficher le graphique des tendances en sélectionnant un élément spécifique de la légende.

### <a name="order-details-table"></a>Tableau des détails des commandes

Le tableau Détails des commandes présente la liste numérotée des 1 000 premières commandes triées par date d’acquisition.

- Chaque colonne de la grille peut être triée.
- Les données peuvent être extraites dans un fichier TSV en présence d'un nombre d’enregistrements inférieur à 1 000.
- En présence d'un nombre d'enregistrements supérieur à 1 000, les données exportées sont placées, de manière asynchrone, dans une page de téléchargements pour une durée de 30 jours.
- Appliquez des filtres au **tableau Détails des commandes** pour afficher uniquement les données qui vous intéressent. Filtrez les données par pays/région, type de licence Azure, type de licence de Place de marché commerciale, type d’offre, état de la commande, évaluations gratuites, ID d’abonnement de Place de marché commerciale, ID de client et nom de société.
- Étant donné que les offres SaaS achetées par le biais de la Place de marché Azure ou d’AppSource ne nécessitent pas d’abonnement Azure, l’ID d’abonnement de Place de marché affiche 00000000-0000-0000-0000-000000000000 dans la section **Données de commandes détaillées**.

#### <a name="orders-page-filters"></a>Filtres de page des commandes

Ces filtres s’appliquent au niveau de la page.

Vous pouvez sélectionner plusieurs filtres afin d’afficher le graphe pour les critères et les données souhaités dans la grille/l’exportation **Données de commandes détaillées**. Les filtres sont appliqués aux données extraites pour la plage de données sélectionnée dans l’angle supérieur droit de la page Commandes.

- Le type et le nom des offres ne figurent que pour les offres comptant des commandes pendant la plage de dates sélectionnée. Les noms d’offres de la liste s’affichent pour les types d’offres sélectionnés dans la liste.
- Les filtres appliqués indiquent les métriques totales de toute la sélection pour chaque filtre sélectionné. Il ne s’affichent pas lorsque la sélection par défaut est utilisée.
- Si **Tout** est sélectionné pour l’une des listes déroulantes, toutes les métriques de la page sélectionnée sont agrégées. Par exemple : L’option de filtre des types d’offres « Tout » signifie que tous les types d’offre sont sélectionnés. Il s’agit de la sélection par défaut pour les listes déroulantes. Les filtres appliqués n’indiquent rien quand **Tout** est sélectionné.
- **Sélection de valeurs multiples** : toutes les métriques de la page seront agrégées pour toutes les sélections effectuées dans la liste déroulante. En cas de sélections multiples, le filtre appliqué indique le nombre de sélections effectuées, comme dans l’image ci-dessous :

    ![Menu Analyser de l’Espace partenaires – Commande avec plusieurs valeurs appliquées au filtre](./media/filters-applied.png)

- **Sélection d’une seule valeur** : si une seule valeur est sélectionnée, le filtre appliqué indique le nombre associé au filtre sélectionné, comme dans l’image ci-dessous :

     ![Menu Analyser de l’Espace partenaires – Commande avec une seule valeur appliquée au filtre](./media/filters-applied-single.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble des rapports d’analytique disponibles sur la place de marché commerciale de l’Espace partenaires, voir [Analytics pour la place de marché commerciale dans l’Espace partenaires](./analytics.md).
- Pour consulter les graphes, tendances et valeurs des données agrégées qui résument l’activité de la place de marché pour vos offres, voir [Tableau de bord Résumé dans l’analytique de la place de marché commerciale](./summary-dashboard.md).
- Pour consulter les métriques d’utilisation et de facturation à l’usage des offres de machines virtuelles, voir [Tableau de bord de l’utilisation dans l’analytique de la place de marché commerciale](./usage-dashboard.md).
- Pour des informations détaillées sur vos clients, y compris des tendances de croissance, voir [Tableau de bord client dans l’analytique de la place de marché commerciale](./customer-dashboard.md).
- Pour obtenir la liste de vos demandes de téléchargement des 30 derniers jours, voir [Tableau de bord des téléchargements dans l’analytique de la place de marché commerciale](./downloads-dashboard.md).
- Pour une vue consolidée des commentaires des clients pour les offres sur la Place de marché Azure et AppSource, voir [Tableau de bord Évaluations et avis dans l’analytique de la place de marché commerciale](./ratings-reviews.md).
- Pour voir les questions fréquentes sur l’analytique de la place de marché commerciale et un dictionnaire complet des termes liés aux données, consultez [Questions fréquentes et terminologie pour l’analytique de la place de marché commerciale](./faq-terminology.md).
