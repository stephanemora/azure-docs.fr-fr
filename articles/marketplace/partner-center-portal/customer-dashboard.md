---
title: Tableau de bord Client dans l’analytique de la place de marché commerciale Microsoft dans l’Espace partenaires, la Place de marché Azure et Microsoft AppSource
description: Découvrez comment accéder à des informations détaillées sur vos clients, notamment les tendances de croissance, avec le tableau de bord Client dans l’analytique de la Place de marché commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2020
author: shganesh-dev
ms.author: shganesh
ms.openlocfilehash: 44e992bb1445e1e58f42ec150a8e2c0682eea98b
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87317584"
---
# <a name="customer-dashboard-in-commercial-marketplace-analytics"></a>Tableau de bord Client dans l’analytique de la Place de marché commerciale

Cet article donne des informations sur le **tableau de bord Client** de l’Espace partenaires. Ce tableau de bord présente des informations sur vos clients, notamment les tendances de croissance, dans un format graphique et téléchargeable.

Pour accéder au **tableau de bord Client**, ouvrez le tableau de bord **[Analyser](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** sous Place de marché commerciale.

>[!NOTE]
> Pour accéder aux définitions détaillées de la terminologie d’analytique, consultez [Questions fréquentes et terminologie pour l’analytique de la Place de marché commerciale](./faq-terminology.md).

## <a name="customer-dashboard"></a>Tableau de bord Client

Le **tableau de bord Client** du menu **Analyser** présente les données des clients qui ont acheté vos offres. Vous pouvez afficher une représentation graphique des éléments suivants :

- [Résumé des clients](#customer-summary)
- [Clients par zone géographique](#customer-by-geography)
- [Tendances clients](#customer-trends)
- [Clients par commande et utilisation](#customers-by-orders-and-usage)
- [Clients par plan](#customers-by-plans)
- [Commandes et utilisation par type de client](#orders-and-usage-by-customer-type)
- [Tableau des détails des commandes](#customer-details-table)
- [Filtres de la page Client](#customer-page-filters)

La latence maximale entre l’acquisition du client et la création de rapports dans l’Espace partenaires est de 48 heures.

### <a name="customer-summary"></a>Résumé des clients

La section Résumé des clients présente le nombre total de clients (anciens, existants, nouveaux) sur la plage de dates sélectionnée.

- Le nombre total de clients est défini comme le nombre de clients qui ont acheté votre offre et comptent au moins une commande non annulée.
- Le pourcentage de croissance des clients comparé au mois précédent est indiqué par le nombre et l’indicateur ascendant en vert ou l’indicateur descendant en rouge.
- Les tendances de croissance sont représentées par des graphiques à barres et affichent la valeur pour chaque mois lorsque vous placez le curseur sur les colonnes du graphique.

Il existe trois **types de clients** : anciens, existants et nouveaux.

- Un nouveau client a acheté une ou plusieurs de vos offres pour la première fois au cours du mois sélectionné.
- Un client existant a acheté une ou plusieurs de vos offres avant le mois sélectionné.
- Un client parti a annulé toutes les offres précédemment achetées.

### <a name="customer-by-geography"></a>Clients par zone géographique

Le graphe **Client par zone géographique** présente le nombre total de clients et le nombre de clients acquis au cours de la plage de dates sélectionnée, mis en relation avec le pays/la région du client. Les couleurs claires sur la carte indiquent des valeurs faibles et les couleurs foncées des valeurs élevées. Sélectionnez l’un des enregistrements du tableau pour zoomer sur un pays/une région.

Le carte thermique présente le nombre de clients et le pourcentage par pays/région du client. Vous pouvez vous déplacer sur la carte pour voir l’emplacement exact et zoomer sur un emplacement spécifique. Cette carte comporte une grille supplémentaire permettant d’afficher le pourcentage de clients par emplacement, ainsi que les clients récemment ajoutés à cet emplacement.

### <a name="customer-trends"></a>Tendances clients

Le graphique **Tendances clients** affiche le nombre total de clients, y compris nouveaux, existants et partis, avec une tendance de croissance mois par mois.

- Le graphique en courbes représente les pourcentages globaux de croissance des clients.
- La colonne du mois représente le nombre de clients regroupés par clients nouveaux, existants et partis.
- Le nombre de clients partis s'affiche dans le sens négatif de l’axe y.
- Vous pouvez sélectionner les éléments d'une légende spécifique pour afficher plus de détails. Par exemple, sélectionnez les nouveaux clients dans la légende pour afficher uniquement les nouveaux clients.
- Vous pouvez utiliser le curseur en haut du graphique pour effectuer un défilement vers la droite ou vers la gauche de l'axe x et sélectionner des points de données spécifiques à examiner plus en détail.
- Placez le curseur sur une colonne du graphique pour afficher des détails sur ce mois uniquement.

### <a name="customers-by-orders-and-usage"></a>Clients par commande et utilisation

Le graphique **Clients par commande/utilisation** comporte trois onglets : « Commandes », « Utilisation normalisée » et « Utilisation brute ». Le graphe **Premier centile de clients** est affiché le long de l’axe X, en fonction du nombre de commandes. L’axe y affiche le nombre de commandes du client. L’axe Y secondaire (graphique linéaire) présente le pourcentage cumulé du nombre total de commandes. Vous pouvez afficher les détails en déplaçant le curseur sur les points le long du graphique en courbes.

À titre d’exemple, consultez le graphe ci-dessous pour l’utilisation normalisée : Le trentième centile de clients représente 87 % de l’utilisation normalisée cumulée. Le trentième centile de clients ne correspond qu’à 1,57 millions heures d’utilisation.

### <a name="customers-by-plans"></a>Clients par plan

Les graphes **Clients par plan/utilisation** sont décrits ci-dessous.

1. Le classement présente les détails des 50 premiers clients par nombre de commandes. Les détails du client sélectionné sont présentés dans les sections 2, 3 et 4 de ce tableau de bord.
2. Les détails du profil client s’affichent dans cet espace lorsque les éditeurs sont connectés avec un rôle Propriétaire. S’ils sont connectés avec un rôle Collaborateur, les détails de cette section ne sont pas disponibles.
3. Le graphique en anneau **Commandes par plan** présente la répartition des commandes passées pour les plans. Les cinq premiers plans par nombre de commandes sont affichés, tandis que les autres sont regroupés sous « Tout le reste ».
4. Le graphique en anneau **Sièges par plan** présente la répartition des sièges commandés pour les plans. Les cinq premiers plans par nombre de sièges sont affichés, tandis que les autres sont regroupés sous « Tout le reste ».

### <a name="orders-and-usage-by-customer-type"></a>Commandes et utilisation par type de client

Le graphe **Commandes/utilisation par type de client** présente le nombre de commandes et les heures d’utilisation normalisée et brute répartis entre les nouveaux clients et les clients existants, en fonction respectivement de la sélection des commandes et de l’utilisation normalisée et brute, dans le graphe.

- Un nouveau client a acheté une ou plusieurs de vos offres ou déclaré une utilisation pour la première fois au cours du même mois civil (axe Y).
- Un client existant a acheté une de vos offres ou déclaré une utilisation avant le mois civil de déclaration (axe Y).

### <a name="customer-details-table"></a>Tableau des détails des clients

Le tableau **Détails des clients** présente la liste numérotée des 1 000 meilleurs clients triés selon la date à laquelle ils ont acheté une de vos offres pour la première fois.

- Les informations personnelles sur les clients sont uniquement disponibles s'ils ont donné leur consentement. Pour consulter ces informations, vous devez être connecté avec le niveau d’autorisations du rôle Propriétaire. En savoir plus sur les rôles et autorisations des utilisateurs.
- Chaque colonne de la grille peut être triée.
- Les données peuvent être extraites dans un fichier TSV en présence d'un nombre d’enregistrements inférieur à 1 000.
- En présence d’un nombre d’enregistrements supérieur à 1 000, les données exportées sont placées de manière asynchrone sur une page de téléchargement pour une durée de 30 jours.
- Appliquez des filtres au tableau pour afficher uniquement les données qui vous intéressent. Filtrez les données par nom de société, ID client, ID d’abonnement à la Place de marché, type de licence Azure, date d’acquisition, date d’annulation, adresse e-mail du client, pays/région/état/ville/code postal du client, langue du client, etc.
- Quand une offre est achetée par un client protégé, les informations contenues dans **Données détaillées du client** sont masquées (************).
- Les détails de la dimension Client, tels que le nom de la société, le nom du client et son adresse e-mail, sont au niveau de l’ID de l’organisation, et non au niveau de la transaction Place de marché Azure ou AppSource.

### <a name="customer-page-filters"></a>Filtres de la page Client

Les filtres de la **page Clients** s’appliquent au niveau de la page Clients. Vous pouvez sélectionner plusieurs filtres afin d’afficher le graphique pour les critères et les données souhaités dans la grille/l’exportation « Données de commandes détaillées ». Les filtres sont appliqués aux données extraites pour la plage de données sélectionnée dans l’angle supérieur droit de la page Commandes.

>[!NOTE]
> Pour une définition détaillée de chacun des champs de la grille Client, des filtres de page et de leurs sélections possibles, consultez l’article FAQ indiqué ci-dessous.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble des rapports d’analytique disponibles sur la Place de marché commerciale de l’Espace partenaires, consultez [Analytique de la Place de marché commerciale dans l’Espace partenaires](./analytics.md).
- Pour consulter les graphes, tendances et valeurs des données agrégées qui résument l’activité de la Place de marché pour vos offres, consultez [Tableau de bord Résumé dans l’analytique de la Place de marché commerciale](./summary-dashboard.md).
- Pour plus d’informations sur vos commandes dans un format graphique et téléchargeable, consultez [Tableau de bord des commandes dans l’analytique de la Place de marché commerciale](./orders-dashboard.md).
- Pour voir les métriques de facturation à l’utilisation des offres de machines virtuelles, consultez [Tableau de bord de l’utilisation dans l’analytique de la place de marché commerciale](./usage-dashboard.md).
- Pour obtenir la liste de vos demandes de téléchargement des 30 derniers jours, consultez [Tableau de bord des téléchargements dans l’analytique de la Place de marché commerciale](./downloads-dashboard.md).
- Pour une vue centralisée des commentaires clients sur les offres de la Place de marché Azure et AppSource, consultez [Tableau de bord Évaluations et avis dans l’analytique de la place de marché commerciale](./ratings-reviews.md).
- Pour voir les questions fréquentes sur l’analytique de la place de marché commerciale et un dictionnaire complet des termes liés aux données, consultez [Questions fréquentes et terminologie pour l’analytique de la place de marché commerciale](./faq-terminology.md).
