---
title: Analytique pour la place de marché partenaires commerciaux
description: Découvrez comment accéder aux rapports analytiques pour surveiller les ventes, évaluer les performances et optimiser vos offres place de marché.
author: mattwojo
manager: evansma
ms.author: shthota
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/31/2019
ms.openlocfilehash: 0da95dbdca52232b4b24ad921bf2d9c78dc76d56
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247723"
---
# <a name="analytics-for-the-commercial-marketplace-in-partner-center"></a>Analytique pour la place de marché partenaires commerciaux

Découvrez comment accéder aux rapports analytiques dans Microsoft Partner Center pour surveiller les ventes, évaluer les performances et optimiser vos offres dans la place de marché. En tant que partenaire, vous pouvez surveiller vos annonces offre à l’aide de la visualisation des données et les graphiques insight pris en charge par les partenaires et trouver des moyens d’optimiser vos ventes. Les outils d’analytique améliorée permettent d’agir sur les résultats de performances et de mettre à jour de meilleures relations avec vos clients et les revendeurs. 

Pour accéder aux outils d’analytique Partner Center, ouvrez le **[analyser](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** tableau de bord sous la place de marché Commercial.

|**Tableau de bord**|**Contenu affiché**|
|:---|:---|
|[Résumé](#summary-dashboard)|Graphiques, des tendances et des valeurs de données d’agrégation qui résument l’activité de la place de marché pour vos offres|
|[Commandes](#orders-dashboard)|Plus d’informations sur vos commandes dans un format graphique et téléchargeable|
|[Clients](#customer-dashboard)|Informations sur vos clients, y compris les tendances de croissance, présentés sous forme graphique et téléchargeable|
|[Téléchargements](#downloads-dashboard)|Une liste de vos demandes de téléchargement sur les 30 derniers jours|

## <a name="summary-dashboard"></a>Tableau de bord Résumé

Le **Résumé** tableau de bord présente une vue d’ensemble en fonction de chaque type d’offre. **Insights** afficher des informations critiques en un clin de œil et fournissent une vue générale de l’activité de vente de vos offres. Vous pouvez visualiser ces rapports à l’aide de la **Résumé** tableau de bord :

- [Nombre total de commandes](#totals)
- [Nombre total de clients](#totals)
- [Emplacement géographique de vos clients](#customers-by-geography)
- [Les tendances émergentes en fonction de vos informations de customer et order](#growth-trend-chart)
- [Classement des clients avec les commandes la plus élevées](#customer-leaderboard)
- [Nombre de commandes, organisées par nom de l’offre](#offers-by-orders)

### <a name="date-range"></a>Plage de dates

Vous trouverez une sélection de plage de date dans l’angle supérieur droit de chaque page. La sortie de la **Résumé** graphiques de la page peuvent être personnalisés en sélectionnant une plage de dates selon le passé 3, 6 ou 12 mois, ou en sélectionnant une plage de dates personnalisée avec une durée maximale de 12 mois. La plage de dates par défaut se situe six mois.

![Tableau de bord de partenaires analyser](./media/analyze-dashboard.png)

### <a name="totals"></a>Totals

Le **totaux** section affiche le nombre de toutes les commandes créées ou clients acquis, au cours de la plage de dates sélectionnée. 

- La valeur de pourcentage suivant pour **nombre Total de commandes** et **nombre Total de clients** représente la quantité de croissance par rapport au mois précédent. 
- Un triangle vert qui pointe vers le haut indique une tendance positive. Un triangle rouge pointant vers le bas indique une tendance négative par rapport au mois précédent. 
- Ordre et les tendances de croissance de client sont représentées par les graphiques à barres et affichent la valeur pour chaque mois en plaçant le curseur sur les colonnes du graphique.

![Totaux d’analyse de partenaires](./media/analyze-totals.png)

### <a name="customers-by-geography"></a>Clients par zone géographique

Le **clients par zone géographique** carte thermique affiche le nombre de clients sur une carte du monde. 

- Vous pouvez déplacer la carte pour afficher l’emplacement exact. 
- Vous pouvez effectuer un zoom dans un emplacement spécifique. 
- La carte thermique a une grille supplémentaire pour afficher les détails du nombre de clients (ou [commander nombre](#orders-by-geography)) dans l’emplacement spécifique. 
- Vous pouvez rechercher et sélectionner un pays dans la grille pour effectuer un zoom à l’emplacement dans le mappage. Revenir à la vue d’origine en appuyant sur la **accueil** bouton dans le mappage.
- Un **nouveau** le client a acheté un de vos offres pour la première fois au cours du mois dans la plage de dates sélectionnée.

![Zone géographique du client partenaires analyser](./media/analyze-customer-geography.png)

### <a name="growth-trend-chart"></a>Graphique de tendances de croissance

Vous pouvez afficher des tendances reposant sur la croissance de votre **commandes créées** ou **clients acquis**, affichés mois par mois en fonction de la plage de dates sélectionnée. Pour analyser plus ces tendances en sélectionnant les liens sous le graphique, ce qui accédez à respectifs **ordre** ou **client** pages.

![Tendances de croissance partenaires analyser](./media/analyze-growth-trends.png)

### <a name="customer-leaderboard"></a>Classement des clients

Les clients de 50 premières avec le plus grand nombre de commandes sont affichées sur un *classement*, triées par nombre de commandes la plus élevée et le pourcentage de la commande. 

- Sélectionnez un client pour afficher leurs détails du profil, organisés par offre des commandes ou commandes organisés par canal de type et la tarification de licence Azure. 
- Le **offre par des commandes** graphique en anneau présente les offres de quatre principaux (en nombre de commandes) et les autres offre groupée dans en tant que « Rest All ».

> [!NOTE]
> Informations personnelles des clients seront affiche uniquement si le client a fourni le consentement. Vous pouvez afficher ces informations si vous êtes connecté avec un **propriétaire** au niveau des autorisations du rôle. Les utilisateurs avec le **contributeur** rôle ne sera pas en mesure d’afficher ces informations. [En savoir plus sur les rôles d’utilisateur et les autorisations](./manage-account.md#define-user-roles-and-permissions).

![Tendances de croissance partenaires analyser](./media/analyze-growth-trends.png)

### <a name="offers-by-orders"></a>Offres par des commandes

Le **offre par des commandes** graphique organise vos commandes actives en fonction du nom de l’offre. 

- Vous pouvez faire glisser sur les tranches du graphique en anneau de gauche pour le graphique en anneau droite afin d’afficher plus de détails sur l’offre que vous avez fait glisser. Ces deux graphiques permettent de comparer une offre spécifique avec les performances de toutes vos autres offres (« Rest All »). 

![Analyser des offres partenaires par des commandes](./media/analyze-offer-by-order.png)

## <a name="orders-dashboard"></a>Tableau de bord de commandes

Le **commandes** tableau de bord de la **analyser** menu affiche les commandes en cours pour l’ensemble de vos offres de SaaS. Vous pouvez afficher des représentations graphiques de :

- [Total des commandes](#order-totals)
- [Commandes par zone géographique](#orders-by-geography)
- [Tendances pour les commandes actives et a été annulées](#trends-for-active-and-canceled-orders)
- [Commandes organisées par type de licence de place de marché](#orders-by-marketplace-license-type)
- [Commandes organisés par les clients nouveaux et existants](#orders-by-customer-type)
- [Table Order details](#order-details-table)

> [!NOTE]
> Il existe des différences entre comment analytique signale l’affichage dans le portail de partenaire de Cloud (CPP) et le nouveau programme de la place de marché Commercial dans partenaires. Une façon spécifique est que le **Insights de vendeur** dans CPP a un **& utilisation des commandes** onglet, qui affiche des données pour les offres basées sur l’utilisation et non basée sur l’utilisation des offres. Dans le centre de partenaires, les **commandes** page comporte un onglet distinct pour l’offre SaaS.

### <a name="order-totals"></a>Total des commandes

Le **les totaux de commande** section affiche le nombre de toutes les commandes créées, y compris les **Active** et **annulée** orders, au cours sélectionné [plage de dates ](#date-range). 

- La valeur de pourcentage suivant pour **nombre Total de commandes** représente la quantité de croissance par rapport au mois précédent. 
- Un triangle vert qui pointe vers le haut indique une tendance positive. Un triangle rouge pointant vers le bas indique une tendance négative par rapport au mois précédent. 
- Les tendances de croissance sont représentés par les graphiques à barres et affichent la valeur pour chaque mois en plaçant le curseur sur les colonnes du graphique.

### <a name="orders-by-geography"></a>Commandes par zone géographique

Le **commandes par zone géographique** carte thermique affiche le nombre de vos commandes sur une carte du monde et fonctionne comme le  **[clients par carte thermique geography](#customers-by-geography)** .

### <a name="trends-for-active-and-canceled-orders"></a>Tendances pour les commandes actives et a été annulées

Le **actif trie en fonction des offres** graphique du graphique en anneau organise l’ensemble de vos commandes actives en fonction de leurs noms de l’offre.

- Les offres de 4 supérieurs sont affichées dans le graphique et le reste des offres sont considérés comme « Rest All ».
- Vous pouvez sélectionner les offres spécifiques de la légende pour afficher uniquement ces offres dans le graphique. 
- Vous placez le curseur sur une tranche dans le graphique affichera le nombre de commandes et de pourcentage de cette offre par rapport à votre nombre total de commandes sur toutes les offres.
- **Trie par offres tendance** affiche les tendances de croissance du mois par mois. La colonne month représente le nombre de commandes par nom de l’offre. Le graphique en courbes affiche la tendance du pourcentage de croissance tracée sur un axe z.
- Vous pouvez utiliser le curseur en haut du graphique pour faire défiler à droite et gauche le long de l’axe des abscisses et le focus sur les points de données spécifiques.
- Vous pouvez afficher le graphique de tendance en sélectionnant un élément spécifique de la légende.
- Vous pouvez également choisir d’afficher des tendances et des données pour **commandes annulées**. Le graphique fonctionnera de la même façon que les commandes actives.

![Commandes actives analyser de partenaires](./media/analyze-active-orders.png)

### <a name="orders-by-marketplace-license-type"></a>Commandes par type de licence de place de marché
<!-- Section needs review and clarification!  -->
Le **commandes par type de licence de place de marché** graphique affiche un nombre de commandes de mois par mois selon le type de licence et la méthode de facturation de l’offre de la place de marché. Types de licence incluent :

- **Facturée via Azure**: Microsoft facture les clients à votre place lorsque vous choisissez de [vendre votre offre via Microsoft](./create-new-saas-offer.md#sell-through-microsoft) avec ce type de licence. Les types de paiement incluent le paiement à l’utilisation par le biais de carte de crédit ou de la facturation de l’entreprise.
- **Apportez votre propre licence**: Microsoft ne facture pas les clients pour leur utilisation de ce type d’offre place de marché. Répertorié comme **[obtenez-le maintenant (Free)](./create-new-saas-offer.md#get-it-now-free)** dans la place de marché.
- **Gratuit** : Microsoft ne facture pas les clients pour leur utilisation de ce type d’offre place de marché. Répertorié comme **[version d’évaluation gratuite](./create-new-saas-offer.md#free-trial)** dans la place de marché.
- **Microsoft en tant que revendeur**: Représente des offres commercialisées par des revendeurs Microsoft dans le cadre de la  **[programme Cloud Solution Provider (CSP)](./create-new-saas-offer.md#csp-program-opt-in)** .

![Commandes d’analyser de partenaires par type de licence](./media/analyze-license-type.png)

### <a name="orders-by-customer-type"></a>Commandes par type de client

Le **commandes par type de client** graphique à barres affiche le nombre de commandes divisé entre **nouveaux clients** et **les clients existants**. 

- Un **nouveau client** a acquis une ou plusieurs de vos offres pour la première fois dans le même mois calendaire (axe y). Un **Existing client** a acquis précédemment une offre à partir de vous avant le mois calendaire signalé (sur l’axe des y). 
- Un graphique à secteurs supplémentaire représente toutes les commandes créées par le client de nouveau ou existant pour la plage de dates sélectionnée.
- Dans les deux graphiques, vous pouvez choisir Afficher uniquement nouveau ou uniquement les clients existants en sélectionnant la légende respectif.

![Commandes d’analyser de partenaires par type de client](./media/analyze-order-by-customer.png)

### <a name="order-details-table"></a>Table Order details

Le **table Order details** affiche une liste numérotée des commandes top 1000 triés par date d’acquisition.

- Chaque colonne dans la grille est pouvant être trié.
- Les données peuvent être extraites dans un fichier TSV si le nombre d’enregistrements est inférieur à 1 000.
- Si le nombre d’enregistrements supérieure à 1 000, exportés données seront en mode asynchrone placées dans une page de téléchargements pendant 30 jours.
- Filtres peuvent être appliqués à la **table Order details** pour afficher uniquement les données qui vous intéresse. Données peuvent être filtrées par pays, type de licence Azure, type de licence Marketplace, type d’offre, état de la commande, des pistes gratuits, place de marché abonnement ID, Customer ID, et nom de la société.

![Détails de la commande Analyze Partner Center](./media/analyze-order-details.png)

## <a name="customer-dashboard"></a>Tableau de bord client

Le **client** tableau de bord de la **analyser** menu affiche les données pour les clients qui ont acquis vos offres. Vous pouvez afficher des représentations graphiques de :

- [Totaux de client](#customer-totals)
- [Clients par zone géographique](#customers-by-geography)
- [Tendances client présenté](#customer-trends)
- [Clients par des commandes](#customers-by-orders)
- [Table des détails de client](#customer-details-table)

### <a name="customer-totals"></a>Totaux de client

Le **totales des clients** section affiche le nombre de tous les clients, y compris les nouveaux, existants et hautement évolutives, lors de l’élément sélectionné [plage de dates](#date-range).

- Pourcentage de clients en cas de croissance par rapport au mois précédent est indiqué par le nombre et l’indicateur vers le haut dans un indicateur vert ou vers le bas en rouge.
- Les tendances de croissance sont représentés par les graphiques à barres et affichent la valeur pour chaque mois en plaçant le curseur sur les colonnes du graphique.

#### <a name="customer-types"></a>Types de clients

Il existe trois types de client : nouveaux, existants et hautement évolutives. 

- Un nouveau client a acquis une ou plusieurs de vos offres pour la première fois au cours du mois sélectionné.
- Un client existant a acquis une ou plusieurs de vos offres avant le mois sélectionné.
- Un client churned a annulé toutes les offres précédemment achetés.

### <a name="customer-trends"></a>Tendances client présenté

Le **des tendances client présenté** graphique affiche le nombre de tous les clients, y compris les nouveaux, existants et hautement évolutives, avec une tendance de croissance du mois par mois.

- Le graphique en courbes représente les pourcentages de croissance de clients globale. 
- La colonne month représente le nombre de clients à empilés par les clients nouveaux, existants et hautement évolutives.
- Le nombre de clients hautement évolutives s’affiche dans le sens négatif de l’axe des Y.
- Vous pouvez sélectionner légende spécifique pour les éléments affichent plus des vues détaillées. Par exemple, sélectionnez les nouveaux clients à partir de la légende pour afficher uniquement les nouveaux clients.
- Vous pouvez utiliser le curseur en haut du graphique pour faire défiler de droite et gauche sur l’axe des abscisses et vous concentrer sur les points de données spécifiques pour afficher plus en détail.
- Vous placez le curseur sur une colonne du graphique affichera les détails pour le mois uniquement.

![Partenaires analyser des tendances client présenté](./media/analyze-customer-trends.png)

### <a name="customers-by-orders"></a>Clients par des commandes

Le **clients par les commandes** graphique affiche le **haut client centile** sur l’axe x, tel que déterminé par leur nombre de commandes. L’axe y affiche le nombre de commandes du client. L’axe z (graphique linéaire) affiche le pourcentage cumulé du nombre total de commandes. Vous pouvez afficher les détails en pointant sur les points sur le graphique en courbes.

Par exemple, vous pouvez apprendre à partir de ces données de commande client les premiers 30 % de vos clients contribuent à 83 % des commandes, égales à 2,130 orders.

![Commandes des clients partenaires analyser](./media/analyze-customer-orders.png)

### <a name="customer-details-table"></a>Table des détails de client

Le **table des détails de client** affiche une liste numérotée des clients 1000 premières triés par date elles acquis tout d’abord un de vos offres.

- Informations personnelles des clients sera uniquement disponibles si le client a fourni le consentement. Vous pouvez uniquement afficher ces informations si vous êtes connecté avec un **propriétaire** au niveau du rôle d’autorisations. [En savoir plus sur les rôles d’utilisateur et les autorisations](./manage-account.md#define-user-roles-and-permissions).
- Chaque colonne dans la grille est pouvant être trié.
- Les données peuvent être extraites dans un fichier TSV si le nombre d’enregistrements est inférieur à 1 000.
- Si le nombre d’enregistrements supérieure à 1 000, exportés données seront en mode asynchrone placées dans une page de téléchargements pendant 30 jours.
- Filtres peuvent être appliqués à la table pour afficher uniquement les données qui vous intéresse. Les données peuvent être filtrés par nom de la société, ID de client, ID d’abonnement place de marché, Type de licence Azure, acquis de Date, Date perdu, électroniques des clients, pays/état/ville du client/Zip, langue de l’utilisateur, etc.

![Informations sur les clients partenaires analyser](./media/analyze-customer-table.png)

## <a name="downloads-dashboard"></a>Tableau de bord de téléchargements

Le **télécharge** tableau de bord de la **analyser** menu affiche les demandes pour les téléchargements qui contiennent plus de 1000 lignes de données client ou une commande.

Vous recevrez une notification contextuelle et un e-mail contenant un lien vers le **télécharge** chaque fois que vous demandez un téléchargement avec plus de 1000 lignes de données du tableau de bord. Ces téléchargements de données seront disponibles pendant une période de 30 jours et puis supprimé.

![Téléchargements d’analyse de partenaires](./media/analyze-downloads.png)

### <a name="dictionary-of-data-terms"></a>Dictionnaire de termes du contrat de données

| Nom de l'attribut | Rapports | Définition|
|---|---|---|
| Type de licence Azure | Client, commande | Type de contrat de licence utilisé par les clients pour acheter Azure. Également appelé canal |
| Type de licence Azure : Fournisseur de solutions cloud | Client, commande | Le client final se procure Azure et votre offre de la Place de marché par le biais de son fournisseur de solutions cloud, qui agit en tant que revendeur.|
| Type de licence Azure : Entreprise | Client, commande | Le client final se procure Azure et votre offre de la Place de marché par le biais d'un contrat Entreprise, signé directement auprès de Microsoft.|
| Type de licence Azure : Entreprise, via un revendeur  | Client, commande | Le client final procure Azure et votre offre place de marché auprès d’un revendeur qui facilite leur contrat entreprise avec Microsoft.|  |
| Type de licence Azure : Paiement| Client, commande | Le client final procure Azure et votre offre place de marché via un contrat de « Paiement », connecté directement auprès de Microsoft.||
| Nom de l’instance cloud| Ordre| Instance de Microsoft Cloud sur laquelle des machines virtuelles ont été déployées.||
| Nom de l’Instance cloud : Azure Global| Ordre| Le cloud public de Microsoft global.|| |
| Nom de l’Instance cloud : Azure Government | Ordre| Clouds de Microsoft Government spécifiques pour l’une des gouvernements suivantes : Chine, Allemagne ou États-Unis d’Amérique.| |
| Ville du client| Client| Le nom de ville fourni par le client. Ville peut être différente de celui de la ville dans l’abonnement du client Azure.||
| Langue de communication du client  | Client| Langue dans laquelle le client préfère communiquer.||
| Nom de la société du client | Client, commande | Le nom de société fourni par le client. Nom peut être différent de celui de la ville dans l’abonnement du client Azure.|  |
| Pays du client | Client, commande | Le nom du pays fourni par le client. Pays peut être différente de celle du pays dans l’abonnement du client Azure.|  |
| Adresse e-mail du client| Client| L’adresse de messagerie fournie par le client final. E-mail peut être différent de celle de l’adresse de messagerie dans l’abonnement du client Azure.||
| Prénom du client| Client| Le nom fourni par le client. Nom peut être différent du nom fourni dans l’abonnement du client Azure.| |
| ID du client | Client, commande | Identificateur unique affecté à un client. Un client peut avoir zéro ou plusieurs abonnements de la place de marché Azure.|  |
| Code postal du client  | Client| Le code postal fourni par le client. Code peut être différent dans le code postal fourni dans l’abonnement du client Azure.| |
| État du client| Client| L’état (adresse) fourni par le client. État peut être différent de celui le nom fourni dans l’abonnement du client Azure.| |
| Date d'acquisition| Client| La première date le client a acheté une offre publiée par vous.| |
| Date d'annulation| Client| La dernière date le client a annulé la dernière de toutes les offres précédemment acheté.||
| Est le nouveau client  | Ordre| La valeur identifie un nouveau client que l’acquisition d’un ou plusieurs de vos offres pour la première fois (ou pas). Valeur sera « Oui » si, dans le même mois du calendrier pour « Acquérir Date ». Valeur sera « Non » si le client a acheté un de vos offres avant le mois calendaire signalé. |
| Est la version préliminaire de référence (SKU)| Ordre| La valeur vous indiquera si vous avez étiqueté la référence (SKU) en tant que « preview ». Valeur correspond à « Oui » si la référence (SKU) a été marquée en conséquence, les abonnements Azure uniquement autorisés par vous peuvent déployer et utiliser cette image. Valeur sera « Non » si la référence (SKU) n’a pas été identifié comme « preview ».  |
| Promotionnels pour refuser| Client| La valeur vous indiquera si le client proactive opté pour la réception des messages promotionnels provenant d’éditeurs. À ce stade, nous n'offrons pas cette possibilité aux clients. Par conséquent, nous avons indiqué « Non » sur le tableau. Une mise à jour interviendra lorsque cette fonctionnalité sera déployée.|
| Type de licence de la Place de marché| Ordre| Mode de facturation de l’offre de la Place de marché.||
| Type de licence de place de marché : Facturée via Azure| Ordre| Pour cette offre de la Place de marché, Microsoft est votre agent et facture les clients de votre part. (Carte de crédit avec paiement à l’utilisation ou facture d’entreprise)||
| Type de licence de place de marché : Apportez votre propre licence | Ordre| La machine virtuelle nécessite une clé de licence fournie par le client à déployer. Microsoft ne facture pas les clients pour répertorier leurs offres de cette façon via la place de marché.||
| Type de licence de place de marché : Gratuit| Ordre| L’offre est configuré pour être libre de tous les utilisateurs. Microsoft ne facture pas les clients pour leur utilisation de cette offre.||
| Type de licence de place de marché : Microsoft en tant que revendeur  | Ordre| Microsoft est votre revendeur pour cette offre de la Place de marché.|  |
| ID d’abonnement place de marché | Client, commande | L’identificateur unique associé à l’abonnement Azure du client utilisé pour acheter votre offre place de marché. ID se trouvait auparavant le GUID d’abonnement Azure.||
| Nom de l’offre  | Ordre| Le nom de l’offre de la place de marché.|| |
| Type d’offre  | Ordre| Le type d’offre de Microsoft Marketplace.|||
| Type d’offre : Application managée  | commande | Utilisez le type d’offre Application Azure : application managée lorsque les conditions suivantes sont requises : Vous déployez une solution basée sur un abonnement pour votre client à l’aide d’une machine virtuelle ou d’une solution IaaS complète. Vous ou votre client nécessite que la solution être géré par un partenaire. |
| Type d’offre : Azure Application| commande | Utilisez le type d’offre de modèle de solution d’Application Azure lorsque votre solution nécessite une automatisation supplémentaire du déploiement et la configuration au-delà d’une machine virtuelle simple.||
| Type d’offre : Service de conseil| Ordre| Les services de conseil de la Place de marché Azure aident les clients à se connecter aux services pour gérer et étendre leur utilisation d’Azure.| |
| Type d’offre : Conteneur | Ordre| Utilisez le type d’offre Conteneur si votre solution est une image de conteneur Docker approvisionnée en tant que service de conteneur Azure Kubernetes.||
| Type d’offre : Dynamics 365 Business Central| Ordre| Utilisez ce type d’offre lorsque votre solution est intégrée à Dynamics 365 pour Finance and Operations| |
| Type d’offre : Dynamics 365 for Customer Engagement | Ordre| Utilisez ce type d’offre lorsque votre solution est intégrée à Dynamics 365 for Customer Engagement.||
| Type d’offre : Module IoT Edge | Ordre| Modules Azure IoT Edge sont plus petites unités de calcul gérées par IoT Edge et peuvent contenir des services de Microsoft (par exemple, Azure Stream Analytique), 3 rd-party services ou votre propre code spécifique à la solution. |
| Type d’offre : Application Power BI | Ordre| Utilisez le type d’offre d’Application Power BI lorsque vous déployez une application intégrée avec Power BI.|  |
| Type d’offre : Application SaaS| Ordre| Utilisez le type d’offre Applications SaaS pour permettre à votre client d’acheter votre solution technique SaaS sous la forme d’un abonnement.||
| Type d’offre : Machine virtuelle | Ordre| Utilisez le type d’offre Machine virtuelle si vous déployez une appliance virtuelle dans le cadre de l’abonnement associé à votre client.||
| Type d’offre : Extension de la place de marché Visual Studio  | Ordre| Type autrefois d’offre aux développeurs d’extensions Azure DevOps. Va vers l’avant Azure DevOps les développeurs d’extensions peuvent vendre leur extension directement auprès des clients. Offres d’extension peuvent être configurés en tant que payés ou y compris une version d’évaluation. |
| Date d’annulation de la commande| Ordre| Date à laquelle la commande passée sur la Place de marché a été annulée.||
| ID de commande| Ordre| L’identificateur unique de la commande du client pour votre service de la place de marché. Offres basées sur l’utilisation de machines virtuelles ne sont pas associés à une commande.| |
| Date de la commande| Ordre| Date de création de l’ordre de la place de marché.|||
| État de la commande| Ordre| État d’une commande de la Place de marché au moment de la dernière actualisation des données.|     |
| État de la commande : Actif  | Ordre| Le client a acheté une commande et leur ordre n’a pas annulé.|         |
| État de la commande : annulée | Ordre| Le client précédemment acheté un ordre et par la suite annulé leur ordre.||
| E-mail de fournisseur| Client| L’adresse de messagerie du fournisseur impliqué dans la relation entre Microsoft et le client final. Si le client est une entreprise via un revendeur, ce sera le revendeur. Si un fournisseur de solutions Cloud (CSP) est impliqué, ce sera le CSP.|
| Nom du fournisseur| Client| Le nom du fournisseur impliqué dans la relation entre le client de Microsoft et de fin. Si le client est une entreprise via un revendeur, ce sera le revendeur. Si un fournisseur de solutions Cloud (CSP) est impliqué, ce sera le CSP.|
| SKU| Ordre| Nom de référence (SKU) tel que défini lors de la publication. Une offre peut avoir plusieurs références (SKU), mais une référence (SKU) peut uniquement être associé à une offre unique.||
| Date de fin d'essai| Ordre| La date de fin de la période d’essai de cette commande approche ou est dépassée.||