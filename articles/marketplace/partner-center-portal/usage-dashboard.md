---
title: Tableau de bord Utilisation de l’analytique de la Place de marché commerciale
description: Découvrez comment consulter toutes les métriques d’utilisation des offres de machines virtuelles et de leur facturation à l’usage. Accédez au tableau de bord Utilisation de l’Espace partenaires sous la Place de marché commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 8747ff98cd2b53773d9e0a95436d90fef74fda55
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120668"
---
# <a name="usage-dashboard-in-microsoft-commercial-marketplace-analytics"></a>Tableau de bord Utilisation de l’analytique de la Place de marché commerciale Microsoft

Cet article fournit des informations sur le tableau de bord Utilisation de l’Espace partenaires. Ce tableau de bord affiche toutes les métriques d’utilisation et de facturation à l’usage des machines virtuelles dans deux onglets distincts : Utilisation de machine virtuelle et Utilisation de la facturation mesurée.

Pour accéder au tableau de bord Utilisation, ouvrez le tableau de bord **[Analyser](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** sous la **Place de marché commerciale**.

>[!NOTE]
> Pour avoir des définitions détaillées des termes d’analytique, consultez [Questions fréquentes et terminologie relatives à l’analytique de la Place de marché commerciale](./faq-terminology.md).

## <a name="usage-dashboard"></a>Tableau de bord de l’utilisation

Le tableau de bord Utilisation affiche les métriques relatives à l’utilisation de toutes les offres de machines virtuelles et à l’utilisation de la facturation basée sur des mesures. Ces métriques se trouvent sous deux onglets distincts : Utilisation de machine virtuelle et Utilisation de la facturation mesurée.

L’onglet Utilisation de machine virtuelle comporte des représentations graphiques des éléments suivants :

- [Résumé de l’utilisation](#usage-summary)
- [Utilisation par zone géographique](#usage-by-geography)
- [Utilisation par offres](#usage-by-offers)
- [Tendance d’utilisation par offres et références SKU](#usage-trend-by-offers-and-skus)
- [Utilisation par type d’offre](#usage-by-offer-type)
- [Utilisation par taille de machine virtuelle](#usage-by-vm-size)
- [Utilisation par canal de vente](#usage-by-sales-channel)
- [Données d’utilisation détaillées](#detailed-usage-data)

> [!NOTE]
> Les rapports d’analytique se présentent différemment dans le Portail Cloud Partner (CPP) et l’Espace partenaires. Dans le Portail Cloud Partner, la page **Insights Vendeurs** comporte un onglet « Commandes et utilisation » qui affiche les données de toutes les offres (celles facturées à l’usage et les autres). Dans l’Espace partenaires, les métriques d’utilisation sont affichées dans une page distincte.

### <a name="usage-summary"></a>Récapitulatif de l’utilisation

Le tableau Résumé de l’utilisation affiche les heures d’utilisation par les clients de toutes les offres qu’ils ont achetées.

- Les heures d’utilisation normalisée correspondent aux heures d’utilisation normalisées pour prendre en compte le nombre de cœurs de machines virtuelles ([nombre de cœurs de machines virtuelles] x [heures d’utilisation brute]). Les machines virtuelles désignées sous le nom de « SHAREDCORE » utilisent 1/6 (ou 0,1666) comme multiplicateur [nombre de cœurs de machines virtuelles].
- Les heures d’utilisation brute correspondent au nombre d’heures pendant lesquelles les machines virtuelles ont été utilisées.
- Le pourcentage représente le taux d’utilisation en plus ou en moins pour la plage de dates sélectionnée ([utilisation du dernier mois – utilisation du premier mois])/utilisation du premier mois).
- Les triangles verts pointant vers le haut indiquent une hausse de l’utilisation.
- Les triangles rouges pointant vers le bas indiquent une baisse de l’utilisation par rapport au mois précédent.
- Les micrographiques à barres affichent les valeurs mensuelles. Vous pouvez afficher la valeur de chaque mois en pointant sur les colonnes du graphique.

### <a name="usage-by-geography"></a>Utilisation par zone géographique

La carte thermique de l’**utilisation normalisée par zone géographique** affiche les heures d’utilisation mappées selon le pays/la région du client. La variation de la couleur du pays/de la région représente une concentration d’utilisation normalisée. Pour revenir à la vue initiale, appuyez sur le bouton d’**accueil** de la carte.

### <a name="usage-by-offers"></a>Utilisation par offres

- Le graphique en secteurs de l’**utilisation normalisée par offres** affiche une répartition des heures d’utilisation normalisée des offres en fonction de la plage de dates sélectionnée. Les cinq premières offres sont affichées dans le graphique. Les autres offres sont regroupées dans la catégorie **Tout le reste**.
- Le graphique à barres représente la tendance de croissance mois par mois pour la plage de dates sélectionnée. Les colonnes de mois représentent les heures d’utilisation des offres les plus utilisées (en nombre d’heures) durant le mois respectif. Le graphique en courbes illustre la tendance du pourcentage de croissance tracée sur l’axe Y secondaire.
- Utilisez le curseur en haut du graphique pour faire défiler de droite à gauche le long de l’axe X et/ou vous concentrer sur des points de données spécifiques.

### <a name="usage-trend-by-offers-and-skus"></a>Tendance d’utilisation par offres et références SKU

Ce graphique montre la tendance de l’utilisation normalisée des références SKU sélectionnées d’une offre. Le leaderboard des offres affiche les 50 offres enregistrant la plus forte utilisation, classées par heures d’utilisation. Le leaderboard des références SKU liste les 50 références SKU les plus utilisées pour l’offre sélectionnée.

### <a name="usage-by-offer-type"></a>Utilisation par type d’offre

- Le graphique en secteurs de l’**utilisation par type d’offre** organise les données d’utilisation en fonction du type d’offre.
- Les premières offres sont affichées dans le graphique. Les autres offres sont regroupées dans la catégorie « Tout le reste ».
- Le graphique de **tendances** affiche les tendances de croissance mois par mois. La colonne de mois représente l’utilisation en fonction des principaux types d’offres du mois respectif.

### <a name="usage-by-vm-size"></a>Utilisation par taille de machine virtuelle

Ce graphique représente la tendance d’utilisation des tailles de machines virtuelles sélectionnées (cinq au maximum) pour toutes vos offres/références SKU. L’histogramme est empilé avec les heures d’utilisation des tailles de machines virtuelles sélectionnées.

Le leaderboard liste les 50 premières tailles de machines virtuelles enregistrant la plus forte utilisation, classées en fonction du nombre d’heures d’utilisation.

### <a name="usage-by-sales-channel"></a>Utilisation par canal de vente

- Le graphique en secteurs de l’utilisation par canal de vente organise les données d’utilisation en fonction du canal de vente.
- Les canaux de vente enregistrant la plus forte utilisation sont affichés dans le graphique. Les autres canaux de vente sont regroupés dans la catégorie « Tout le reste ».
- La colonne de mois représente l’utilisation en fonction des principaux canaux de vente du mois respectif.
- Ce graphique a les mêmes caractéristiques que le graphique « Utilisation par offres »

### <a name="detailed-usage-data"></a>Données d’utilisation détaillées

Le **tableau des détails de l’utilisation** affiche une liste numérotée des 1 000 premiers enregistrements d’utilisation triés par utilisation.

- Chaque colonne de la grille peut être triée.
- Les données peuvent être extraites dans un fichier CSV si le nombre d’enregistrements est inférieur à 1 000.
- Si le nombre d’enregistrements dépasse 1 000, les données sont exportées de manière asynchrone dans une page de téléchargements qui restera disponible pendant une période de 30 jours.
- Vous pouvez appliquer des filtres aux **données d’utilisation détaillées** pour afficher uniquement les données qui vous intéressent. Les données peuvent être filtrées par pays/région, canal de vente, type de licence Place de marché, type d’utilisation, nom d’offre, type d’offre, essais gratuits, ID d’abonnement Place de marché, ID client et nom de société.

> [!NOTE]
> Sélectionnez le **type d’utilisation** dans le filtre de page pour afficher les graphiques en « vue normalisée » ou en « vue brute ». La « vue normalisée » est la vue utilisée par défaut pour ces graphiques.

Les **filtres de la page Utilisation** sont appliqués au niveau de la page. Vous pouvez sélectionner plusieurs filtres afin d’afficher le graphique selon les critères souhaités et pour les données qui vous intéressent dans la grille ou l’exportation « Données d’utilisation détaillées ». Les filtres sont appliqués aux données extraites pour la plage de données sélectionnée dans l’angle supérieur droit de la page Commandes.

- Les **types d’offres** et les **noms des offres** sont indiqués seulement pour les offres que vous avez achetées au cours de la période sélectionnée. Les noms d’offres de la liste s’affichent pour les types d’offres qui sont sélectionnés dans la liste.
- La sélection par défaut est « Tout » pour chacune des options de filtre, à l’exception de **Type d’utilisation**. Pour **Type d’utilisation**, l’utilisation normalisée est sélectionnée par défaut. Pour afficher l’utilisation brute dans les graphiques, sélectionnez « utilisation brute ».
- Les filtres appliqués indiquent le nombre de sélections de filtre qui ont été effectuées. Ils ne s’affichent pas pour les sélections par défaut.

> [!NOTE]
> Chacun des champs de la grille « données de commande détaillées », des filtres de page et des sélections possibles est défini en détail dans la section du dictionnaire de données fourni dans l’article [Questions fréquentes et terminologie](link needed).

L’onglet **Utilisation de la facturation mesurée** contient des informations sur l’utilisation des types d’offres dont l’utilisation est mesurée par une dimension de compteur. La surutilisation du type d’offre SaaS y est affichée. L’onglet fournit des représentations graphiques des tendances de surutilisation pour la facturation à l’usage de SaaS :

- **Tendance de dépassement par dimension de compteur** : affiche la tendance de dépassement par mois de la dimension de compteur sélectionnée pour une offre. L’axe X représente le mois et l’axe Y représente la quantité d’utilisation. L’unité de mesure du compteur personnalisé est également indiquée sur l’axe Y.
- **Tendance de surutilisation par référence SKU** : représente la tendance de la quantité d’utilisation de la dimension de compteur sélectionnée par références SKU. Les références SKU affichées constituent les cinq références SKU enregistrant la plus grande quantité d’utilisation de l’offre sélectionnée.
- **Tendance de surutilisation des 50 clients principaux** : les 50 premières offres enregistrant le plus grand nombre d’heures d’utilisation sont affichées sur un ***leaderboard*** et sont classées en fonction de l’utilisation la plus élevée du compteur personnalisé. Sélectionnez un client dans le leaderboard pour voir la tendance d’utilisation d’une dimension de compteur sélectionnée.
- **Tendance de surutilisation des clients principaux** : présente les centiles des clients principaux qui contribuent au pourcentage de l’utilisation globale. Les centiles des clients principaux sont indiqués le long de l’axe X et dépendent de la quantité d’utilisation par les clients. L’axe Y indique la quantité d’utilisation. Vous pouvez afficher les détails en déplaçant le curseur sur les points le long du graphique en courbes.

> [!NOTE]
> Les détails d’utilisation et tous les graphiques affichés dans cette page s’appliquent à la dimension de compteur sélectionnée pour le filtre de page.

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble des rapports d’analytique disponibles sur la Place de marché commerciale de l’Espace partenaires, consultez [Analytique de la place de marché commerciale dans l’Espace partenaires](./analytics.md).
- Pour consulter les graphes, tendances et valeurs des données agrégées qui résument l’activité de la Place de marché pour vos offres, consultez [Tableau de bord Résumé dans l’analytique de la Place de marché commerciale](./summary-dashboard.md).
- Pour plus d’informations sur vos commandes dans un format graphique et téléchargeable, consultez [Tableau de bord des commandes dans l’analytique de la place de marché commerciale](./orders-dashboard.md).
- Pour des informations détaillées sur vos clients, y compris les tendances de croissance, consultez [Tableau de bord client dans l’analytique de la place de marché commerciale](./customer-dashboard.md).
- Pour obtenir la liste de vos demandes de téléchargement des 30 derniers jours, consultez [Tableau de bord des téléchargements dans l’analytique de la Place de marché commerciale](./downloads-dashboard.md).
- Pour une vue centralisée des commentaires clients sur les offres de Microsoft AppSource et de la Place de marché Azure, consultez [Tableau de bord Évaluations et avis dans l’analytique de la Place de marché commerciale](./ratings-reviews.md).
- Pour voir les questions fréquentes sur l’analytique de la place de marché commerciale et un dictionnaire complet des termes liés aux données, consultez [Questions fréquentes et terminologie pour l’analytique de la place de marché commerciale](./faq-terminology.md).
