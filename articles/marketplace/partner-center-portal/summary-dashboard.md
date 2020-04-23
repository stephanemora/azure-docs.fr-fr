---
title: Tableau de bord Résumé de l’analytique de l’Espace partenaires sur la place de marché commerciale
description: Découvrez comment accéder aux graphes, tendances et valeurs des données agrégées qui résument l’activité de la place de marché dans le tableau de bord Résumé de l’Espace partenaires.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 86bce85c6ec273b4ab5f9f00cbae68fc054f53f5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262398"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Tableau de bord Résumé dans l’analytique de la Place de marché commerciale

Cet article fournit des informations sur le tableau de bord Résumé de l’Espace partenaires. Ce tableau de bord présente les graphes, tendances et valeurs des données agrégées qui résument l’activité de la place de marché pour vos offres.

Pour accéder au tableau de bord Résumé, ouvrez le **[tableau de bord Analyser](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** sous Place de marché commerciale.

>[!NOTE]
> Pour avoir des définitions détaillées des termes d’analytique, consultez [Questions fréquentes et terminologie relatives à l’analytique de la Place de marché commerciale](./faq-terminology.md).

## <a name="summary-dashboard"></a>Tableau de bord Résumé

Le tableau de bord **Résumé** présente une vue d’ensemble en fonction de chaque type d’offre. **Insights** offre un aperçu rapide des informations critiques et une vue générale des activités de vente de vos offres. Vous pouvez visualiser ces rapports dans le tableau de bord **Résumé**. Cet article décrit plus en détail chacun des éléments suivants :

- [Plage de dates](#date-range)
- [Section Résumé](#summary-section)
- [Clients par zone géographique](#customers-by-geography)
- [Graphiques de tendances de croissance](#growth-trend-charts)
- [Classement des clients](#customer-leaderboard)
- [Tendance du nombre de sièges](#seat-count-trend)
- [Tendance des commandes SaaS d’essai gratuit](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>Éléments du tableau de bord Résumé

Les sections suivantes expliquent comment utiliser le tableau de bord Résumé et lire les données.

### <a name="date-range"></a>Plage de dates

Une sélection par plage de dates est disponible en haut à droite de chaque page. Personnalisez la sortie des graphes de la page **Résumé** en sélectionnant la plage de dates des 3, 6 ou 12 derniers mois, ou une plage de dates personnalisée d’une durée maximale de 12 mois. La plage de dates par défaut est de six mois.

![Tableau de bord Analyser de l'Espace partenaires](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Section Résumé

La section Résumé présente le nombre total de commandes créées et de clients acquis ainsi que l’utilisation déclarée sur la plage de dates sélectionnée. Le mois en cours déjà commencé sera exclu du calcul de ces métriques. Par exemple : Si vous avez sélectionné comme période 6 mois, les heures d’utilisation sont calculées pour les six mois précédant le mois en cours. Si une plage de dates personnalisée est sélectionnée, la partie entamée du mois en cours sera exclue du calcul.

![Tendances de croissance dans le tableau de bord Résumé](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>Lire la section Résumé

- **Commandes** : nombre total de commandes passées (hors commandes annulées) des offres que vous avez publiées jusqu’à présent.
- **Clients** : nombre total de clients qui ont acheté vos offres et comptent au moins une commande non annulée.
- **Heures d’utilisation normalisée** : heures d’utilisation normalisée afin de prendre en compte le nombre de cœurs de machines virtuelles ([nombre de cœurs de machines virtuelles] x [heures d’utilisation brute]). Les machines virtuelles désignées sous le nom de « SHAREDCORE » utilisent 1/6 (ou 0,1666) comme multiplicateur [nombre de cœurs de machines virtuelles].
- **Heures d’utilisation brute** : durée en heures d’exécution des machines virtuelles. La valeur en pourcentage en regard de **nombre total de commandes**, **nombre total de clients**, **heures d’utilisation normalisée**, **heures d’utilisation brute**, **visites de pages** et **appels à l’action** représente la croissance de l’utilisation pour la plage de dates sélectionnée ([utilisation du dernier mois – utilisation du premier mois]/utilisation du premier mois). Comme nous l’avons vu, la partie entamée du mois en cours sera exclue de cette métrique.
- **Tendances de croissance** : si vous pointez sur les colonnes du graphe, les histogrammes indiquent la valeur pour chaque mois.
- **Triangle vert pointant vers le haut** : indique une tendance de croissance positive.
- **Triangle rouge pointant vers le bas** : indique une tendance de croissance négative par rapport au mois précédent.

### <a name="customers-by-geography"></a>Clients par zone géographique

La carte thermique **Clients par zone géographique** affiche un nombre de clients sur une carte du monde.

![Clients par zone géographique dans le tableau de bord Résumé](./media/summary-customers-by-geography.png)

- Vous pouvez déplacer la carte pour afficher l'emplacement exact.
- Vous pouvez zoomer sur un emplacement spécifique.
- La carte thermique comporte une grille supplémentaire présentant les détails du nombre de clients, du nombre de commandes et des heures d’utilisation normalisée à l’endroit spécifié.
- Vous pouvez rechercher et sélectionner un pays de la grille pour zoomer sur un emplacement de la carte. Pour revenir à la vue d’origine, appuyez sur le bouton **Accueil** de la carte.
- Un **nouveau** client a acheté une de vos offres pour la première fois au cours du mois compris dans la plage de dates sélectionnée.

### <a name="growth-trend-charts"></a>Graphiques de tendances de croissance

Vous pouvez afficher les tendances de croissance de vos **commandes passées** (commandes annulées incluses), de vos **clients acquis** (clients perdus inclus) et de votre **utilisation** déclarée mois par mois en fonction de la plage de dates sélectionnée. Pour analyser ces tendances de manière plus approfondie, sélectionnez les liens situés sous le graphe afin d’accéder aux pages **Commandes**, **Utilisation**, **Clients** et **Insights sur la place de marché**.

Les graphiques de tendances **Visites de pages et appel à l’action** de l’offre de la place de marché sont présentés sur deux onglets pour la Place de marché Azure et AppSource.

![Graphiques de tendances Visites de pages et appels à l’action dans le tableau de bord Résumé](./media/summary-page-visits-and-cta.png)

Le graphe **Commandes par offre** organise vos commandes par nom d’offre.

Le graphique à secteurs **Commandes par canal de vente** organise vos commandes (commandes annulées par les clients incluses) de la plage de dates sélectionnée par canal de vente. Le canal de vente est le type de contrat de licence utilisé par les clients pour acheter Azure : fournisseur de solutions Cloud (CSP), Entreprise, Entreprise par le biais d’un revendeur, GTM et paiement à l’utilisation.

Les graphiques à secteurs **Utilisation par offre** et **Utilisation par canal de vente** présentent respectivement la répartition de l’utilisation par offre principale et par canal de vente. Le graphique à secteurs intérieur représente l’utilisation brute et le graphique à secteurs extérieur l’utilisation normalisée.

Les graphiques à secteurs **Commandes par type de licence de place de marché** et **Utilisation par type de licence de place de marché** présentent la répartition des commandes et de l’utilisation par type de licence. Les types comprennent :

- **facturé via Azure** : Microsoft facture les clients en votre nom lorsque vous choisissez de vendre votre offre par le biais de Microsoft avec ce type de licence. Les modes de paiement incluent le paiement à l’utilisation par carte de crédit ou facturation d'entreprise.
- **BYOL (apportez votre propre licence)**  : Microsoft ne facture rien aux clients qui utilisent ce type d’offre de place de marché. Cette utilisation apparaît comme **Télécharger maintenant (gratuit)** sur la place de marché.
- **Gratuit** : Microsoft ne facture rien aux clients qui utilisent ce type d’offre de place de marché. Cette utilisation apparaît comme **Essai gratuit** sur la place de marché.
- **Microsoft en tant que revendeur** : représente les offres vendues par des revendeurs Microsoft dans le cadre du **programme Fournisseur de solutions Cloud (CSP)** .

### <a name="customer-leaderboard"></a>Classement des clients

Les 50 clients présentant le plus grand nombre de commandes s'affichent dans un *classement*, trié par plus grand nombre de commandes et pourcentage de commandes.

- Sélectionnez un client pour afficher les détails de son profil, ses commandes organisées par offre ou ses commandes organisées par type de licence Azure et canal de tarification.
- Le graphique en anneau **Offres par commandes** présente les quatre meilleures offres (par nombre de commandes) et les offres restantes groupées dans « Autres offres ».
- Le graphique en anneau **Utilisation normalisée par offre** présente les cinq premières offres par ordre d’utilisation.

> [!NOTE]
> Les informations personnelles sur les clients s'affichent uniquement si le client a donné son le consentement. Pour consulter ces informations, vous devez être connecté avec un rôle **Propriétaire**. Les utilisateurs dotés d'un rôle **Contributeur** n'y ont pas accès. [En savoir plus sur les rôles et autorisations d'utilisateur](./manage-account.md#define-user-roles-and-permissions).

### <a name="seat-count-trend"></a>Tendance du nombre de sièges

Le graphe **Commandes par siège/site** présente la répartition de toutes les commandes passées en fonction du modèle tarifaire. Le graphe **Tendance du nombre de sièges** présente les sièges par rapport aux commandes passées pour l’ensemble des offres SaaS (software as a service) par siège.

### <a name="free-trials-saas-orders-trend"></a>Tendance des commandes SaaS d’essai gratuit

Le graphe **Tendance des commandes SaaS d’essai gratuit** présente la tendance des commandes pour les offres SaaS d’essai gratuit avec période d’essai de 30 jours.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble des rapports d’analytique disponibles sur la Place de marché commerciale de l’Espace partenaires, consultez [Analytique de la Place de marché commerciale dans l’Espace partenaires](./analytics.md).
- Pour plus d’informations sur vos commandes dans un format graphique et téléchargeable, consultez [Tableau de bord des commandes dans l’analytique de la Place de marché commerciale](./orders-dashboard.md).
- Pour voir les métriques de facturation à l’utilisation des offres de machines virtuelles, consultez [Tableau de bord de l’utilisation dans l’analytique de la place de marché commerciale](./usage-dashboard.md).
- Pour obtenir des informations détaillées sur vos clients, y compris des tendances de croissance, consultez [Tableau de bord Client dans l’analytique de la Place de marché commerciale](./customer-dashboard.md).
- Pour obtenir la liste de vos demandes de téléchargement des 30 derniers jours, consultez [Tableau de bord des téléchargements dans l’analytique de la Place de marché commerciale](./downloads-dashboard.md).
- Pour une vue centralisée des commentaires clients sur les offres de la Place de marché Azure et AppSource, consultez [Tableau de bord Évaluations et avis dans l’analytique de la place de marché commerciale](./ratings-reviews.md).
- Pour voir les questions fréquentes sur l’analytique de la place de marché commerciale et un dictionnaire complet des termes liés aux données, consultez [Questions fréquentes et terminologie pour l’analytique de la place de marché commerciale](./faq-terminology.md).
