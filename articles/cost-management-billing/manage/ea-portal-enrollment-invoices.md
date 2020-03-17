---
title: Factures d’inscription Azure Entreprise
description: Cet article explique comment gérer et traiter votre facture Azure Enterprise.
author: bandersmsft
ms.author: banders
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 9fbc2ffcf1158f436c060817f8f25152f839c3e0
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037268"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Factures d’inscription Azure Entreprise

Cet article explique comment gérer et traiter votre facture Azure Enterprise Agreement (Azure EA). Il s’agit d’une représentation de votre facture. Vérifiez l’exactitude des informations qu’elle contient. Familiarisez-vous aussi avec d’autres tâches éventuellement nécessaires pour gérer votre facture.

## <a name="change-a-po-number-for-an-overage-invoice"></a>Modifier un numéro de bon de commande pour une facture de dépassement

Le portail Azure Enterprise génère automatiquement un numéro de bon de commande par défaut, sauf si l’administrateur de l’entreprise en définit un avant la date de la facture. L’administrateur de l’entreprise peut également mettre à jour le numéro de bon de commande dans un délai de sept jours après la réception d’un e-mail de notification de facture automatisée.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Pour mettre à jour le numéro de bon de commande des services Azure :

1. Sur le portail Azure Enterprise, sélectionnez **Rapport** > **Résumé de l'utilisation**.
1. Sélectionnez **Edit PO Numbers** (Modifier les numéros de bon de commande) dans le coin supérieur droit.
1. Sélectionnez la case d’option **Services Azure**.
1. Sélectionnez une **Période de facturation** dans le menu déroulant de plages de dates.

   Vous pouvez modifier un numéro de bon de commande pendant sept jours après avoir reçu une notification de facture, mais avant d'avoir payé la facture.
1. Entrez un nouveau numéro de bon de commande dans le champ **PO Number** (Numéro de bon de commande).
1. Cliquez sur **Save** (Enregistrer) pour envoyer votre modification.

### <a name="to-update-the-azure-marketplace-purchase-order-number"></a>Pour mettre à jour le numéro de bon de commande de Place de marché Azure :

1. Sur le portail Azure Enterprise, sélectionnez **Rapport** > **Résumé de l'utilisation**.
1. Sélectionnez **Edit PO Numbers** (Modifier les numéros de bon de commande) dans le coin supérieur droit.
1. Sélectionnez la case d’option **Place de marché**.
1. Sélectionnez une **Période de facturation** dans le menu déroulant de plages de dates.

   Vous pouvez modifier un numéro de bon de commande pendant sept jours après avoir reçu une notification de facture, mais avant d'avoir payé la facture.
1. Entrez un nouveau numéro de bon de commande dans le champ **PO Number** (Numéro de bon de commande).
1. Cliquez sur **Save** (Enregistrer) pour envoyer votre modification.

## <a name="cadence-of-azure-enterprise-billing"></a>Fréquence de facturation d’Azure Enterprise

Chaque année à la date d’effet de l’inscription, Microsoft facture tous les achats avec engagement des services Microsoft Azure. Microsoft vous facturera pour les arriérés de paiement relatifs à toute utilisation au-delà des montants de cet engagement.

- Les frais d’engagement sont établis selon un taux mensuel et facturés une fois par an à l’avance.
- Les frais de dépassement sont calculés chaque mois et facturés en arriérés à la fin de votre période de facturation.

### <a name="billing-intervals"></a>Intervalles de facturation

Votre intervalle de facturation dépend de la manière dont vous choisissez d'effectuer vos achats d'engagement. Votre engagement annuel coïncide avec l'un ou l'autre des termes suivants :

- La date anniversaire de votre inscription
- La date d'entrée en vigueur de votre abonnement d'un an aux avenants.

Vous recevrez votre facture de dépassement en fonction de la date de début et de la configuration de votre inscription :

- **Inscriptions directes avec une date de début antérieure au 1er mai 2018** :
  - Si vous avez conclu un Contrat Entreprise (EA) direct, vous serez facturé selon un cycle de facturation annuel pour les services Azure, à l’exception des services de la Place de marché Azure. Votre cycle de facturation est basé sur la date d'anniversaire, la date d’entrée en vigueur de votre contrat.
  - Si vous dépassez 150 % du seuil de votre engagement financier (Monetary Commitment ou MC) EA, vous basculerez automatiquement vers un cycle de facturation trimestriel basé sur votre date d'anniversaire. Vous recevrez également une facture de dépassement de service Azure.
  - Si vous ne dépassez pas 150 % du seuil de votre engagement financier, votre inscription restera sur un cycle de facturation annuel. Vous recevrez la facture de dépassement à la fin de l'année d'engagement.

- **Inscriptions directes avec une date de début postérieure au 1er mai 2018** :
  - Votre consommation Azure et les frais facturés séparément sont calculés sur un cycle de facturation mensuel.
  - Les frais qui ne sont pas couverts par votre engagement financier sont dus en tant que paiement de dépassement.  

- **Inscriptions indirectes avec une inscription qui a commencé avant le 1er mai 2018** :

  Si vous êtes un client ayant conclu un Contrat Entreprise (EA) dont la date de début est antérieure au 1er mai 2018, vous êtes soumis à un cycle de facturation trimestriel. Le partenaire de canal (CP) vous facture directement.  

- **Inscriptions indirectes avec une date de début postérieure au 1er mai 2018** :

  Vous êtes sur un cycle de facturation mensuel.  

### <a name="increase-your-monetary-commitment"></a>Augmenter votre engagement financier

Vous pouvez augmenter votre engagement à tout moment. Vous serez facturé pour le nombre de mois restant dans la période d'engagement de l’année en cours. Par exemple, si vous vous inscrivez à un avenant d’abonnement d’un an et que vous augmentez votre engagement au cours du sixième six mois, vous serez facturé pour les six mois restants de cette période. Vos quantités d’engagement seront ensuite mises à jour au cours des six derniers mois de votre période d’engagement. Les nouvelles quantités serviront à déterminer les frais de dépassement.

### <a name="overage"></a>Dépassement

Pour le dépassement, vous êtes facturé pour l’utilisation ou les réservations qui ont dépassé votre engagement pendant la période de facturation. Pour afficher une répartition du calcul des quantités de dépassement pour des éléments individuels, consultez le rapport Résumé de l’utilisation ou contactez votre partenaire de canal.

Chaque ligne de la facture affiche les informations suivantes :

- **Montant total** : total des frais
- **Utilisation de l’engagement** : montant de votre engagement utilisé pour couvrir les frais
- **Montant net** : frais qui dépassent votre engagement

Les taxes applicables sont calculées uniquement sur le montant net qui dépasse votre engagement.

La facturation de dépassement est automatisée. Le calendrier des notifications et des factures dépend de la date de fin de votre période de facturation.

- La notification de dépassement est généralement envoyée sept jours après la date de fin de facturation.
- Les factures de dépassement sont envoyées sept à neuf jours après la notification.
- Vous pouvez consulter les frais et mettre à jour les numéros de bon de commande générés par le système pendant les sept jours qui s'écoulent entre la notification du dépassement et la facturation.

### <a name="azure-marketplace"></a>Place de marché Azure

À compter du cycle de facturation d’avril 2019, les clients ont commencé à recevoir une seule facture Azure, car nous regroupons tous les frais liés à Azure et à la Place de marché Azure sur une seule et même facture au lieu de deux factures distinctes. Cette modification n’affecte pas les clients en Australie, au Japon ou à Singapour.

Pendant la transition vers une facture combinée, vous recevrez une facture de la Place de marché Azure partielle. Cette facture distincte finale indiquera les frais liés à la Place de marché Azure avant la date de la mise à jour de facturation. Les frais liés à la Place de marché Azure facturés après cette date apparaissent sur votre facture Azure. Après la période de transition, vous verrez tous les frais liés à Azure et à la Place de marché Azure sur la facture combinée.  

## <a name="adjust-billing-frequency"></a>Ajuster la fréquence de la facturation

La fréquence de la facturation d’un client est annuelle, trimestrielle ou mensuelle. Le cycle de facturation est déterminé au moment où le client signe son contrat. La facturation mensuelle constitue l’intervalle de facturation le plus petit.

- Une **approbation** de l’administrateur d’entreprise est nécessaire pour passer d’un cycle de facturation annuel à un cycle trimestriel pour les inscriptions directes. Une approbation donnée par l’administrateur d’un partenaire est nécessaire pour les inscriptions indirectes. La modification prend effet à la fin du trimestre de facturation en cours.
- Un **avenant** au contrat est nécessaire pour passer d’un cycle de facturation annuel ou trimestriel à un cycle mensuel.  Toute modification apportée au cycle de facturation de l’inscription existante nécessite l’approbation d’un administrateur d’entreprise ou du « Contact facturation ».
- **Envoyez** votre approbation au [support du portail Azure Enterprise](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Sélectionnez la catégorie de problème : **Facturation**.

La modification prend effet à la fin du trimestre de facturation en cours.

Si vous signez un avenant M503, vous pouvez déplacer tout contrat, quelle qu’en soit la fréquence, vers une facturation mensuelle. 

## <a name="credits-and-adjustments"></a>Crédits et ajustements

Vous pouvez consulter tous les crédits ou ajustements appliqués à votre inscription dans la section **Rapports** du portail [ Azure Enterprise](https://ea.azure.com).

Pour afficher les crédits :

1. Dans le [portail Azure Enterprise](https://ea.azure.com), sélectionnez la section **Rapports**.
1. Sélectionnez **Résumé de l’utilisation**.
1. Dans le coin supérieur droit, modifiez l’affichage **M** en **C**.
1. Étendez le champ d’ajustement dans la table d’engagement de service Azure.
1. Vous verrez les crédits appliqués à votre inscription ainsi qu’une courte explication. Par exemple : Crédit de contrat de niveau de service.

## <a name="request-an-invoice-copy"></a>Demander une copie de facture

Pour demander une copie de votre facture, contactez votre partenaire.

## <a name="overage-offset"></a>Décalage de dépassement

Pour pouvoir appliquer votre engagement financier aux dépassements, vous devez répondre aux critères suivants :

- Vous devez avoir des frais de dépassement engagés et qui n’ont pas encore été payés, compris dans un délai d’un an à compter de la date de fin du service facturé.
- Le montant de votre engagement financier disponible doit couvrir le montant total des frais engagés, y compris toutes les factures Azure échues non payées.
- La durée de facturation que vous demandez doit être entièrement clôturée. La facturation est entièrement clôturée après le cinq du mois.
- La période de facturation que vous demandez pour le décalage doit être entièrement clôturée.
- Votre remise ACD (Azure Commitment Discount) se base sur le nouvel engagement réel moins les fonds prévus pour la consommation précédente. Cette exigence s’applique uniquement aux frais de dépassement engagés. Elle n’est valable que pour les services qui consomment un engagement financier et exclut donc les frais relatifs à la Place de marché Azure. Les frais relatifs à la Place de marché Azure sont facturés séparément.

Pour effectuer un décalage de dépassement, vous ou l’équipe en charge des comptes pouvez ouvrir une demande de support. Une approbation par e-mail de la part de l'administrateur de votre entreprise ou du contact facturation est nécessaire.

## <a name="view-price-sheet-information"></a>Voir des informations de grille tarifaire

Les administrateurs d’entreprise peuvent voir les tarifs associés à leur inscription pour les services Azure.

Pour voir la grille tarifaire actuelle :

1. Dans le portail Azure Enterprise, sélectionnez **Rapports**, puis **Grille tarifaire**.
2. Affichez la grille tarifaire ou sélectionnez **Télécharger**.

![Exemple présentant des informations de grille tarifaire](./media/ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

Pour télécharger des tarifs historiques :

1. Dans le portail Azure Enterprise, sélectionnez **Rapports**, puis **Télécharger l'utilisation**.
2. Téléchargez la grille tarifaire.

![Exemple montrant l’emplacement de téléchargement d’une ancienne grille tarifaire](./media/ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

Voici quelques raisons qui expliquent les différences de tarifs :

- Les tarifs ont peut-être changé entre l’inscription précédente et la nouvelle inscription. Des changements de tarifs peuvent survenir du fait qu’ils sont contractuels pendant une inscription spécifique depuis la date de début jusqu’à la date de fin d’un contrat.
- Lorsque vous passez à une nouvelle inscription, la tarification change en fonction du nouvel accord. Le tarif est défini par votre grille tarifaire, et peut être plus élevé dans la nouvelle inscription.
- Si une inscription est prolongée, les tarifs changent aussi. Les prix varient en fonction de ceux du paiement à l’utilisation.

## <a name="request-detailed-usage-information"></a>Demander des informations détaillées sur l’utilisation

Les administrateurs d’entreprise peuvent voir un résumé de leurs données d’utilisation, l’engagement financier consommé et les frais associés à l’utilisation supplémentaire dans le portail Azure Enterprise. Les frais sont présentés au niveau du résumé pour tous les comptes et abonnements.

Pour voir l’utilisation détaillée de comptes spécifiques, téléchargez le rapport de détails de l’utilisation en accédant à **Rapports** > **Télécharger l’utilisation**.

> [!NOTE]
> Le rapport des détails de l’utilisation n’inclut pas les taxes applicables. Une latence pouvant aller jusqu’à huit heures peut être observée entre le moment où l’utilisation est engagée et celui où elle apparaît dans le rapport.

Pour les inscriptions indirectes, votre partenaire doit activer la fonction de balisage pour que vous puissiez voir toutes les informations relatives aux coûts.

## <a name="reports"></a>Rapports

Les administrateurs d’entreprise peuvent voir un résumé de leurs données d’utilisation, l’engagement financier consommé et les frais associés à l’utilisation supplémentaire dans le portail Azure Enterprise. Les frais sont présentés au niveau du résumé pour tous les comptes et abonnements.

### <a name="azure-enterprise-reports"></a>Rapports Azure Enterprise

- Résumé de l’utilisation et graphiques
- Rapport d’utilisation du service
- Rapport de solde et de frais
- Rapport de détails de l’utilisation
- Rapport de frais de la Place de marché Azure
- Price sheet
- Téléchargement des rapports avancés
- Mise en forme du rapport CSV

### <a name="to-view-the-usage-summary-reports-and-graphs"></a>Pour voir les rapports et graphes du résumé de l’utilisation :

1. Accédez au portail Azure Enterprise.
1. Sélectionnez **Rapports** dans le volet de gauche.
1. Sélectionnez l’onglet **Résumé de l’utilisation**.
1. Sélectionnez la période d’engagement dans le menu des plages de dates en haut à gauche.
1. Sélectionnez la période ou le mois sur le graphe pour afficher des détails supplémentaires.
1. Dans cet onglet, vous pouvez :
   - Afficher un graphique montrant l’utilisation par mois, avec le détail de l’engagement, du dépassement des frais de service, des frais facturés séparément et des frais de la Place de marché Azure.
   - Filtrez par services, comptes et abonnements sous le graphe.
   - Basculez entre la **répartition des frais par services** et la **répartition des frais par hiérarchie**.
   - Affichez les détails des services Azure, des frais facturés séparément et des frais de la Place de marché Azure.

## <a name="service-usage-report"></a>Rapport d’utilisation du service

La page du rapport d’utilisation du service permet aux administrateurs d’entreprise d’afficher un résumé de leurs données d’utilisation du service. L’utilisation est présentée au niveau du résumé pour tous les comptes et abonnements. Pour consulter les détails de l'utilisation, vous pouvez filtrer le rapport par comptes ou par abonnements.

> [!NOTE]
> Une latence jusqu’à cinq jours entre la date d’utilisation et le moment où cette utilisation est reflétée dans ce rapport est possible.

### <a name="to-view-the-report"></a>Pour afficher le rapport :

1. Connectez-vous au portail Azure Enterprise.
1. Sélectionnez **Rapports** dans la barre de navigation à gauche.
1. Sélectionnez l’onglet **Résumé de l’utilisation**.
1. Sélectionnez la plage de dates.
1. Choisissez les comptes ou abonnements à afficher.
1. Vous pouvez éventuellement :
   - Basculer entre la vue des **frais par services** et celle des **frais par hiérarchie** pour afficher différentes répartitions.
   - Affichez les détails tels que le nom du service, l’unité de mesure, les unités consommées, le taux effectif et le coût total.

## <a name="download-csv-reports"></a>Télécharger les rapports CSV

La page de téléchargement de rapport mensuel permet aux administrateurs de l’entreprise de télécharger plusieurs rapports dans des fichiers au format .CSV. Les rapports téléchargeables incluent :

- Rapport de solde et de frais
- Rapport de détails de l’utilisation
- Rapport de frais de la Place de marché Azure
- Price sheet

### <a name="to-download-reports"></a>Pour télécharger des rapports :

1. Sur le portail Azure Enterprise, sélectionnez **Rapport**.
1. Sélectionnez **Usage Download** (Téléchargement de l’utilisation) dans le ruban supérieur.
1. Sélectionnez **Télécharger** en regard du rapport mensuel approprié.

### <a name="csv-report-formatting-issues"></a>Problèmes de mise en forme des rapports CSV

Les clients qui consultent les rapports CSV du portail Azure Enterprise en euros peuvent rencontrer des problèmes de mise en forme liés aux virgules et aux points.

Par exemple, vous pouvez voir :

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Heures | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Ce qui suit doit s’afficher :

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Heures | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

Ce problème de mise en forme est dû aux paramètres par défaut de la fonctionnalité d'importation Excel. Excel importe tous les champs en texte « général » et suppose qu’un nombre est séparé dans la norme mathématique. Par exemple : « 1,000.00 ».

Si une devise européenne utilise un point (.) comme séparateur de milliers et une virgule comme séparateur décimal (,), ce nombre s’affichera de manière incorrecte. Par exemple : "1.000,00". Les résultats de l’importation peuvent varier en fonction de votre paramètre de langue régionale.

### <a name="to-import-the-csv-file-without-formatting-issues"></a>Pour importer le fichier CSV sans problèmes de mise en forme :

1. Dans Microsoft Excel, sélectionnez **Fichier** > **Ouvrir**.
   L’Assistant Importation de texte s’affiche.
1. Sous **Type de données d’origine**, choisissez **Délimité**.  La valeur par défaut est **Largeur fixe**.
1. Sélectionnez **Suivant**.
1. Sous Délimiteurs, cochez la case de la **virgule**. Désactivez l’option **Tabulation** si elle est activée.
1. Sélectionnez **Suivant**.
1. Faites défiler jusqu’aux colonnes **ResourceRate** et **ExtendedCost**.
1. Sélectionnez la colonne **ResourceRate**. Elle apparaît en surbrillance en noir.
1. Sous la section **Format des données en colonne**, sélectionnez **Texte** au lieu de **Général**. L'en-tête de la colonne passera de **Général** à **Texte**.
1. Répétez les étapes 8 et 9 pour la colonne **Extended Cost**, puis sélectionnez **Terminer**.

> [!TIP]
> Si Excel est configuré pour ouvrir automatiquement des fichiers CSV, vous devez utiliser la fonction **Ouvrir** dans Excel à la place. Dans Excel, sélectionnez **Fichier** > **Ouvrir**.

## <a name="balance-and-charge-report"></a>Rapport de solde et de frais

Le rapport de solde et de frais fournit un résumé mensuel des informations sur les soldes, nouveaux achats, frais de service de la Place de marché Azure, ajustements et frais de dépassement.

Toutes les lignes du tableau de l’engagement de service Azure restent statiques mois après mois. Vous trouverez les détails de tous les achats et ajustements sous les sections de **détails des nouveaux achats** et de **détails des ajustements**.

### <a name="download-the-balance-and-charge-report"></a>Télécharger le rapport de solde et de frais

1. Connectez-vous au portail Azure Enterprise en tant qu’administrateur d’entreprise.
1. Sélectionnez **Rapports** dans le volet de gauche.
1. Sélectionnez l’onglet **Report Download** (Téléchargement du rapport).
1. Sélectionnez le mois approprié sous la colonne **Balance and Charge** (Solde et frais), puis téléchargez le rapport.

## <a name="usage-detail-report"></a>Rapport de détails de l’utilisation

Le rapport de détails de l’utilisation offre un résumé mensuel de la consommation des services et des quantités d’une inscription. Il comprend des informations sur les noms des compteurs, les types de compteurs et les quantités consommées.

### <a name="download-the-usage-detail-report"></a>Télécharger le rapport de détails de l’utilisation

1. Connectez-vous au portail Azure Enterprise en tant qu’administrateur d’entreprise.
1. Sélectionnez **Rapports** dans la barre de navigation à gauche.
1. Sélectionnez l’onglet **Télécharger l’utilisation**.
1. Sélectionnez le mois approprié sous la colonne **Usage Detail** (Détails de l’utilisation), puis téléchargez le rapport.

## <a name="azure-marketplace-charges-in-azure-enterprise-portal-reports"></a>Frais de la Place de marché Azure dans les rapports Azure Enterprise

Il existe deux types de frais de la Place de marché Azure :

- **Basés sur l’utilisation :** Produits mesurés en unités transactionnelles.  Par exemple, les machines virtuelles sont facturées toutes les heures, les recherches d’API Bing sont facturées par nombre de recherches.
- **Frais mensuels** : Facturation mensuelle basée sur l’utilisation ou l’accès.

Pour plus d'informations sur les frais de la Place de marché Azure, voir les [FAQ sur la Place de marché Azure](https://azure.microsoft.com/marketplace/faq/).

Pour afficher les différents frais dans le portail Azure Enterprise :

- **Rapport de résumé de l’utilisation** : affiche **à la fois** les frais de la Place de marché Azure basés sur l’utilisation et les frais mensuels.
- **Rapport de frais de la Place de marché** : affiche **uniquement** les frais de la Place de marché Azure basés sur l’utilisation.  Les frais à usage unique ne sont pas affichés.

> [!NOTE]
> La Place de marché Azure n’est pas disponible pour les inscriptions MPSA.

## <a name="advanced-report-download"></a>Téléchargement des rapports avancés

Pour la création de rapports sur des plages de dates ou des comptes spécifiques, vous pouvez utiliser le téléchargement de rapports avancés. Depuis le 30 août 2016, le format du fichier de sortie est CSV afin de prendre en charge les jeux d’enregistrements plus volumineux.

1. Dans le portail Azure Enterprise, sélectionnez **Téléchargement des rapports avancés**.
1. Sélectionnez **Appropriate Date Range** (Plage de dates appropriée).
1. Sélectionnez **Appropriate Accounts** (Comptes appropriés).
1. Sélectionnez **Request Usage Data** (Demander les données d’utilisation).
1. Sélectionnez le bouton **Actualiser** jusqu’à ce que l’état du rapport affiche **Télécharger**.
1. Téléchargez le rapport.

## <a name="reporting-for-non-enterprise-administrators"></a>Rapports pour les administrateurs non d’entreprise

Les administrateurs d’entreprise peuvent autoriser les administrateurs de service (DA) et les propriétaires de comptes (AO) à afficher les frais sur une inscription. Les propriétaires de compte disposant d’un accès peuvent télécharger des rapports CSV spécifiques à leur compte et à leurs abonnements. Ils peuvent également consulter les informations visuellement dans la section des rapports du portail Azure Enterprise.

### <a name="to-enable-access"></a>Pour activer l’accès :

 1. Connectez-vous au portail Azure Enterprise en tant qu’administrateur d’entreprise.
 1. Sélectionnez **Gérer** dans le volet de navigation de gauche.
 1. Sélectionnez l’onglet **Inscription**.
 1. Sous la section des **détails de l’inscription**, sélectionnez l’icône du crayon en regard de **DA View Charges** (Afficher les frais de l’administrateur de service) ou de **AO View Charges** (Afficher les frais du propriétaire du compte).
 1. Sélectionnez **Enabled**.
 1. Sélectionnez **Enregistrer**.

### <a name="to-view-reports"></a>Pour afficher des rapports :

1. Connectez-vous au portail Azure Enterprise en tant qu’administrateur de service ou propriétaire du compte.
1. Sélectionnez **Rapports** dans la barre de navigation à gauche.
1. Sélectionnez l’onglet **Résumé de l’utilisation** pour afficher des informations sur le compte et l’abonnement.
1. Sélectionnez **Usage Download** (Téléchargement de l’utilisation) pour afficher les rapports .CSV.

Les administrateurs de service et les propriétaires de comptes ne peuvent pas afficher les frais lors de l’utilisation de la fonction **Téléchargement des rapports avancés**. Les coûts s’affichent sous la forme $0.

Les autorisations accordées au propriétaire du compte pour afficher les frais s’étendent aux propriétaires de comptes et à tous les utilisateurs qui disposent d’autorisations sur les abonnements associés. Si vous êtes un client indirect, les fonctionnalités de coût doivent être activées par votre partenaire de canal.

## <a name="move-usage-data-to-another-enrollment"></a>Déplacer des données d’utilisation vers une autre inscription

Les données d’utilisation sont déplacées uniquement lorsqu’un transfert est antidaté. Il existe deux options pour déplacer des données d’utilisation d’une inscription à une autre :

- Transfert de comptes depuis une inscription vers une autre
- Transferts d’inscriptions depuis une inscription vers une autre

Quelle que soit l’option, vous devez envoyer une [demande de support](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) à l’équipe du support technique EA pour obtenir de l’aide. 

## <a name="azure-ea-pricing-overview"></a>Vue d’ensemble de la tarification d’Azure EA

Cette section fournit des détails sur le mode de calcul de l'utilisation. Elle répond à de nombreuses questions fréquemment posées sur la tarification de divers services Azure dans le cadre d'un Contrat Entreprise où les calculs sont plus complexes.

### <a name="price-protection"></a>Protection des prix

En tant que client ou partenaire de canal, vous êtes assuré de recevoir des prix inférieurs ou égaux à ceux indiqués dans votre grille tarifaire client (CPS) ou aux prix en vigueur à la date d’effet de votre achat Azure. Ce prix est appelé « prix de base ». Pour les services introduits après votre achat Azure, vous serez facturé selon le prix en vigueur au niveau de remise applicable lors de la première mise à disposition du service. Cette protection des prix s’applique pour la durée de votre période d’engagement, qui peut être d’un ou trois ans en fonction de votre Contrat Entreprise.

### <a name="price-changes"></a>Changements de prix

Microsoft peut supprimer le prix actuel du Contrat Entreprise pour les services Azure individuels au cours de la période d’inscription. Nous étendrons ces tarifs réduits aux clients existants alors que le prix inférieur est en vigueur. Si ces tarifs augmentent par la suite, le prix de votre inscription à un service n'augmentera pas au-delà de votre plafond de protection des prix, tel que défini ci-dessus. Toutefois, le tarif pourrait augmenter par rapport au précédent tarif réduit. Dans les deux cas, nous informerons les clients et les partenaires de canaux indirects des changements de prix à venir par l’envoi d’e-mails aux administrateurs d’entreprise et de partenaires associés à l’inscription.

### <a name="current-effective-pricing"></a>Tarification effective actuelle

Les clients et partenaires de canaux peuvent consulter la tarification actuelle d’une inscription en se connectant au [portail Azure Enterprise](https://ea.azure.com/) et en affichant la page de la grille tarifaire de cette inscription. Si vous achetez Azure indirectement par le biais d’un de nos partenaires de canaux, vous devez obtenir vos mises à jour tarifaires auprès de votre partenaire de canal s’il n’a pas activé l’affichage de la tarification de majoration pour votre inscription.

### <a name="introduction-of-new-azure-services"></a>Présentation des nouveaux services Azure

Nous améliorons en permanence Azure et ajoutons régulièrement de nouveaux services qui sont facturés séparément des services existants. Certains services de préversion sont automatiquement disponibles, tandis que d’autres nécessitent une action du client dans le [portail des comptes Azure](https://account.windowsazure.com/PreviewFeatures).

Certains services sont d’abord proposés aux tarifs promotionnels en vigueur lors de la première mise à disposition et peuvent être augmentés à une date ultérieure.

À mesure que les services passent de la préversion à la disponibilité générale, les prix peuvent augmenter à mesure que des contrats de niveau de service de performances et de fiabilité sont appliqués. Une telle augmentation n’est pas contrainte par la protection des prix de base normale. L'utilisation de ces services est facturée au tarif majoré. Pour éviter les frais liés à ces nouveaux services, vous devrez éviter de les utiliser.

### <a name="introduction-of-regional-pricing"></a>Présentation de la tarification régionale

Outre l’introduction de nouveaux services, les services changent également régulièrement d’une base globale unique vers un modèle plus régional à mesure que la prise en charge régionale de ces services évolue.

Lors de la première mise à disposition de la régionalisation d’un service, la protection des prix s’applique à ces nouvelles régions. Toutefois, si d’autres régions sont mises à disposition ultérieurement pour ce même service, elles sont considérées comme de nouveaux services et sont proposées à leurs propres tarifs individuels, indépendamment de la protection des prix de base.

### <a name="enterprise-devtest"></a>Enterprise Dev/Test

Les administrateurs d’entreprise peuvent autoriser les propriétaires de comptes à créer des abonnements basés sur l’offre Enterprise Dev/Test. Le propriétaire du compte doit mettre en place les abonnements Enterprise Dev/Test nécessaires aux abonnés sous-jacents. Cette configuration permet aux abonnés Visual Studio actifs d’exécuter des charges de travail de développement et de test sur Azure à des tarifs Enterprise Dev/Test spéciaux. Pour plus d’informations, consultez [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="enterprise-agreement-usage-calculations"></a>Calculs de l’utilisation dans le cadre d’un Contrat Entreprise

Reportez-vous à [Services Azure](https://azure.microsoft.com/services/) et à [Tarification Azure](https://azure.microsoft.com/pricing/) pour obtenir des informations sur la tarification publique de base, les unités de mesure, la FAQ et des instructions de création de rapports d’utilisation pour chaque service individuel. Vous trouverez de plus amples informations sur les calculs EA dans les sections suivantes.

### <a name="enterprise-agreement-units-of-measure"></a>Unités de mesure du Contrat Entreprise

Les unités de mesure des Contrats Entreprise sont souvent différentes de celles présentées dans nos autres programmes tels que le programme de Contrat d’abonnement à Microsoft Online (MOSA). Cette disparité signifie que, pour un certain nombre de services, l’unité de mesure est agrégée pour fournir la tarification normalisée. L’unité de mesure affichée dans la vue Résumé de l’utilisation du portail Enterprise est toujours la mesure Enterprise. Une liste complète des unités de mesure et des conversions actuelles pour chaque service est fournie avec le fichier Excel des [noms de services conviviaux](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx).

### <a name="rounding-rules"></a>Règles d’arrondi

L’arrondi effectué dans le portail Enterprise utilise la logique d’arrondi bancaire ou d’arrondi Gaussien standard IEEE. Cette logique permet d'arrondir les numéros au chiffre pair le plus proche pour les valeurs à demi-chiffres. La logique d’arrondi au demi supérieur la plus courante consiste toujours à arrondir les demi-chiffres au chiffre le plus élevé supérieur. Cette méthode du portail Azure Enterprise fournit en fait une somme totale plus précise sur le groupe par rapport à la logique Excel standard.

Par exemple, lorsque le premier chiffre à arrondir est 5 et qu'il n'y a pas de chiffres suivants ou que les chiffres suivants sont des zéros, arrondissez au chiffre pair le plus proche. Par exemple : 2,315 et 2,325 arrondis au centième le plus proche deviennent 2,32.

À titre de référence, le tableau suivant présente des formules Excel que vous pouvez utiliser pour modéliser les règles d'arrondi et de conversion du portail Azure Enterprise :

| Scénario | Formule de logique bancaire |
| --- | --- |
| Arrondi de l’utilisation | =MROUND({_source_}, 0.0002) |
| Arrondi de la tarification (2 décimales) | =MROUND({_source_}, 0.02) |
| Arrondi de la tarification (0 décimale) | =MROUND({_source_}, 2) |

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>Conversion entre le rapport de détails de l’utilisation et la page de résumé de l’utilisation

Dans le rapport de téléchargement des données d’utilisation, vous pouvez voir l'utilisation des ressources brutes jusqu'à six décimales. Toutefois, les données d’utilisation affichées dans le portail Azure Enterprise sont arrondies à quatre décimales pour les unités d’engagement et tronquées à zéro décimale pour les unités de dépassement. Les données d'utilisation brutes sont d'abord arrondies à quatre chiffres avant d'être converties en unités utilisées dans le portail Azure Enterprise. Les unités Enterprise converties sont ensuite à nouveau arrondies à quatre chiffres. Vous pouvez afficher les heures consommées réelles avant la conversion dans le rapport de téléchargement de l’utilisation et non dans le portail Azure Enterprise.

Par exemple : Supposons que 694,533404 heures SQL Server réelles sont indiquées dans le rapport de détails de l’utilisation. Ces unités sont converties en unités 6,94533404 de 100 heures de calcul, puis arrondies à 6,9453 pour être affichées dans le portail Azure Enterprise.

- Pour déterminer le montant de facturation total, les unités affichées sont multipliées par le prix unitaire de l’engagement, et le résultat est tronqué à deux décimales. Pour le yen japonais (JPY) et le won coréen (KRW), le montant total est arrondi à zéro décimale.
- Pour calculer le dépassement, les unités de facturation sont tronquées à six chiffres, puis multipliées par le prix unitaire de dépassement pour déterminer le montant de facturation total.
- Pour la facturation du fournisseur de services managés (MSP), toute utilisation associée à un service marqué comme MSP est tronquée à zéro décimale après la conversion en unité de mesure EA. En conséquence, la somme de cette utilisation peut être inférieure à la somme totale de l’utilisation indiquée dans le portail Azure Enterprise. Cela dépend si le MSP se trouve toujours dans le solde de son engagement financier ou s’il est déjà en dépassement.

### <a name="graduated-pricing"></a>Tarification progressive

La tarification Contrat Entreprise n’inclut pas actuellement la tarification progressive, dans laquelle les frais par unité diminuent à mesure que l’utilisation augmente. Lors du passage d’un programme MOSA avec une tarification progressive à un Contrat Entreprise, la tarification est définie en fonction du niveau de base de ce service, moins les ajustements des remises du Contrat Entreprise, le cas échéant.

### <a name="partial-hour-billing"></a>Facturation à l’heure partielle

Toute l’utilisation est facturée sur la base des minutes converties en heures partielles, et non sur la base d’incréments d’heures entières. Les taux horaires Enterprise indiqués sont appliqués au total des heures plus les heures partielles.

### <a name="average-daily-consumption"></a>Consommation quotidienne moyenne

Certains services sont facturés sur une base mensuelle, mais l'utilisation est rapportée sur une base quotidienne. Dans ces cas, cette utilisation est évaluée une fois par jour, divisée par 31 et totalisée sur le nombre de jours de ce mois de facturation. Les tarifs ne sont ainsi jamais plus élevés que prévu pour un mois et qui sont légèrement inférieurs pour les mois comptant moins de 31 jours.

### <a name="compute-hours-conversion"></a>Conversion des heures de calcul

Avant le 28 janvier 2016, toute utilisation de machines virtuelles A0, A2, A3 et A4 Standard et De base et de services cloud était calculée en minutes de machines virtuelles A1. Les machines virtuelles A0 comptaient comme des fractions de minutes de machine virtuelle A1, tandis que les machines virtuelles A2, A3 et A4 étaient converties en multiples. Cette stratégie entraînait une certaine confusion pour nos clients, et nous avons donc implémenté un changement afin d’assigner une utilisation par minute aux mesures dédiées des machines virtuelles A0, A2, A3 et A4.

La nouvelle mesure a pris effet entre le 27 janvier et le 28 janvier 2016. Sur le téléchargement CSV montrant l'utilisation pendant cette période de transition, vous pouvez voir les deux éléments :

- Utilisation générée sur le compteur A1
- Utilisation générée sur le nouveau compteur dédié correspondant à la taille de votre déploiement.

| **Taille du déploiement** | **Utilisation calculée en multiples de A1 jusqu’au 26 janvier 2016** | **Utilisation calculée sur une mesure dédiée à partir du 27 janvier 2016** |
| --- | --- | --- |
| A0 | 0,25 heure de A1 | 1 heure de A0 |
| A2 | 2 heures de A1 | 1 heure de A2 |
| A3 | 4 heures de A1 | 1 heure de A3 |
| A4 | 8 heures de A1 | 1 heure de A4 |

### <a name="regions"></a>Régions

Pour les services où la zone et la région affectent la tarification, consultez le tableau suivant présentant un mappage des zones géographiques et des régions :

| Zone géographique | Régions de transfert de données | Régions CDN |
| --- | --- | --- |
| Zone 1 | Europe septentrionale <br> Europe occidentale <br> USA Ouest <br> USA Est <br> USA Centre Nord <br> USA Centre Sud <br> USA Est 2 <br> USA Centre | Amérique du Nord <br> Europe |
| Zone 2 | Est de l’Asie-Pacifique <br> Sud-est de l’Asie-Pacifique <br> Japon Est <br> OuJapon Est <br> Australie Est <br> Sud-Australie Est | Asie-Pacifique <br> Japon <br> Amérique latine <br> Moyen-Orient / Afrique <br> Australie Est <br> Sud-Australie Est |
| Zone 3 | Brésil Sud |   |

Aucuns frais ne sont facturés pour la sortie de données entre des services hébergés dans le même centre de données. Par exemple, Office 365 et Azure.

### <a name="monetary-commitment-and-unbilled-usage"></a>Engagement financier et utilisation non facturée

L’engagement financier Azure est un montant payé à l’avance pour les services Azure. Il est consommé à fur et à mesure que des services sont utilisés. Les services Azure internes sont facturés en fonction de l’engagement financier. Toutefois, certains frais sont facturés séparément, et les services de la Place de marché Azure ne consomment pas l'engagement financier.

### <a name="charges-billed-separately"></a>Frais facturés séparément

Certains produits et services fournis par des sources tierces ne consomment pas l’engagement financier Azure. Au lieu de cela, ces éléments sont facturés séparément dans le cadre de la facture de dépassement du cycle de facturation standard.

Nous avons regroupé tous les frais liés à Azure et à la Place de marché Azure dans une facture unique qui s’aligne sur le cycle de facturation de l’inscription. La facture combinée ne s’applique pas aux clients en Australie, au Japon ou à Singapour.

La facture combinée indique d'abord l'utilisation Azure, puis les frais éventuels de la Place de marché Azure. Les clients en Australie, au Japon ou à Singapour verront les frais liés à la Place de marché Azure sur une facture distincte.

S’il n’y a pas d’utilisation de dépassement à la fin du cycle de facturation standard, les frais facturés séparément sont facturés séparément. Ce processus s'applique aux clients en Australie, au Japon et à Singapour.

Les services suivants sont facturés séparément :

- Canonical
- Citrix XenApp Essentials
- Utilisateur inscrit de Citrix XenDesktop
- Openlogic
- Utilisateur inscrit de Remote Access Rights XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (mensuel)
- Visual Studio Enterprise (annuel)
- Visual Studio Professional (mensuel)
- Visual Studio Professional (annuel)

## <a name="azure-marketplace-for-ea-customers"></a>Place de marché Azure pour les clients EA

Pour les clients titulaires d’un Contrat Entreprise Direct, les frais de la Place de marché Azure sont visibles sur le portail Azure Enterprise. Les achats et consommations de la Place de marché Azure sont facturés hors engagement financier, tous les trimestres ou tous les mois, et à terme échu.

Les clients indirects peuvent consulter leurs abonnements Place de marché Azure sur la page **Gérer les abonnements** du portail Azure Enterprise, mais la tarification est masquée. Les clients doivent contacter leur fournisseur de services de concession de licences (LSP) pour obtenir des informations sur les frais de la Place de marché Azure.

Les nouveaux achats récurrents mensuels ou annuels de la Place de marché Azure sont facturés en totalité dans la période au cours de laquelle les articles de la Place de marché Azure ont été achetés. Ces articles sont renouvelés automatiquement dans la période suivante, le même jour que l’achat d’origine.

Les frais récurrents mensuels continuent d’être renouvelés le premier jour de chaque mois calendaire. Les frais annuels sont renouvelés à l’anniversaire de la date d’achat.

Certains services de revendeur tiers sur la Place de marché Azure utilisent maintenant votre solde d’engagement financier Contrat Entreprise (EA). Ces services étaient précédemment facturés séparément, c’est-à-dire en dehors de l’engagement financier EA. L’engagement financier EA pour ces services de la Place de marché Azure permet de simplifier la gestion des achats et des paiements clients. Pour obtenir la liste complète des services qui consomment désormais l’engagement financier, consultez la [mise à jour du 6 mars 2018 sur le site web d’Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

### <a name="partners"></a>Partenaires

Les LSP peuvent télécharger une liste de prix spécifique à la Place de marché Azure en accédant à la grille tarifaire dans le portail Azure Enterprise. Sélectionnez le lien vers la **liste des prix de la Place de marché** dans le coin supérieur droit. La liste des prix de la Place de marché Azure répertorie tous les services disponibles et leurs prix.

Pour télécharger la liste de prix :

1. Dans le portail Azure Enterprise, sélectionnez **Rapports** > **Grille tarifaire**.
1. Dans le coin supérieur droit, recherchez le lien vers la liste des prix de la Place de marché Azure sous votre nom d’utilisateur.
1. Cliquez sur le lien avec le bouton droit et sélectionnez **Enregistrer la cible sous**.
1. Dans la fenêtre d’**enregistrement**, remplacez le titre du document par `AzureMarketplacePricelist.zip`, ce qui remplacera le fichier .xlsx par un fichier .zip.
1. Une fois le téléchargement terminé, vous disposez d’un fichier zip avec des listes de prix spécifiques des pays.
1. Les LSP doivent se référer au fichier de pays pour la tarification spécifique du pays. Les LSP peuvent utiliser l'onglet **Notifications** pour connaître les références (SKU) nouvelles ou retirées.
1. Les changements de prix sont peu fréquents. Les LSP sont informés par e-mail des augmentations de prix et des modifications des taux de change 30 jours à l'avance.
1. Les LSP reçoivent une facture par inscription, par ISV et par trimestre.

### <a name="enabling-azure-marketplace-purchases"></a>Activation des achats de la Place de marché Azure

Les administrateurs d’entreprise peuvent désactiver ou activer des achats de la Place de marché Azure pour tous les abonnements Azure dans le cadre de cette inscription. Si l’administrateur d’entreprise désactive les achats et que des abonnements Azure incluent déjà des abonnements de Place de marché Azure, ceux-ci ne sont pas annulés ni affectés.

Bien que les clients puissent convertir leurs abonnements Azure directs en Azure EA en les associant à leur inscription dans le portail Azure Enterprise, cette action ne convertit pas automatiquement les abonnements enfants.

Pour activer les achats de la Place de marché Azure :

1. Connectez-vous au portail Azure Enterprise en tant qu’administrateur d’entreprise.
1. Accédez à **Gérer**.
1. Sous **Détails de l’inscription**, sélectionnez l’icône du crayon en regard de la ligne **Place de marché Azure**.
1. Basculez sur **Activé/désactivé** ou sur **BYOL SKUs Only** (Références SKU BYOL uniquement) en fonction des besoins.
1. Sélectionnez **Enregistrer**.

> [!NOTE]
> BYOL (apportez votre propre licence) et l’option gratuite uniquement limitent l’achat et l’acquisition de références SKU de la Place de marché Azure aux références SKU BYOL et gratuites uniquement.

Obtenez davantage d’informations sur les [frais de la Place de marché Azure dans les rapports du portail Azure Enterprise](#azure-marketplace-charges-in-azure-enterprise-portal-reports).

### <a name="services-billed-hourly-for-azure-ea"></a>Services facturés à l’heure pour Azure EA

Les services suivants sont facturés à l'heure dans le cadre d'un Contrat Entreprise par rapport au tarif mensuel dans MOSP :

- Application Delivery Network (ADN)
- Pare-feu d’applications web

### <a name="azure-remoteapp"></a>Azure RemoteApp

Si vous disposez d’un Contrat Entreprise, vous payez Azure RemoteApp en fonction du niveau de prix de ce Contrat Entreprise. Il n'y a pas de frais supplémentaires. Le prix standard inclut une durée initiale de 40 heures. Le niveau de prix illimité couvre une durée initiale de 80 heures. L’application distante cesse d’émettre de l’utilisation au-delà des 80 heures.

## <a name="azure-marketplace-faq"></a>FAQ Place de marché Azure

Cette section explique comment votre engagement financier Azure peut s'appliquer à certains services de revendeurs tiers sur la Place de marché Azure.

### <a name="what-changed-with-azure-marketplace-services-and-ea-monetary-commitment"></a>Qu'est-ce qui a changé au niveau des services de la Place de marché Azure Marché et de l'engagement financier EA ?

À partir du 1er mars 2018, certains services de revendeurs tiers consomment l'engagement financier (MC) EA. À l'exception des instances de machines virtuelles réservées (RI) Azure, les services étaient auparavant facturés séparément, en dehors de l'engagement financier EA.

Nous avons développé l’engagement financier pour inclure certains des services de la Place de marché Azure publiés par des tiers et les plus fréquemment achetés. L’engagement financier EA pour ces services de la Place de marché Azure permet de simplifier la gestion de vos achats et paiements.

### <a name="why-did-we-make-this-change"></a>Pourquoi avons-nous procédé à ce changement ?

Les clients recherchent continuellement de nouveaux moyens pour tirer parti du paiement initial effectué via un engagement financier. Ce changement a souvent été demandé par les clients, et il a eu un impact sur une grande partie des clients de la Place de marché Azure.

### <a name="how-do-you-benefit"></a>Quels sont les avantages pour vous ?

Vous bénéficiez d’une expérience de facturation plus simple et de la possibilité d’utiliser votre engagement financier EA. Comme ces services sont inclus dans votre engagement financier prépayé, votre engagement financier EA prend de la valeur.

### <a name="what-azure-marketplace-services-use-ea-monetary-commitment-and-how-do-i-know"></a>Comment identifier les services de la Place de marché Azure qui utilisent l'engagement financier EA ?

Lorsque vous achetez un service qui utilise l’engagement financier, la Place de marché Azure affiche une clause de non-responsabilité. Certains services publiés par Red Hat, SUSE, Autodesk et Oracle sont pris en charge. Actuellement, des services portant le même nom et publiés par d'autres parties ne sont pas déduits de l’engagement financier. Une liste complète est disponible à la fin de cette FAQ.

### <a name="what-if-my-ea-monetary-commitment-runs-out"></a>Que se passe-t-il en cas d’utilisation totale de mon engagement financier EA ?

Si vous avez consommé la totalité de votre engagement financier et êtes maintenant en dépassement, les frais liés à ces services apparaissent sur votre prochaine facture de dépassement, avec les autres services de consommation. Avant le changement survenu le 1er mars 2018, ces frais étaient facturés avec d'autres services de la Place de marché Azure.

### <a name="why-dont-all-azure-marketplaces-consume-ea-monetary-commitment"></a>Pourquoi toutes les Places de marché Azure ne consomment-elles pas l'engagement financier EA ?

Nous nous efforçons d’offrir la meilleure expérience client liée à l’engagement financier EA. Cette modification concerne un grand nombre de clients et une part importante du total des dépenses dans la Place de marché Azure. D’autres services peuvent être ajoutés à l’avenir.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Comment cela affecte-t-il l’inscription et les partenaires indirects ?

Il n’y a aucun impact sur nos clients ou partenaires d’inscription indirects. Ces services sont soumis aux mêmes fonctionnalités de majoration partenaire que les autres services de consommation. La seule modification est qu’ils apparaissent sur une facture différente, et que le paiement des frais provient de l'engagement financier EA du client.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-ea-monetary-commitment"></a>Existe-t-il une liste des services de la Place de marché Azure qui consomment l'engagement financier l'EA ?

Des offres spécifiques de la Place de marché Azure peuvent utiliser des fonds d’engagement financier. Consultez la section consacrée aux [services tiers qui utilisent l'engagement financier](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment) pour obtenir une liste complète des produits participant à ce programme.

## <a name="power-bi-reporting"></a>Rapports Power BI

La création de rapports de Power BI est disponible pour les clients EA directs, les partenaires et les clients indirects qui ont accès aux informations de facturation.

### <a name="power-bi-pro"></a>Power BI Pro

Power BI Pro est disponible pour les clients EA. Avec Power BI Pro, vous pouvez générer et partager des rapports pour gérer efficacement vos données de coûts. Vous bénéficiez également de fonctionnalités de collaboration et d’actualisation des données supplémentaires. Power BI Pro offre une capacité de données et des limites de diffusion des données supérieures.

<!--We plan to add new cost management features for Azure Enterprise customers.

Current Power BI (free) users who use the Microsoft Azure Consumption Insights content pack can get a 60-day free trial of Power BI Pro. After the trial is over, you can continue using Power BI Pro by adding a license.

To sign up for the free Power BI Pro trial:

1. From the gear icon in Power BI, select **Manage personal storage**.
1. Select **Try Pro for free** on the right.

See [Power BI self-service sign up](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) for more information on the Power BI Pro free trial.

### Azure EA Power BI Pro trial terms

- **General purpose**: The extended Power BI Pro for the "Microsoft Azure Enterprise" content pack trial offer (the "Offer") is available to existing qualified users during the term of the Offer, to allow them to access certain insights related to their Azure consumption through the use of a specific Power BI content pack.
- **Eligibility**: Users under an Enterprise Agreement (EA) can participate in the Offer if they have a function related to their organization's Azure billing, service, or cost management.
- **Exclusions**:
  - Users already participating in the Extended Power BI Pro trial will continue to qualify under the pre-existing offer and can't enter into the Azure EA Power BI Pro trial offer.
  - Users participating in the Offer can only use Power BI Pro with the Microsoft Azure Enterprise content pack. Any other use of Power BI Pro is prohibited.
  - Term: The Offer began on June 1, 2017 and ended on May 31, 2018.  Acceptance can occur at any time during the 12-month period, though the offer will terminate on May 31, 2018 for all users regardless of when they accepted the Offer.
  -->

### <a name="to-access-microsoft-azure-consumption-insights"></a>Pour accéder à Microsoft Azure Consumption Insights :

1. Accédez à [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
1. Sélectionnez **Obtenir maintenant**.
1. Indiquez un numéro d'inscription et le nombre de mois, puis sélectionnez **Suivant**.
1. Fournissez votre clé d’accès API pour la connexion. Vous pouvez trouver la clé de votre inscription dans le [portail Enterprise](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
1. Sélectionnez **Sign In** (Connexion) pour lancer automatiquement le processus d'importation.
1. Une fois l’opération terminée, un nouveau tableau de bord, un nouveau rapport et un nouveau modèle s’affichent dans le volet de navigation. Sélectionnez le tableau de bord pour afficher vos données importées.

> [!TIP]
>
> - Pour plus d’informations sur la génération de la clé API pour votre inscription, consultez le fichier d’aide des rapports d’API sur le [portail Enterprise](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
> - Pour plus d'informations sur la connexion de Power BI à votre consommation Azure, voir [Microsoft Azure Consumption Insights](/power-bi/desktop-connect-azure-cost-management).

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>Pour accéder au pack de contenu Power BI EA hérité :

1. Rendez-vous sur le [site web de Power BI](https://app.powerbi.com/getdata/services/azure-enterprise).
1. Connectez-vous avec un compte professionnel ou scolaire valide.

   Le compte professionnel ou scolaire peut être identique ou différent de celui que vous utilisez pour accéder à l’inscription via le portail Azure Enterprise.
1. Dans le tableau de bord de services, sélectionnez **Microsoft Azure Enterprise**, puis **Connexion**.
1. Sur l’écran **Se connecter à Azure Enterprise**, renseignez les champs :
    - URL d’environnement Azure : [https://ea.azure.com](https://ea.azure.com/)
    - Nombre de mois : de 1 à 36
    - Numéro d’inscription : entrez votre numéro d’inscription
1. Sélectionnez **Suivant**.
1. Dans la **zone de la clé d’authentification**, entrez la clé API.

    Vous pouvez accéder à la clé API dans le portail Azure Enterprise sous l’onglet **Télécharger l’utilisation**. Sélectionnez **Clé d’accès API**, puis collez la clé dans la zone **Clé du compte**.
1. Le chargement des données dure entre 5 et 30 minutes environ dans Power BI, en fonction de la taille des jeux de données.

## <a name="reports-faq"></a>FAQ sur les rapports

Cette section répond aux questions les plus courantes sur les rapports.

### <a name="why-is-my-cost-showing-as-0"></a>Pourquoi mon coût affiche-t-il $0 ?

Pour les clients avec **inscription directe**, les administrateurs d'entreprise peuvent fournir aux propriétaires de compte et aux administrateurs de service un accès aux informations sur le coût et le prix des rapports d'utilisation. Procédez comme suit :

1. Dans le portail Enterprise, sélectionnez **Gérer** dans le volet de navigation de gauche.
1. Sélectionnez le crayon bleu en regard de l’affichage des frais pour le DA (administrateur de service).
1. Sélectionnez **Activé** et enregistrez.
1. Sélectionnez le crayon bleu en regard de l’affichage des frais pour l’AO (propriétaire du compte).
1. Sélectionnez **Activé** et enregistrez.

> [!NOTE]
> Si vous êtes propriétaire de compte ou administrateur de service, contactez votre administrateur d’entreprise pour activer la fonctionnalité de tarification.

Pour les clients avec **inscription indirecte**, vérifiez auprès de votre partenaire s’il a activé la fonctionnalité de tarification pour vous. Cette opération ne peut être réalisée que par le partenaire. Une fois cette fonctionnalité activée, vous pouvez afficher le coût et le prix de votre inscription en tant qu'administrateur d'entreprise.

Pour les partenaires, si vous souhaitez activer la fonctionnalité d'affichage des frais pour un propriétaire de compte ou un administrateur de service, suivez les étapes de l'**inscription directe**.

### <a name="why-is-there-no-sku-information-on-my-usage-detail-report"></a>Pourquoi mon rapport de détails de l’utilisation ne contient-il aucune information de référence SKU ?

Le rapport de détails de l’utilisation ne contient aucune information de référence SKU. En revanche, étant donné qu’il contient des informations sur l'utilisation, vous pouvez télécharger le rapport de grille tarifaire afin d’obtenir des informations de référence SKU.

### <a name="why-doesnt-the-total-amount-on-azure-marketplace-match-the-reports-for-usage-summary-and-detail"></a>Pourquoi le montant total sur la Place de marché Azure ne correspond-il pas aux rapports pour le résumé et les détails de l'utilisation ?

Le rapport de frais de la Place de marché Azure affiche uniquement les frais basés sur l’utilisation. Les frais à usage unique ne sont pas affichés. Reportez-vous à la page Résumé de l’utilisation pour connaître les utilisations à jour sur les frais basés sur l’utilisation et les frais à usage unique.

### <a name="why-is-there-no-information-on-my-api-report"></a>Pourquoi mon rapport d’API n’affiche-t-il aucune information ?

Les clés API expirent tous les six mois. En cas de problème, un administrateur d'entreprise doit générer une nouvelle clé API. N'oubliez pas de suivre les étapes de la FAQ sur le rapport API.

### <a name="why-isnt-my-power-bi-report-working"></a>Pourquoi mon rapport Power BI ne fonctionne-t-il pas ?

En cas de problème avec Power BI, ouvrez un ticket auprès de l'[équipe de support Power BI](https://support.powerbi.com).

### <a name="why-dont-my-resource-tags-show-on-my-reports"></a>Pourquoi mes balises de ressources ne s'affichent-elles pas sur mes rapports ?

Les balises de ressources sont gérées sur le portail Azure. Vous pouvez contacter l'équipe des abonnements Azure sur le [portail Azure](https://portal.azure.com). Suivez les étapes de l'article [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

### <a name="why-does-my-resource-rate-change-every-day"></a>Pourquoi mon taux de ressource change-t-il tous les jours ?

Le taux de ressource indiqué dans le rapport de détails de l’utilisation est une valeur calculée. Il représente le tarif mensuel moyen qui a été facturé pour le service. Le taux de ressource est calculé à l’aide de la moyenne de votre engagement mensuel et de vos frais de dépassement mensuels pour une unité de service. La partie de l’utilisation facturée par rapport à votre engagement et aux tarifs de dépassement change à chaque fin de journée. Ainsi, le taux de ressource affiché change également au cours du mois. Le taux de ressource est verrouillé le cinquième jour suivant la fin du mois.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Glossaire des processus de calcul du taux de ressource

- **Nombre total d’unités brutes :** Quantité consommée dans le rapport de détails de l’utilisation.
- **Ressource MOCP par unité :** Le système d’utilisation en amont émet les utilisations de chaque service dans des unités différentes. (Préréglage)
- **Consommation par unité :** Unité de mesure Azure Enterprise. (Préréglage)
- **Prix** : Prix unitaire dans le portail Azure Enterprise.
- **Coût total :** Coût total du rapport de détails de l’utilisation, ou utilisation de l’engagement + dépassement dans le portail Azure Enterprise.

### <a name="charges-calculations"></a>Calculs des frais

- **Convertir en ressources MOCP consommées** = `ROUND(Total RAW Units * MOCP Resource Per Unit,4)`
- **Convertir en unités consommées** = `Consumed MOCP Resources / Consumption per Unit`
- **Calculer le coût total** = `Consumed Units * Price`

### <a name="logic-in-the-usage-calculation-logic"></a>Logique de calcul de l'utilisation

**Taux de ressource** = `Total Cost /(Total RAW Units / MOCP Resource Per Unit)`

Le taux de ressource est calculé sur la base de vos frais. Il peut ne pas correspondre au prix unitaire réel figurant dans la grille tarifaire.

Dans le rapport de téléchargement des données d’utilisation, vous pouvez voir l'utilisation des ressources brutes jusqu'à six décimales. Ces données sont utilisées pour le calcul des frais de dépassement. Toutefois, les données d’utilisation affichées dans le portail Azure Enterprise sont arrondies à quatre décimales pour les unités d’engagement et tronquées à zéro décimale pour les unités de dépassement. Sous le portail Azure Enterprise, tous les dépassements d’utilisation sont facturés pour des unités complètes uniquement. Vous risquez de constater une différence importante entre le prix unitaire et le taux de ressource pour l’utilisation facturée en tant que dépassement ou pour plusieurs mois.

## <a name="next-steps"></a>Étapes suivantes

- Les fichiers Excel suivants fournissent des détails sur les services Azure et sont mis à jour le 6 et le 20 de chaque mois :

   | Intitulé | Description | Nom de fichier |
   | --- | --- | --- |
   | [Noms de services conviviaux](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | Répertorie de tous les services actifs, et inclus les informations suivantes : <br>  <ul><li>catégorie de service</li>   <li>nom de service convivial</li>   <li>nom de l’engagement et numéro de référence</li> <li>nom de la consommation et numéro de pièce</li>   <li>unité de mesure</li>   <li>facteurs de conversion entre l'utilisation déclarée et l'utilisation affichée sur le portail Enterprise</li></ul> | Friendly\_Service\_Names.xlsx |
   | [Champs de téléchargement de service](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | Cette feuille de calcul fournit une liste de toutes les combinaisons possibles de champs liés au service dans le rapport de téléchargement de l’utilisation. | Service\_Download\_Fields.xlsx |

- Pour plus d’informations sur la compréhension de votre facture et de vos frais, consultez [Comprendre votre facture Contrat Entreprise Azure](../understand/review-enterprise-agreement-bill.md).
- Pour commencer à utiliser le portail Azure Enterprise, consultez [Bien démarrer avec le portail Azure EA](ea-portal-get-started.md).
