---
title: Tableau de bord Insights sur la place de marché dans l’analytique de la place de marché commerciale dans l’Espace partenaires
description: Accédez à un résumé du Web Analytics de la place de marché, qui permet aux éditeurs de mesurer l’engagement client vis-à-vis des vitrines AppSource et Place de marché Azure.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: a547ced9df98298361360ecab88036599cd86027
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275847"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Tableau de bord Insights sur la place de marché dans l’analytique de la place de marché commerciale

Cet article donne des informations sur le tableau de bord Insight sur la place de marché de l’Espace partenaires. Ce tableau de bord présente un résumé du Web Analytics de la place de marché, qui permet aux éditeurs de mesurer l’engagement client vis-à-vis de leurs fiches produits respectives sur les vitrines des places de marché : AppSource et la Place de marché Azure.

## <a name="marketplace-insights-dashboard"></a>Tableau de bord des insights de la Place de marché

Pour accéder au **tableau de bord Insights sur la place de marché** de l’Espace partenaires, ouvrez **[l’onglet Analyser](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** sous Place de marché commerciale.

Vous pouvez afficher une représentation graphique des éléments suivants :  

- [Résumé des insights sur la place de marché](#marketplace-insights-summary)
- [Visites de pages par zone géographique](#page-visits-by-geography)  
- [Comparaison des tendances des visites de pages et des visiteurs uniques](#page-visits-versus-unique-visitors-trend)
- [Rapprochement entre les appels à l’action et les visiteurs uniques avec CTA](#call-to-action-versus-unique-visitors-with-ctas)
- [Visites de pages et appels à l’action par offre](#page-visits-and-calls-to-action-by-offers)
- [Tendance du pourcentage d’appels à l’action](#call-to-action-percentage-trend)
- [Visites de pages et appels à l’action par domaine de referral](#page-visits-and-calls-to-action-by-referral-domains)
- [Insights sur la place de marché – Tableau des détails](#marketplace-insights-details-table)

>[!NOTE]
> Pour accéder aux définitions détaillées de la terminologie d’analytique, voir [Questions fréquentes et terminologie pour l’analytique de la place de marché commerciale](./faq-terminology.md).

### <a name="insights-dashboard-layout"></a>Disposition du tableau de bord Insights

Il existe plusieurs façons d’afficher les métriques de la place de marché :

- Onglets de la vitrine
- Filtres de page
- Filtres de date

**Onglets de la vitrine** : vous pouvez afficher séparément les métriques de vos offres dans les onglets AppSource et Place de marché Azure. Sélectionnez la ou les offres dans la liste déroulante sur la droite pour obtenir une visualisation des métriques correspondantes. Par défaut, toutes les offres sont sélectionnées.

![Insights de l’Espace partenaires – Liste déroulante des offres du tableau de bord](./media/insights-offer-dropdown.png)

**Filtres de la page Insights** : ces filtres s’appliquent au niveau de l’offre (fiche produit). Vous pouvez sélectionner plusieurs filtres pour les critères que vous souhaitez. Ce filtre s’applique à l’intégralité de la section Insights sur la place de marché, y compris les graphes et les détails.

![Insights de l’Espace partenaires – Filtres de la page du tableau de bord](./media/insights-page-filter.png)

- Les noms d’offres ne sont précisés que pour les offres qui présentent des visites de pages dans la plage de dates sélectionnée.  
- La sélection par défaut est « Tout » pour chacune des options de filtre.
- Les filtres appliqués indiquent le nombre de sélections pour les choix effectués. Ils ne s’affichent pas pour la sélection « Tout » par défaut.

![Insights de l’Espace partenaires – Filtres appliqués](./media/insights-page-filter-two.png)

**Filtres de date des insights** : ce filtre s’applique à l’intégralité de la section Insights sur la place de marché. Les filtres peuvent inclure des plages de dates prédéterminées ou une plage de dates personnalisée.

![Insights de l’Espace partenaires – Filtres de date](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>Résumé des insights sur la place de marché

La section Résumé des insights sur la place de marché indique le nombre de **Visites de pages**, **Appels à l’action** et **Visiteurs uniques** pour la plage de dates sélectionnée.

### <a name="page-visits"></a>Visites de pages

Ce nombre représente le nombre de sessions utilisateur distinctes sur la page de l’offre (fiche produit) pour une plage de dates sélectionnée. L’indicateur de pourcentage rouge/vert représente le pourcentage de croissance des visites de pages. Le graphique de tendances indique le nombre de visites de pages par mois.

### <a name="unique-visitors"></a>Visiteurs uniques

Ce nombre représente le nombre de visiteurs distincts au cours de la plage de dates sélectionnée pour la ou les offres sélectionnées dans le filtre de page. Un visiteur qui a visité une ou plusieurs fiches produits est compté comme un visiteur unique.

### <a name="call-to-action"></a>Invite à l’action

Ce nombre représente le nombre de clics sur des boutons **d’appel à l’action** effectués sur la page de l’offre (fiche produit). Des **appels à l’action** sont comptabilisés lorsque les boutons **Télécharger maintenant**, **Essai gratuit**, **Contactez-moi** et **Version d’évaluation** sont sélectionnés.

![Insights de l’Espace partenaires – Résumé des appels à l’action](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>Visites de pages par zone géographique

Le carte thermique ci-dessous présente le nombre de **Visites de pages**, **Appels à l’action**, et **Visiteurs uniques en fonction du pays du client**. Les visites de pages les plus élevées sont représentées par des couleurs plus sombres et les moins élevées par des couleurs plus claires.

![Insights de l’Espace partenaires – Répartition géographique](./media/insights-geography.png)

La carte thermique comporte les fonctionnalités suivantes :

- Une grille supplémentaire permet d’afficher les détails des **Visites de pages**, **Appels à l’action** et **Visiteurs uniques** dans un emplacement spécifique, sur lequel vous pouvez zoomer.  
- La **répartition par pays** correspond au nombre total de pays en provenance desquels vos clients ont effectué des visites de pages sur la plage de dates sélectionnée.
- Vous pouvez rechercher et sélectionner un pays de la grille pour zoomer sur un emplacement de la carte. Revenez à la vue d’origine en sélectionnant **Accueil** sur la carte.

## <a name="page-visits-versus-unique-visitors-trend"></a>Comparaison des tendances des visites de pages et des visiteurs uniques

Les colonnes ci-dessous représentent le nombre de visites de pages par mois, indiquées sur l’axe Y (du côté gauche du graphe). La courbe de tendance représente la tendance mensuelle des visiteurs uniques, affichée sur l’axe Y secondaire (du côté droit du graphe), pour les offres publiées sur les vitrines : Azure Marketplace et AppSource.

![Insights de l’Espace partenaires – Comparaison des tendances des visites de pages et des visiteurs uniques](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>Rapprochement entre les appels à l’action et les visiteurs uniques avec CTA

Les colonnes empilées représentent les appels à l’action (CTA) par mois, répartis par type (**Télécharger maintenant**, **Contactez-moi**et **Essai gratuit**) et tracés sur l’axe Y (du côté gauche de la page). La courbe de tendance représente la tendance mensuelle des visiteurs uniques avec CTA, affichée sur l’axe Y secondaire (du côté droit du graphe), pour les offres publiées sur la Place de marché Azure et sur AppSource.

![Insights de l’Espace partenaires – Rapprochement entre les appels à l’action et les visiteurs uniques avec CTA](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>Visites de pages et appels à l’action par offre

Le graphique à secteurs externe représente la répartition des **Visites de pages** en fonction des offres publiées sur la place de marché et sélectionnées dans le filtre. Le graphique interne représente la répartition des **Appels à l’action** pour les mêmes offres.

![Insights de l’Espace partenaires – Visites de pages et appels à l’action par offre](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>Tendance du pourcentage d’appels à l’action

La **Tendance du pourcentage d’appels à l’action** présente le pourcentage de CTA pour les offres publiées sur la place de marché. % CTA = (CTA/visites de pages) * 100.

![Insights de l’Espace partenaires – Tendance du pourcentage d’appels à l’action](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>Visites de pages et appels à l’action par domaine de referral

Le graphe ci-dessous présente les 50 premiers domaines de referral. Si vous sélectionnez un domaine de referral spécifique, la tendance mensuelle des visites de pages et des appels à l’action apparaît sur le graphe à droite.

![Insights de l’Espace partenaires – Visites de pages et appels à l’action par domaine de referral et campagne](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>Insights sur la place de marché – Tableau des détails

Ce tableau affiche en mode Liste les visites de pages et les appels à l’action des offres sélectionnées, triés par date.

![Insights de l’Espace partenaires – Tableau des détails](./media/insights-details-page.png)

- Les données peuvent être extraites dans un fichier CSV si le nombre d’enregistrements est inférieur à 1 000.
- En présence d’un nombre d’enregistrements supérieur à 1 000, les données exportées sont placées de manière asynchrone sur une page de téléchargement pour une durée de 30 jours.
- Vous pouvez appliquer des filtres pour afficher les données qui vous intéressent. Les données peuvent être filtrées par nom d’offre et par nom de campagne.  

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble des rapports d’analytique disponibles sur la place de marché commerciale de l’Espace partenaires, voir [Analytique de la place de marché commerciale dans l’Espace partenaires](./analytics.md).
- Pour consulter les graphes, tendances et valeurs des données agrégées qui résument l’activité de la place de marché pour vos offres, voir [Tableau de bord Résumé dans l’analytique de la place de marché commerciale](./summary-dashboard.md).
- Pour plus d’informations sur vos commandes dans un format graphique et téléchargeable, voir [Tableau de bord des commandes dans l’analytique de la place de marché commerciale](./orders-dashboard.md).
- Pour consulter les métriques d’utilisation et de facturation à l’usage des offres de machines virtuelles, voir [Tableau de bord de l’utilisation dans l’analytique de la place de marché commerciale](./usage-dashboard.md).
- Pour des informations détaillées sur vos clients, y compris des tendances de croissance, voir [Tableau de bord client dans l’analytique de la place de marché commerciale](./customer-dashboard.md).
- Pour obtenir la liste de vos demandes de téléchargement des 30 derniers jours, voir [Tableau de bord des téléchargements dans l’analytique de la place de marché commerciale](./downloads-dashboard.md).
- Pour une vue consolidée des commentaires des clients pour les offres sur la Place de marché Azure et AppSource, voir [Tableau de bord Évaluations et avis dans l’analytique de la place de marché commerciale](./ratings-reviews.md).
- Pour obtenir des réponses aux questions fréquentes sur l’analytique de la place de marché commerciale et un dictionnaire complet des termes liés aux données, voir [Questions fréquentes et terminologie pour l’analytique de la place de marché commerciale](./faq-terminology.md).
