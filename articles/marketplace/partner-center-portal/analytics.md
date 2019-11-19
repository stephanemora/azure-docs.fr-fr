---
title: Analytics pour la Place de marché commerciale dans l’Espace partenaires
description: Découvrez comment accéder aux rapports analytiques pour surveiller les ventes, évaluer les performances et optimiser vos offres sur la Place de marché.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 53d7cb125f59491190aadb07755954ade78356fa
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075522"
---
# <a name="analytics-for-the-commercial-marketplace-in-partner-center"></a>Analytics pour la Place de marché commerciale dans l’Espace partenaires

Découvrez comment accéder aux rapports analytiques dans Microsoft Partner Center pour surveiller les ventes, évaluer les performances et optimiser vos offres sur la Place de marché. En tant que partenaire, vous pouvez surveiller vos listes d'offres à l'aide de la visualisation des données et de graphiques d'insights pris en charge par l'Espace partenaires et trouver des moyens d’optimiser vos ventes. Les outils d’analyse améliorés vous permettent d’agir sur les résultats des performances et de maintenir de meilleures relations avec vos clients et revendeurs.

Pour accéder aux outils d'analyse de l'Espace partenaires, ouvrez le tableau de bord **[Analyser](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** sous Place de marché commerciale.

|**Tableau de bord**|**Contenu affiché**|
|:---|:---|
|[Résumé](#summary-dashboard)|Graphes, tendances et valeurs de données agrégées qui résument l’activité de la Place de marché liée à vos offres|
|[Commandes](#orders-dashboard)|Informations relatives à vos commandes dans un format graphique et téléchargeable|
|[Clients](#customer-dashboard)|Informations sur vos clients, y compris les tendances de croissance, présentées dans un format graphique et téléchargeable|
|[Téléchargements](#downloads-dashboard)|Liste de vos requêtes de téléchargement durant les 30 derniers jours|
|[Évaluations et avis](#ratings-and-reviews)| Informations sur les évaluations et les avis laissés par vos clients pour les offres qu’ils ont acquises.

## <a name="summary-dashboard"></a>Tableau de bord Résumé

Le tableau de bord **Résumé** présente une vue d’ensemble en fonction de chaque type d’offre. **Insights** offre un aperçu rapide des informations critiques et une vue générale des activités de vente de vos offres. Vous pouvez visualiser ces rapports à l’aide du tableau de bord **Résumé** :

- [Nombre total de commandes](#totals)
- [Nombre total de clients](#totals)
- [Emplacement géographique de vos clients](#customers-by-geography)
- [Tendances émergentes en fonction des informations relatives à vos clients et commandes](#growth-trend-chart)
- [Classement des clients avec le plus grand nombre de commandes](#customer-leaderboard)
- [Nombre de commandes, organisées par nom d'offre](#offers-by-orders)

### <a name="date-range"></a>Plage de dates

Une sélection par plage de dates est disponible en haut à droite de chaque page. La sortie des graphiques de la page **Résumé** peut être personnalisée en sélectionnant une plage de dates en fonction des 3, 6 ou 12 derniers mois, ou en sélectionnant une plage de dates personnalisée d'une durée maximale de 12 mois. La plage de dates par défaut est de six mois.

![Tableau de bord Analyser de l'Espace partenaires](./media/analyze-dashboard.png)

### <a name="totals"></a>Totaux

La section **Totaux** affiche le nombre de commandes créées ou de clients acquis dans la plage de dates sélectionnée. 

- Le pourcentage situé en regard de **Nombre total de commandes** et **Nombre total de clients** représente la progression par rapport au mois précédent. 
- Un triangle vert pointant vers le haut indique une tendance positive. Un triangle rouge pointant vers le bas indique une tendance négative par rapport au mois précédent. 
- Les tendances de croissance liées aux commandes et aux clients sont représentées par des graphiques à barres et affichent la valeur pour chaque mois lorsque vous placez le curseur sur les colonnes du graphique.

![Totaux du tableau de bord Analyser de l'Espace partenaires](./media/analyze-totals.png)

### <a name="customers-by-geography"></a>Clients par zone géographique

La carte thermique **Clients par zone géographique** affiche un nombre de clients sur une carte du monde.

- Vous pouvez déplacer la carte pour afficher l'emplacement exact.
- Vous pouvez zoomer sur un emplacement spécifique.
- La carte thermique dispose d'une grille supplémentaire pour afficher les détails en termes de nombre de clients (ou de [nombre de commandes](#orders-by-geography)) d'un emplacement spécifique.
- Vous pouvez rechercher et sélectionner un pays de la grille pour zoomer sur un emplacement de la carte. Pour revenir à la vue d’origine, appuyez sur le bouton **Accueil** de la carte.
- Un **nouveau** client a acheté une de vos offres pour la première fois au cours du mois compris dans la plage de dates sélectionnée.

![Zone géographique des clients de l'Espace partenaires](./media/analyze-customer-geography.png)

### <a name="growth-trend-chart"></a>Graphique de tendances de croissance

Vous pouvez afficher des tendances basées sur la croissance de vos **Commandes créées** ou **Clients acquis**, affichés mois par mois en fonction de la plage de dates sélectionnée. Vous pouvez analyser de manière plus approfondie ces tendances en sélectionnant les liens situés sous le graphique pour accéder aux pages **Commandes** ou **Clients**.

![Tendances de croissance du tableau de bord Analyser de l'Espace partenaires](./media/analyze-growth-trends.png)

### <a name="customer-leaderboard"></a>Classement des clients

Les 50 clients présentant le plus grand nombre de commandes s'affichent dans un *classement*, trié par plus grand nombre de commandes et pourcentage de commandes. 

- Sélectionnez un client pour afficher les détails de son profil, ses commandes organisées par offre ou ses commandes organisées par type de licence Azure et canal de tarification. 
- Le graphique en anneau **Offres par commandes** présente les quatre meilleures offres (par nombre de commandes) et les offres restantes groupées dans « Autres offres ».

> [!NOTE]
> Les informations personnelles sur les clients s'affichent uniquement si le client a donné son le consentement. Pour consulter ces informations, vous devez être connecté avec un rôle **Propriétaire**. Les utilisateurs dotés d'un rôle **Contributeur** n'y ont pas accès. [En savoir plus sur les rôles et autorisations d'utilisateur](./manage-account.md#define-user-roles-and-permissions).

![Tendances de croissance du tableau de bord Analyser de l'Espace partenaires](./media/analyze-growth-trends.png)

### <a name="offers-by-orders"></a>Offres par commandes

Le tableau **Offres par commandes** organise vos commandes actives par nom d'offre. 

- Vous pouvez faire glisser les tranches du graphique en anneau de gauche vers le graphique en anneau de droite pour afficher plus de détails sur l’offre que vous avez déplacée. Ces deux graphiques vous permettent de comparer une offre spécifique par rapport aux performances de toutes vos autres offres (« Rest All »). 

![Offres par commandes dans le menu Analyser de l'Espace partenaires](./media/analyze-offer-by-order.png)

## <a name="orders-dashboard"></a>Tableau de bord des commandes

Le tableau de bord **Commandes** du menu **Analyser** affiche les commandes en cours pour l’ensemble de vos offres SaaS. Vous pouvez afficher des représentations graphiques de ce qui suit :

- [Nombre total de commandes](#order-totals)
- [Commandes par zone géographique](#orders-by-geography)
- [Tendances en matière de commandes actives et annulées](#trends-for-active-and-canceled-orders)
- [Commandes organisées par type de licence de Place de marché](#orders-by-marketplace-license-type)
- [Commandées par clients nouveaux et existants](#orders-by-customer-type)
- [Tableau des détails des commandes](#order-details-table)

> [!NOTE]
> Il existe des différences sur la manière dont les rapports d'analyse s'affichent dans le Portail Cloud Partner et le nouveau programme Place de marché commerciale de l'Espace partenaires. En effet, la section **Seller Insights** du Portail Cloud Partner est doté d'un onglet **Commandes et utilisation** qui affiche les données des offres basées sur l'utilisation et des offres non basées sur l’utilisation. Dans l'Espace partenaires, la page **Commandes** propose un onglet distinct pour les offres SaaS.

### <a name="order-totals"></a>Nombre total de commandes

La section **Nombre total de commandes** affiche le nombre de toutes les commandes créées, y compris des commandes **actives** et **annulées** dans la [plage de dates](#date-range) sélectionnée. 

- Le pourcentage situé en regard de **Nombre total de commandes** et représente la progression par rapport au mois précédent. 
- Un triangle vert pointant vers le haut indique une tendance positive. Un triangle rouge pointant vers le bas indique une tendance négative par rapport au mois précédent. 
- Les tendances de croissance sont représentées par des graphiques à barres et affichent la valeur pour chaque mois lorsque vous placez le curseur sur les colonnes du graphique.

### <a name="orders-by-geography"></a>Commandes par zone géographique

La carte thermique **Commandes par zone géographique** affiche le nombre de vos commandes sur une carte du monde et fonctionne la **[carte thermique Clients par zone géographique](#customers-by-geography)** .

### <a name="trends-for-active-and-canceled-orders"></a>Tendances en matière de commandes actives et annulées

Le graphique en anneau **Commandes actives par offres** organise l’ensemble de vos commandes actives en fonction de leurs noms d'offres.

- Les quatre meilleures offres s’affichent dans le graphique et les autres offres sont regroupées sous « Autres offres ».
- Vous pouvez sélectionner des offres spécifiques dans la légende pour afficher uniquement ces offres dans le graphique. 
- Placez le curseur sur une tranche du graphique pour afficher le nombre de commandes et le pourcentage de cette offre par rapport au nombre total de vos commandes pour toutes les offres.
- **Tendance des commandes par offres** affiche les tendances de croissance mois par mois. La colonne du mois représente le nombre de commandes par nom d'offre. Le graphique en courbes affiche la tendance du pourcentage de croissance tracée sur un axe z.
- Vous pouvez utiliser le curseur en haut du graphique pour effectuer un défilement vers la droite et la gauche le long de l’axe x et vous concentrer sur des points de données spécifiques.
- Vous pouvez afficher le graphique des tendances en sélectionnant un élément spécifique de la légende.
- Vous pouvez également choisir d’afficher les tendances et les données relatives aux **commandes annulées**. Le graphique fonctionne de la même manière pour les commandes actives.

![Commandes actives du menu Analyser de l'Espace partenaires](./media/analyze-active-orders.png)

### <a name="orders-by-marketplace-license-type"></a>Commandes par type de licence de Place de marché
<!-- Section needs review and clarification!  -->
Le graphique **Commandes par type de licence de Place de marché** affiche un nombre de commandes mois par mois basé sur le type de licence et la méthode de facturation de l’offre sur la Place de marché. Les types comprennent :

- **facturé via Azure** : Microsoft facture les clients pour votre compte lorsque vous choisissez de [vendre votre offre via Microsoft](./create-new-saas-offer.md#sell-through-microsoft) avec ce type de licence. Les modes de paiement incluent le paiement à l’utilisation par carte de crédit ou facturation d'entreprise.
- **BYOL (apportez votre propre licence)**  : Microsoft ne facture rien aux clients qui utilisent ce type d'offre de Place de marché. Répertorié en tant que **[Obtenez-en une maintenant (gratuit)](./create-new-saas-offer.md#get-it-now-free)** dans la Place de marché.
- **Gratuit** : Microsoft ne facture rien aux clients qui utilisent ce type d'offre de Place de marché. Répertorié en tant que **[Version d’évaluation gratuite](./create-new-saas-offer.md##free-trial-listing)** dans la Place de marché.
- **Microsoft en tant que revendeur** : Représente des offres vendues par des revendeurs Microsoft dans le cadre du **[programme Fournisseur de solutions cloud (CSP)](./create-new-saas-offer.md)** .

![Commandes par type de licence du menu Analyser de l'Espace partenaires](./media/analyze-license-type.png)

### <a name="orders-by-customer-type"></a>Commandes par type de client

Le graphique à barres **Commandes par type de client** affiche le nombre de commandes réparties entre **nouveaux clients** et **clients existants**. 

- Un **nouveau client** a acheté une ou plusieurs de vos offres pour la première fois au cours du même mois calendaire (axe y). Un **client existant** a acheté une de vos offres avant le mois calendaire signalé (axe y). 
- Un graphique à secteurs supplémentaire représente toutes les commandes créées par le client nouveau ou existant pour la plage de dates sélectionnée.
- Dans les deux graphiques, vous pouvez choisir d'afficher uniquement les clients nouveaux ou existants en sélectionnant la légende respective.

![Commandes par type de client du menu Analyser de l'Espace partenaires](./media/analyze-order-by-customer.png)

### <a name="order-details-table"></a>Tableau des détails des commandes

Le **Table des détails des commandes** affiche la liste numérotée des 1 000 meilleures commandes triées par date d'achat.

- Chaque colonne de la grille peut être triée.
- Les données peuvent être extraites dans un fichier TSV en présence d'un nombre d’enregistrements inférieur à 1 000.
- En présence d'un nombre d'enregistrements supérieur à 1 000, les données exportées sont placées, de manière asynchrone, dans une page de téléchargements pour une durée de 30 jours.
- Des filtres peuvent être appliqués au **Table des détails des commandes** pour afficher uniquement les données qui vous intéressent. Les données peuvent être filtrées par pays, type de licence Azure, type de licence de Place de marché, type d’offre, état de la commande, évaluations gratuites, ID d'abonnement de Place de marché, ID de client et nom de société.

![Détails des commandes du menu Analyser de l'Espace partenaires](./media/analyze-order-details.png)

## <a name="customer-dashboard"></a>Tableau de bord Client

Le tableau de bord **Client** du menu **Analyser** affiche les données des clients qui ont acheté vos offres. Vous pouvez afficher des représentations graphiques de ce qui suit :

- [Nombre total de clients](#customer-totals)
- [Clients par zone géographique](#customers-by-geography)
- [Tendances clients](#customer-trends)
- [Clients par commandes](#customers-by-orders)
- [Tableau des détails des commandes](#customer-details-table)

### <a name="customer-totals"></a>Nombre total de clients

La section **Nombre total de clients** affiche le nombre de tous les clients, y compris des clients nouveaux, existants et partis dans la [plage de dates](#date-range) sélectionnée.

- Le pourcentage de croissance des clients comparé au mois précédent est indiqué par le nombre et l'indicateur ascendant en vert ou l'indicateur descendant en rouge.
- Les tendances de croissance sont représentées par des graphiques à barres et affichent la valeur pour chaque mois lorsque vous placez le curseur sur les colonnes du graphique.

#### <a name="customer-types"></a>Types de clients

Il existe trois types de clients : nouveaux, existants et partis. 

- Un nouveau client a acheté une ou plusieurs de vos offres pour la première fois au cours du mois sélectionné.
- Un client existant a acheté une ou plusieurs de vos offres avant le mois sélectionné.
- Un client parti a annulé toutes les offres précédemment achetées.

### <a name="customer-trends"></a>Tendances clients

Le graphique **Tendances clients** affiche le nombre total de clients, y compris nouveaux, existants et partis, avec une tendance de croissance mois par mois.

- Le graphique en courbes représente les pourcentages globaux de croissance des clients. 
- La colonne du mois représente le nombre de clients regroupés par clients nouveaux, existants et partis.
- Le nombre de clients partis s'affiche dans le sens négatif de l’axe y.
- Vous pouvez sélectionner les éléments d'une légende spécifique pour afficher plus de détails. Par exemple, sélectionnez les nouveaux clients dans la légende pour afficher uniquement les nouveaux clients.
- Vous pouvez utiliser le curseur en haut du graphique pour effectuer un défilement vers la droite ou vers la gauche de l'axe x et sélectionner des points de données spécifiques à examiner plus en détail.
- Placez le curseur sur une colonne du graphique pour afficher des détails sur ce mois uniquement.

![Tendances clients du menu Analyser de l'Espace partenaires](./media/analyze-customer-trends.png)

### <a name="customers-by-orders"></a>Clients par commandes

Le graphique **Clients par commandes** affiche le **Meilleur percentile de clients** le long de l'axe x, comme indiqué par leur nombre de commandes. L’axe y affiche le nombre de commandes du client. L’axe z (graphique linéaire) affiche le pourcentage cumulé du nombre total de commandes. Vous pouvez afficher les détails en déplaçant le curseur sur les points le long du graphique en courbes.

À titre d'exemple, ces données de commandes clients indiquent que 30 % de vos meilleurs clients contribuent à hauteur de 83 % du total des commandes, soit 2 130 commandes.

![Commandes clients du menu Analyser de l'Espace partenaires](./media/analyze-customer-orders.png)

### <a name="customer-details-table"></a>Tableau des détails des clients

Le **Tableau des détails des clients** affiche une liste numérotée des 1 000 meilleurs clients triés selon la date à laquelle ils ont acheté une de vos offres pour la première fois.

- Les informations personnelles sur les clients sont uniquement disponibles s'ils ont donné leur consentement. Pour consulter ces informations, vous devez être connecté avec un rôle **Propriétaire**. [En savoir plus sur les rôles et autorisations d'utilisateur](./manage-account.md#define-user-roles-and-permissions).
- Chaque colonne de la grille peut être triée.
- Les données peuvent être extraites dans un fichier TSV en présence d'un nombre d’enregistrements inférieur à 1 000.
- En présence d'un nombre d'enregistrements supérieur à 1 000, les données exportées sont placées, de manière asynchrone, dans une page de téléchargements pour une durée de 30 jours.
- Des filtres peuvent être appliqués au tableau pour afficher uniquement les données qui vous intéressent. Les données peuvent être filtrées par nom de société, ID de client, ID d’abonnement de la Place de marché, type de licence Azure, date d'achat, date d'annulation, adresse e-mail du client, pays/état/ville/code postal du client, langue du client, etc.

![Détails des clients du menu Analyser de l'Espace partenaires](./media/analyze-customer-table.png)

## <a name="downloads-dashboard"></a>Tableau de bord Téléchargements

Le tableau de bord **Téléchargements** du menu **Analyser** affiche les requêtes de téléchargements contenant plus de 1 000 lignes de données relatives aux clients ou commandes.

Vous recevez une notification contextuelle et un e-mail contenant un lien vers le tableau de bord **Téléchargements** chaque fois que vous demandez un téléchargement de plus de 1 000 lignes de données. Ces téléchargements de données sont disponibles pendant 30 jours, puis supprimés.

![Téléchargements du menu Analyser de l'Espace partenaires](./media/analyze-downloads.png)

### <a name="dictionary-of-data-terms"></a>Dictionnaire des termes relatifs aux données

| Nom de l'attribut | Rapports | Définition|
|---|---|---|
| Type de licence Azure | Client, Commande | Type de contrat de licence utilisé par les clients pour acheter Azure. Également appelé canal |
| Type de licence Azure : Fournisseur de solutions cloud | Client, Commande | Le client final se procure Azure et votre offre de la Place de marché par le biais de son fournisseur de solutions cloud, qui agit en tant que revendeur.|
| Type de licence Azure : Entreprise | Client, Commande | Le client final se procure Azure et votre offre de la Place de marché par le biais d'un contrat Entreprise, signé directement auprès de Microsoft.|
| Type de licence Azure : entreprise, via un revendeur  | Client, Commande | Le client final se procure Azure et votre offre de Place de marché par le biais d’un revendeur qui lui propose un contrat Entreprise avec Microsoft.|  |
| Type de licence Azure : paiement à l’utilisation| Client, Commande | Le client final se procure Azure et votre offre de la Place de marché par le biais d’un contrat « Paiement à l’utilisation », signé directement auprès de Microsoft.||
| Nom de l’instance cloud| Ordre| Instance de Microsoft Cloud sur laquelle des machines virtuelles ont été déployées.||
| Nom de l’instance cloud : Azure Global| Ordre| Cloud public global de Microsoft.|| |
| Nom de l’instance cloud : Azure Government | Ordre| Clouds de Microsoft réservés aux gouvernements suivants : Chine, Allemagne ou États-Unis d’Amérique.| |
| Ville du client| Client| Nom de ville fourni par le client final. La ville peut être différente de la ville associée à l’abonnement Azure du client.||
| Langue de communication du client  | Client| Langue dans laquelle le client préfère communiquer.||
| Nom de la société du client | Client, Commande | Nom de société fourni par le client. Le nom peut être différent du nom de la ville associée à l’abonnement Azure du client.|  |
| Pays du client | Client, Commande | Nom de pays fourni par le client. Le pays peut être différent du pays associé à l’abonnement Azure du client.|  |
| Adresse e-mail du client| Client| Adresse e-mail fournie par le client final. L’adresse e-mail peut être différente de l’adresse e-mail associée à l’abonnement Azure du client.||
| Prénom du client| Client| Prénom fourni par le client. Le prénom peut être différent du prénom associé à l’abonnement Azure du client.| |
| ID du client | Client, Commande | Identificateur unique attribué à un client. Un client peut avoir zéro ou plusieurs abonnements de la Place de marché Azure.|  |
| Code postal du client  | Client| Code postal fourni par le client. Le code postal peut être différent du code postal associé à l’abonnement Azure du client.| |
| État du client| Client| État (adresse) fourni par le client. L’État peut être différent de l’État associé à l’abonnement Azure du client.| |
| Date d'acquisition| Client| Date à laquelle le client a acheté pour la première fois une offre que vous avez publiée.| |
| Date d'annulation| Client| Date à laquelle le client a annulé la dernière des offres achetées précédemment.||
| Nouveau client  | Ordre| La valeur identifie un nouveau client achetant une ou plusieurs de vos offres pour la première fois (ou pas). La valeur est « Oui » si le mois calendaire correspond avec la « date d’acquisition ». La valeur est « Non » si le client a acheté une de vos offres avant le mois calendaire signalé. |
| Référence SKU en préversion| Ordre| La valeur indique si vous avez étiqueté la référence SKU en tant que « préversion ». La valeur est « Oui » si la référence SKU a été étiquetée en conséquence, et seuls les abonnements Azure que vous avez autorisés peuvent déployer et utiliser cette image. La valeur est « Non » si la référence SKU n’a pas été identifiée en tant que « préversion ».  |
| Acceptation des messages promotionnels| Client| La valeur indique si le client a accepté les messages promotionnels provenant d’éditeurs de manière proactive. À ce stade, nous n'offrons pas cette possibilité aux clients. Par conséquent, nous avons indiqué « Non » sur le tableau. Une mise à jour interviendra lorsque cette fonctionnalité sera déployée.|
| Type de licence de la Place de marché| Ordre| Mode de facturation de l’offre de la Place de marché.||
| Type de licence de la Place de marché : facturé via Azure| Ordre| Pour cette offre de la Place de marché, Microsoft est votre agent et facture les clients de votre part. (Carte de crédit avec paiement à l’utilisation ou facture d’entreprise)||
| Type de licence de la Place de marché : BYOL (apportez votre propre licence) | Ordre| Le déploiement de la machine virtuelle requiert une clé de licence fournie par le client. Microsoft ne facture rien aux clients qui répertorient leurs offres de cette façon via la Place de marché.||
| Type de licence de la Place de marché : Gratuit| Ordre| L'offre est configurée pour permettre à tous les utilisateurs d’y accéder gratuitement. Microsoft ne facture rien aux clients qui utilisent cette offre.||
| Type de licence de la Place de marché : Microsoft en tant que revendeur  | Ordre| Microsoft est votre revendeur pour cette offre de la Place de marché.|  |
| ID d’abonnement de la Place de marché | Client, Commande | Identificateur unique associé à l’abonnement Azure que le client a utilisé pour acheter votre offre de la Place de marché. Anciennement, GUID d’abonnement Azure.||
| Nom de l’offre  | Ordre| Nom de l’offre de la Place de marché.|| |
| Type d’offre  | Ordre| Type de l’offre de la Place de marché Microsoft.|||
| Type d’offre : Application managée  | Commande, | Utilisez le type d’offre Application Azure : application managée lorsque les conditions suivantes sont requises : Vous déployez une solution basée sur un abonnement pour votre client à l’aide d’une machine virtuelle ou d’une solution IaaS complète. Vous ou votre client exigez que la solution soit gérée par un partenaire. |
| Type d’offre : Azure Application| Commande, | Utilisez le type d’offre Azure Application : modèle de solution si votre solution nécessite une automatisation supplémentaire du déploiement et de la configuration, en plus d’une simple machine virtuelle.||
| Type d’offre : Service de conseil| Ordre| Les services de conseil de la Place de marché Azure aident les clients à se connecter aux services pour gérer et étendre leur utilisation d’Azure.| |
| Type d’offre : Conteneur | Ordre| Utilisez le type d’offre Conteneur si votre solution est une image de conteneur Docker approvisionnée en tant que service de conteneur Azure Kubernetes.||
| Type d’offre : Dynamics 365 Business Central| Ordre| Utilisez ce type d’offre lorsque votre solution est intégrée à Dynamics 365 for Finance and Operations| |
| Type d’offre : Dynamics 365 for Customer Engagement | Ordre| Utilisez ce type d’offre lorsque votre solution est intégrée à Dynamics 365 for Customer Engagement.||
| Type d’offre : Module IoT Edge | Ordre| Les modules Azure IoT Edge sont les plus petites unités de calcul gérées par IoT Edge. Ils peuvent contenir des services Microsoft (par exemple, Azure Stream Analytics), des services tiers ou votre propre code de solution. |
| Type d’offre : Application Power BI | Ordre| Utilisez le type d’offre d'application Power BI lorsque vous déployez une application intégrée à Power BI.|  |
| Type d’offre : Application SaaS| Ordre| Utilisez le type d’offre Applications SaaS pour permettre à votre client d’acheter votre solution technique SaaS sous la forme d’un abonnement.||
| Type d’offre : Machine virtuelle | Ordre| Utilisez le type d’offre Machine virtuelle si vous déployez une appliance virtuelle dans le cadre de l’abonnement associé à votre client.||
| Type d’offre : Extension Visual Studio Marketplace  | Ordre| Type d'offre précédemment disponible pour les développeurs d’extensions Azure DevOps. Désormais, les développeurs d'extensions Azure DevOps peuvent vendre leurs extensions directement aux clients. Les offres d'extensions peuvent être configurées comme payées ou à l'essai. |
| Date d’annulation de la commande| Ordre| Date à laquelle la commande passée sur la Place de marché a été annulée.||
| ID de commande| Ordre| Identificateur unique de la commande du client pour votre service de la Place de marché. Les offres basées sur l’utilisation de machines virtuelles ne sont pas associées à une commande.| |
| Date de la commande| Ordre| Date à laquelle la commande de la Place de marché a été passée.|||
| État de la commande| Ordre| État d’une commande de la Place de marché au moment de la dernière actualisation des données.|     |
| État de la commande : Actif  | Ordre| Le client a passé commande et ne l’a pas annulée.|         |
| État de la commande : annulée | Ordre| Le client a passé commande, puis l’a annulée.||
| Adresse e-mail du fournisseur| Client| Adresse e-mail du fournisseur impliqué dans la relation entre Microsoft et le client final. Si le client est une entreprise passant par un revendeur, ce sera le revendeur. Si un fournisseur de solutions cloud (CSP) est impliqué, ce sera le CSP.|
| Nom du fournisseur| Client| Nom du fournisseur impliqué dans la relation entre Microsoft et le client final. Si le client est une entreprise passant par un revendeur, ce sera le revendeur. Si un fournisseur de solutions cloud (CSP) est impliqué, ce sera le CSP.|
| SKU| Ordre| Nom de la référence SKU, tel que défini lors de la publication. Une offre peut englober de nombreuses références SKU, mais une référence SKU ne peut être associée qu’à une seule offre.||
| Date de fin d'essai| Ordre| La date de fin de la période d’essai de cette commande approche ou est dépassée.||

## <a name="ratings-and-reviews"></a>Évaluations et avis

Quand les clients parcourent, recherchent et achètent des offres dans les deux places de marché, ils peuvent laisser des évaluations et des avis pour les offres qu’ils ont acquises. Ils peuvent soumettre de nouveaux avis ou évaluations, et mettre à jour ou supprimer ceux qu’ils ont déjà soumis. Les avis sont publiés dans la Place de marché Azure ou AppSource sous l’onglet Avis de la page d’affichage du produit de l’offre. Les clients peuvent inclure leur nom pour les évaluations ou les avis, ou publier de manière anonyme.  

> [!NOTE]
> Les clients ne peuvent apporter des modifications qu’à leurs propres évaluations et avis.

Le **tableau de bord des évaluations et avis** affiche les tendances et les insights des activités des clients. Il fournit des représentations graphiques des éléments suivants :

- Évaluations et avis  
- Commentaires d’évaluation

### <a name="marketplace-storefront-tabs"></a>Onglets des vitrines des places de marché

Les éditeurs peuvent voir les métriques de leurs offres séparément dans les onglets AppSource et Place de marché Azure. Pour voir des métriques d’offres spécifiques, sélectionnez **Offre** dans la liste déroulante :

![Liste déroulante des offres du menu Analyser de l’Espace partenaires](./media/analyze-offer-dropdown.png)

### <a name="ratings-and-reviews-summary"></a>Récapitulatif des évaluations et avis

La section Récapitulatif des évaluations et avis montre les métriques ci-dessous pour une plage de dates sélectionnée :

- **Évaluation moyenne :** Évaluation (étoiles) moyenne pondérée de toutes les évaluations soumises par les clients pour l’offre sélectionnée.
- **Détails des évaluations :** Détails des évaluations (étoiles) par le nombre de clients ayant soumis des évaluations. Le graphique à barres est empilé avec des évaluations réelles et révisées (nombre d’évaluations mis à jour).
- **Total des évaluations :** Nombre total des évaluations soumises. Ce nombre comprend également les évaluations avec et sans avis.
- **Évaluations avec avis :** Nombre d’avis soumis.

![Récapitulatif des évaluations et avis du menu Analyser de l’Espace partenaires](./media/analyze-ratings-summary.png)

### <a name="review-comments"></a>Commentaires d’évaluation

Les avis sont affichés dans l’ordre chronologique de leur publication. La vue par défaut montre tous les avis. Vous pouvez filtrer les avis selon l’évaluation (étoiles) à l’aide du **filtre** dans le menu déroulant. Vous pouvez également rechercher les mots clés qui s’affichent dans l’avis.  

![Commentaires d’évaluation du menu Analyser de l’Espace partenaires](./media/analyze-reviews.png)

## <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)

Cette section fournit des réponses aux questions fréquemment posées sur les messages **Aucune analytique disponible pour l’instant** dans l’Espace partenaires.

**Je ne parviens pas à afficher mes données d’analyse dans l’Espace partenaires. Lorsque j’accède à ces pages, le message ci-dessous s’affiche. Pourquoi ?**

![Encore aucunes données pour vos offres](./media/analytics-faq-no-data.png)

Raisons possibles de ce message :

- Vos offres listées dans la Place de marché ne sont pas des offres SaaS. Actuellement, l’Espace partenaires fournit des rapports d’analyse pour les offres SaaS uniquement. Les offres Dynamics 365 peuvent être publiées dans l’Espace partenaires, mais les analyses ne sont pas encore activées pour ces offres dans l’Espace partenaires.
- Aucune acquisition n’existe actuellement pour vos offres publiées dans la Place de marché. Cela peut signifier que vos offres sont en ligne dans la Place de marché et qu’elles sont visionnées par des clients dans les pages d’affichage de produit, mais que les clients n’ont pas encore entrepris d’action pour les acheter et les déployer.
- La publication de votre offre est peut-être encore en cours et pas encore en ligne. Seules les offres en ligne peuvent être acquises par des clients. Pour vérifier le statut de vos offres, consultez Vue d’ensemble dans le [tableau de bord Analyser](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary). Pour plus d’informations, consultez le [tableau de bord Résumé](#summary-dashboard) dans cet article.
- Vos offres peuvent être listées sous **Me contacter**, qui sont des offres à consulter uniquement et qui ne peuvent pas être achetées par les clients dans la Place de marché. Bien que ces offres génèrent des acheteurs et soient partagées avec vous, aucune commande n’est créée pour ces offres car elles ne peuvent pas être achetées. Pour vérifier le type de référencement de vos offres, accédez à la page de configuration.

**Je sais que j’ai des données d’analytique, mais le message ci-dessous s’affiche :**

![Aucune donnée pour la plage de dates donnée](./media/analytics-faq-data-range.png)

Si vous recevez ce message, cela signifie que vous avez des données d’analytique, mais que vous n’avez pas de données pour la plage de dates que vous avez sélectionnée. Sélectionnez une autre plage de dates ou une plage de dates personnalisée pour afficher vos données depuis 2010. Pour plus d’informations, consultez [Plage de dates](#date-range).
