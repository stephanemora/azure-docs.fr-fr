---
title: Factures d’inscription Azure Entreprise
description: Cet article explique comment gérer et traiter votre facture Azure Enterprise.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 15c2e84742ff0f552c688ccf1ba795f742e288ca
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849885"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Factures d’inscription Azure Entreprise

Cet article explique comment gérer et traiter votre facture Azure Enterprise. Votre facture représente ce que vous devez payer, il convient donc d’en vérifier l’exactitude. Familiarisez-vous aussi avec d’autres tâches éventuellement nécessaires pour gérer votre facture.

## <a name="change-a-po-number-for-an-overage-invoice"></a>Modifier un numéro de bon de commande pour une facture de dépassement

Le portail Azure EA génère automatiquement un numéro de bon de commande par défaut, sauf si l’administrateur EA en définit un avant la date de la facture. L’administrateur EA peut également mettre à jour le numéro de bon de commande dans un délai de sept jours après la réception d’un e-mail de notification de facture automatisée.

Un numéro de bon de commande peut également être entré pour un compte ou un abonnement spécifique. Les rapports affichent par défaut le numéro de bon de commande de plus bas niveau défini dans la hiérarchie, ce qui signifie que si aucun numéro de bon de commande d’abonnement n’est entré, le numéro de bon de commande du compte est utilisé et que, si aucun bon de commande de compte n’est entré, le bon de commande du service sera utilisé.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Pour mettre à jour le numéro de bon de commande des services Azure :

1. Dans le portail Azure EA, cliquez sur **Rapports** puis sur **Résumé de l’utilisation**.
1. Sélectionnez **Edit PO Numbers** (Modifier les numéros de bon de commande) dans le coin supérieur droit.
1. Sélectionnez la case d’option **Services Azure**.
1. Sélectionnez une **Période de facturation** dans le menu déroulant de plages de dates. Les numéros de bon de commande peuvent être modifiés jusqu’à sept jours après la notification de la facture ou jusqu’à ce que la facture soit payée, selon la première occurrence.
1. Entrez le nouveau numéro de bon de commande dans le champ  **PO Number**  (Numéro de bon de commande).
1. Cliquez sur  **Enregistrer**  pour soumettre la modification.

### <a name="to-update-the-marketplace-purchase-order-number"></a>Pour mettre à jour le numéro de bon de commande de Place de marché :

1. Dans le portail Azure EA, cliquez sur **Rapports** puis sur **Résumé de l’utilisation**.
1. Sélectionnez **Edit PO Numbers** (Modifier les numéros de bon de commande) dans le coin supérieur droit
1. Sélectionnez la case d’option **Place de marché**
1. Sélectionnez une **Période de facturation** dans le menu déroulant de plages de dates. Les numéros de bon de commande peuvent être modifiés jusqu’à sept jours après la notification de la facture ou jusqu’à ce que la facture soit payée, selon la première occurrence.
1. Entrez le nouveau numéro de bon de commande dans le champ  **PO Number**  (Numéro de bon de commande).
1. Cliquez sur  **Enregistrer**  pour soumettre la modification.

## <a name="cadence-of-azure-ea-billing"></a>Fréquence de facturation d’Azure EA

### <a name="billing-intervals"></a>Intervalles de facturation

Chaque année à la date d’effet de l’inscription, Microsoft facture tous les achats avec engagement des services Microsoft Azure et les arriérés de toute utilisation dépassant les montants de l’engagement. Les frais d’engagement sont établis selon un taux mensuel et facturés une fois par an à l’avance. Les frais de dépassement sont calculés chaque mois et facturés en arriérés à la fin de votre période de facturation.

Selon la manière dont vous choisissez d’effectuer vos achats, votre engagement annuel est pris en compte à la date anniversaire de votre inscription ou à la date d’entrée en vigueur de votre avenant d’abonnement d’un an.

Vous recevrez votre facture de dépassement en fonction de la date de début et de la configuration de votre inscription.

**Inscriptions directes avec une date de début antérieure au 1er mai 2018** : Les clients Azure Enterprise Azure (EA) directs sont configurés selon un cycle de facturation annuel pour les Services Azure (à l’exception des services de la Place de marché). Votre cycle de facturation est basé sur la date anniversaire. La date anniversaire correspond à la date à laquelle votre contrat est entré en vigueur. Pour recevoir la première facture de dépassement de service Azure, vous devez dépasser de 150 % votre seuil d’engagement financier (« MC »).  Lorsque la consommation de service totale dépasse 150 % de votre seuil MC, vous passerez automatiquement à un cycle de facturation trimestriel basé sur la date anniversaire.  Si vous ne dépassez pas 150 % de votre seuil MC, l’inscription reste sur un cycle de facturation annuel et la facture de dépassement est reçue à la fin de l’année de l’engagement.

**Inscriptions directes avec une date de début postérieure au 1er mai 2018** : La consommation d’Azure du client et les frais facturés séparément sont calculés sur un cycle de facturation mensuel.  Les frais qui ne sont pas couverts par l’engagement financier Azure sont dus en tant que paiement de dépassement.  

**Inscriptions indirectes avec une inscription qui a commencé avant le 1er mai 2018** : Les clients Enterprise Azure (EA) indirects sont configurés selon un cycle de facturation trimestriel.  Le partenaire de canal (CP) facture directement le client.  

**Inscriptions indirectes avec une date de début postérieure au 1er mai 2018** : Tout contrat indirect dont la date de début est postérieure ou égale au 1er mai 2018 sera facturé mensuellement.  

### <a name="increasing-commitment"></a>Augmentation de l’engagement

L’engagement peut être augmenté à tout moment et sera facturé pour le nombre de mois restants pour la période d’engagement de cette année. Par exemple, si vous vous inscrivez à un avenant d’abonnement d’un an et que vous augmentez votre engagement au cours du sixième six mois, vous serez facturé pour les six mois restants de cette période. Vos quantités d’engagement seront ensuite mises à jour au cours des six derniers mois de votre période d’engagement pour déterminer les frais de dépassement.

### <a name="overage"></a>Dépassement

Pour le dépassement, vous êtes facturé pour l’utilisation ou les réservations qui ont dépassé votre engagement pendant la période de facturation. Pour afficher une répartition du calcul des quantités de dépassement pour des éléments individuels, consultez le rapport Résumé de l’utilisation ou contactez votre partenaire de canal.

Pour chaque élément de la facture, vous verrez le total des frais (montant total), le montant de l’engagement utilisé pour couvrir les frais (utilisation de l’engagement) et le montant des frais de dépassement de l’engagement (montant net).  Les taxes applicables sont calculées uniquement sur le montant net de dépassement de l’engagement.

La facturation de dépassement est automatisée.  Le calendrier des notifications et des factures dépend de la date de fin de la période de facturation du client.  La notification de dépassement est généralement envoyée sept jours après la date de fin de facturation du client. Les clients peuvent alors accéder au portail, passer en revue leurs frais et mettre à jour les numéros de bon de commande générés par le système (ils peuvent également être mis à jour à tout moment avant la publication du dépassement).  Les factures de dépassement sont envoyées 7 à 9 jours après.

### <a name="azure-marketplace"></a>Place de marché Azure

À compter du cycle de facturation d’avril 2019, les clients commencent à recevoir une seule facture Azure, car nous avons regroupé tous les frais liés à Azure et à la Place de marché Azure sur une seule et même facture au lieu de deux factures distinctes. (Cette modification n’affecte pas les clients en Australie, au Japon ou à Singapour.) Pendant la transition vers une facture consolidée, vous recevrez une facture de la Place de marché partielle. Cette facture distincte finale indiquera les frais liés à la Place de marché avant la date de la mise à jour de facturation. Les frais liés à la Place de marché après cette date apparaissent sur votre facture Azure. Après la période de transition, vous verrez tous les frais liés à Azure et à la Place de marché sur la facture consolidée.  

### <a name="purchase-order-numbers"></a>Numéros de bon de commande

La valeur par défaut de bon de commande est un numéro de bon de commande généré par le système. Les utilisateurs peuvent mettre à jour leur numéro de bon de commande dans le Enterprise Portal en se connectant en tant qu’administrateur d’entreprise et en accédant à la section Rapports. Une zone de numéro de bon de commande apparaît dans le coin supérieur droit de la fenêtre, et permet à l’administrateur d’entreprise de modifier le numéro de bon de commande en cliquant sur l’icône du crayon.

## <a name="adjust-billing-frequency"></a>Ajuster la fréquence de la facturation

La fréquence de la facturation d’un client est annuelle, trimestrielle ou mensuelle. Le cycle de facturation est déterminé au moment où le client signe son contrat. La facturation mensuelle constitue l’intervalle de facturation le plus petit.

Une approbation de l’administrateur d’entreprise est nécessaire pour passer d’un cycle de facturation annuel à un cycle trimestriel pour les inscriptions directes. Une approbation donnée par l’administrateur d’un partenaire est nécessaire pour les inscriptions indirectes. La modification prend effet à la fin du trimestre de facturation en cours.

Pour passer d’un cycle de facturation annuel ou trimestriel à un cycle mensuel, un avenant au contrat est nécessaire.  Toute modification apportée au cycle de facturation de l’inscription existante nécessite l’approbation d’un administrateur d’entreprise ou d’une personne identifiée en tant que _Contact facturation_ dans votre contrat. Vous pouvez soumettre votre approbation au [support Azure EA Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c), puis sélectionner la catégorie de problème **Facturation**.  La modification prend effet à la fin du trimestre de facturation en cours.

Si vous signez un avenant M503, vous pouvez déplacer tout contrat, quelle qu’en soit la fréquence, vers une facturation mensuelle. 

## <a name="credits-and-adjustments"></a>Crédits et ajustements

Tous les crédits ou ajustements appliqués à votre inscription sont visibles sur le site [https://ea.azure.com](https://ea.azure.com) dans la section **Rapports**.

Pour afficher les crédits, procédez comme suit :

1. Sélectionnez la section **Rapports**.
1. Cliquez sur le **Résumé de l’utilisation**.
1. Dans le coin supérieur droit, modifiez l’affichage _M_ en _C_.
1. Étendez le champ d’ajustement dans la table d’engagement de service Azure.
1. Cette ligne affiche les crédits appliqués à votre inscription, ainsi qu’une brève explication, par exemple : Crédit de contrat de niveau de service.

## <a name="request-an-invoice-copy"></a>Demander une copie de facture

Pour demander une copie de votre facture, contactez votre partenaire.

## <a name="overage-offset-by-customers"></a>Décalage de dépassement par les clients

Si le client a des dépassements qu’il souhaite utiliser avec un engagement financier, les critères suivants doivent être respectés :

- Le client doit avoir des frais de dépassement engagés, qu’il n’a pas encore payés, compris dans un délai d’un an à compter de la date de fin du service facturé.
- Le montant de l’engagement financier disponible doit couvrir le montant total des frais engagés, y compris toutes les factures Azure échues non payées.
- La durée de facturation demandée doit être entièrement clôturée. La facturation est entièrement clôturée après le cinq du mois.
- La période de facturation demandée pour le décalage doit être entièrement clôturée.
- La remise ACD se base sur le nouvel engagement réel moins les fonds prévus pour la consommation précédente. Cette exigence s’applique uniquement aux frais de dépassement engagés. Cela ne fonctionne que pour les services qui consomment un engagement financier. Vous ne pouvez donc pas couvrir les frais relatifs au marketplace. Les frais relatifs au marketplace sont facturés séparément.
- Si un client souhaite effectuer un décalage de dépassement, il peut ouvrir une demande de support. Ou l’équipe des comptes peut ouvrir la demande de support. Pour terminer le processus, l’administrateur EA du client ou le contact facturation doit envoyer son approbation par e-mail.

## <a name="view-price-sheet-information"></a>Voir des informations de grille tarifaire

Les administrateurs d’entreprise peuvent voir les tarifs associés à leur inscription pour les services Azure.

Pour voir la grille tarifaire actuelle :

1. Dans le portail Azure EA, cliquez sur **Rapports**, puis sur **Grille tarifaire**.
2. Affichez la grille tarifaire ou cliquez sur **Télécharger**.

![Exemple présentant des informations de grille tarifaire](./media/billing-ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

Pour télécharger des tarifs historiques :

1. Dans le portail Azure EA, cliquez sur **Rapports**, puis sur **Télécharger l’utilisation**.
2. Téléchargez la grille tarifaire.

![Exemple montrant l’emplacement de téléchargement d’une ancienne grille tarifaire](./media/billing-ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

Si vous vous demandez pourquoi certains tarifs diffèrent, voici quelques-unes des raisons :

Les tarifs ont peut-être changé entre l’inscription précédente et la nouvelle inscription. Des changements de tarifs se produisent du fait qu’ils sont contractuels pendant une inscription spécifique depuis la date de début jusqu’à la date de fin d’un contrat. Quand une inscription est transférée vers une nouvelle inscription, elle suit les tarifs d’un nouveau contrat. Les tarifs sont définis par la grille tarifaire du client. Ainsi, les tarifs peuvent être plus élevés dans la nouvelle inscription.

Si une inscription est prolongée, les tarifs changent aussi. Les prix varient en fonction de ceux du paiement à l’utilisation.

## <a name="request-detailed-usage-information"></a>Demander des informations détaillées sur l’utilisation

Les administrateurs d’entreprise peuvent voir un résumé de leurs données d’utilisation, engagement financier consommé et frais associés à l’utilisation supplémentaire dans le portail Azure EA. Les frais sont présentés au niveau du résumé pour tous les comptes et abonnements.

Pour voir l’utilisation détaillée de comptes spécifiques, vous pouvez télécharger le rapport Détails de l’utilisation en accédant à **Rapports** > **Télécharger l’utilisation**. Le rapport n’inclut pas les taxes applicables. Une latence pouvant aller jusqu’à huit heures peut être observée entre le moment où l’utilisation est engagée et celui où elle apparaît dans le rapport.

Pour les inscriptions indirectes, votre partenaire doit activer la fonction de balisage pour que vous puissiez voir toutes les informations relatives aux coûts.

## <a name="reports"></a>Rapports

Les administrateurs d’entreprise peuvent voir un résumé de leurs données d’utilisation, l’engagement financier consommé et les frais associés à l’utilisation supplémentaire dans le Enterprise Portal. Les frais sont présentés au niveau du résumé pour tous les comptes et abonnements.

**Rapports EA**

- Résumé de l’utilisation et graphiques
- Rapport d’utilisation du service
- Rapport de solde et de frais
- Rapport de détails de l’utilisation
- Rapport de frais de la Place de marché
- Price sheet
- Téléchargement des rapports avancés
- Mise en forme du rapport CSV

**Pour afficher les rapports et graphiques de résumé de l’utilisation**

1. Dans le portail Azure EA, cliquez sur **Rapports** dans le volet de navigation de gauche et affichez l’onglet **Résumé de l’utilisation**.
1. Sélectionnez la période d’engagement souhaitée dans le menu déroulant des plages de dates en haut à gauche.
1. Sélectionnez la période ou le mois souhaité sur le graphique pour afficher des détails supplémentaires.
1. Le graphique présente l’utilisation par mois, en détaillant l’engagement utilisé, le dépassement des frais de service, les frais facturés séparément et les frais de la Place de marché.
1. Pour le mois sélectionné, filtrez par services, comptes et abonnements sous le graphe.
1. Basculez entre la répartition des frais par services et la répartition des frais par hiérarchie.
1. Affichez les détails des services Azure, des frais facturés séparément et des frais de la Place de marché Azure.

## <a name="service-usage-report"></a>Rapport d’utilisation du service

La page du rapport d’utilisation du service permet aux administrateurs d’entreprise d’afficher un résumé de leurs données d’utilisation du service. Bien que l’utilisation soit présentée au niveau du résumé pour tous les comptes et abonnements, vous pouvez également filtrer le rapport par comptes ou abonnements pour afficher une utilisation détaillée.

Notez qu’une latence jusqu’à cinq jours entre la date d’utilisation et le moment où cette utilisation est reflétée dans ce rapport est possible.

### <a name="to-view-the-report"></a>Pour afficher le rapport :

1. Connectez-vous au Enterprise Portal.
1. Cliquez sur **Rapports** dans la barre de navigation à gauche.
1. Cliquez sur l’onglet **Résumé de l’utilisation**.
1. Cliquez sur la plage de dates de votre choix.
1. Choisissez les comptes ou abonnements à afficher.
1. Modifiez la vue de frais par services ou de frais par hiérarchie pour afficher différentes répartitions.
1. Affichez les détails, notamment le nom du service, l’unité de mesure, les unités consommées, le taux effectif et le coût total.

## <a name="download-csv-reports"></a>Télécharger les rapports CSV

La page de téléchargement du rapport mensuel permet aux administrateurs d’entreprise de télécharger plusieurs rapports sous forme de fichiers .csv, notamment un rapport de solde et de frais, un rapport détaillé de l’utilisation, un rapport des frais de la Place de marché et une grille tarifaire.

## <a name="to-download-reports"></a>Pour télécharger des rapports :

1. Dans le portail Azure EA, cliquez sur **Rapports**.
1. Sélectionnez **Usage Download** (Téléchargement de l’utilisation) dans le ruban supérieur.
1. Sélectionnez **Télécharger** en regard du rapport mensuel approprié.



## <a name="csv-report-formatting"></a>Mise en forme du rapport CSV

Les clients qui consultent les rapports CSV du portail Azure EA en euros peuvent rencontrer des problèmes de mise en forme liés aux virgules et aux points.

Par exemple, vous pouvez voir :

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Heures | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Ce qui suit doit s’afficher :

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Heures | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

### <a name="root-cause"></a>Cause racine

Excel importe tous les champs en texte « général » et suppose qu’un nombre est séparé dans la norme mathématique : « 1,000.00 ».  Par conséquent, une devise européenne qui utilise un point (.) pour le séparateur de milliers et une virgule pour le séparateur décimal (,) – « 1.000,00 » – s’affichera de manière incorrecte. Cela peut varier en fonction de votre paramètre de langue régionale.

### <a name="when-importing-the-csv-use-the-following-steps"></a>Lorsque vous importez le fichier CSV, procédez comme suit :

1.    Ouvrez Excel, et accédez à Fichier > Ouvrir.
1.    L’Assistant Importation de texte s’affiche.
1.    Sous Type de données d’origine, choisissez _Délimité_.  La valeur par défaut est _Largeur fixe_.
1.    Appuyez sur **Suivant**.
1.    Sous Délimiteurs, cochez la case de la virgule. La valeur par défaut est « Tabulation » (décoché).
1.    Appuyez sur **Suivant**.
1.    Faites défiler jusqu’aux colonnes « ResourceRate » et « ExtendedCost ».
1.    Sélectionnez la colonne « ResourceRate » (elle apparaît en surbrillance en noir).
9.    Sous la section Format des données en colonne, sélectionnez « Texte » au lieu de « Général ».  Vous verrez que l’en-tête de colonne passe de « Général » à « Texte ».
10. Répétez les étapes 8 et 9 pour la colonne « ExtendedCost ». Sélectionnez **Terminer**.

Si Excel est associé pour ouvrir automatiquement des fichiers \*.csv, vous devez utiliser la fonction « Ouvrir » dans Excel à la place. Ouvrez Excel, accédez à Fichier > Ouvrir.

## <a name="balance-and-charge-report"></a>Rapport de solde et de frais

Le rapport de solde et de frais fournit un résumé mensuel des informations sur les soldes, nouveaux achats, frais de service de la Place de marché Azure, ajustements et frais de dépassement. Toutes les lignes du tableau récapitulatif de l’engagement de service Azure restent statiques mois après mois, alors que les détails de tous les achats et ajustements s’affichent sous les sections de détails des nouveaux achats et de détails des ajustements.

### <a name="download-the-balance-and-charge-report"></a>Télécharger le rapport de solde et de frais

1. Connectez-vous au portail Azure EA en tant qu’administrateur d’entreprise.
1. Cliquez sur **Rapports** dans la barre de navigation à gauche.
1. Cliquez sur l’onglet **Report Download** (Téléchargement du rapport).
1. Sélectionnez le mois approprié sous la colonne _Balance and Charge_ (Solde et frais), puis cliquez pour télécharger le rapport.

## <a name="usage-detail-report"></a>Rapport de détails de l’utilisation

Le rapport de détails de l’utilisation fournit un résumé mensuel des services et quantités consommés par une inscription, y compris des informations sur les noms des compteurs, les types de compteurs et les quantités consommées.

### <a name="download-the-usage-detail-report"></a>Télécharger le rapport de détails de l’utilisation

1. Connectez-vous au portail Azure EA en tant qu’administrateur d’entreprise.
1. Cliquez sur **Rapports** dans la barre de navigation à gauche.
1. Cliquez sur l’onglet **Télécharger l’utilisation**.
1. Sélectionnez le mois approprié sous la colonne _Usage Detail_ (Détails de l’utilisation), puis cliquez pour télécharger le rapport.

## <a name="marketplace-charges-in-azure-ea-portal-reports"></a>Frais de la Place de marché dans les rapports Azure EA Portal

Vous trouverez des informations supplémentaires sur les frais de la Place de marché [ici.](https://azure.microsoft.com/marketplace/faq/)

Il existe deux types de frais de la Place de marché :

- **Basés sur l’utilisation :** Produits mesurés en unités transactionnelles.  Par exemple, les machines virtuelles sont facturées toutes les heures, les recherches d’API Bing sont facturées par nombre de recherches.
- **Tarif mensuel** : Facturation mensuelle basée sur l’utilisation/accès.

Pour afficher les différents frais dans le Enterprise Portal :

1. **Rapport de résumé de l’utilisation :** affiche **à la fois** les frais de la Place de marché basés sur l’utilisation et mensuels.
1. **Rapport de frais de la Place de marché :** affiche **uniquement** les frais de la Place de marché basés sur l’utilisation.  Les frais à usage unique ne sont pas affichés.

Notez que la Place de marché Azure n’est pas disponible pour les inscriptions MPSA.

## <a name="advanced-report-download"></a>Téléchargement des rapports avancés

Pour la création de rapports sur des plages de dates ou des comptes spécifiques, le téléchargement de rapports avancés peut être utilisé. Depuis le 30 août 2016, le format du fichier de sortie passe de .xlsx à .csv afin de prendre en charge les jeux d’enregistrements plus volumineux.

1. Sélectionnez **Téléchargement des rapports avancés**.
1. Sélectionnez **Appropriate Date Range** (Plage de dates appropriée).
1. Sélectionnez **Appropriate Accounts** (Comptes appropriés).
1. Sélectionnez **Request Usage Data** (Demander les données d’utilisation).
1. Sélectionnez le bouton **Actualiser** jusqu’à ce que l’état du rapport soit « Télécharger ».
1. Téléchargez le rapport.

## <a name="reporting-for-non-enterprise-administrators"></a>Rapports pour les administrateurs non d’entreprise

Les administrateurs d’entreprise peuvent autoriser l’accès à l’affichage des coûts pour les administrateurs de service (DA) et les propriétaires de comptes (AO) sur une inscription. Une fois activé, un propriétaire de compte peut télécharger des rapports .csv spécifiques à son compte et ses abonnements, et afficher les informations sous la section de rapports du Enterprise Portal.

### <a name="to-enable-access"></a>Pour activer l’accès :

 1. Connectez-vous en tant qu’administrateur d’entreprise.
 1. Cliquez sur **Gérer** dans le volet de navigation de gauche.
 1. Cliquez sur l’onglet **Inscription**.
 1. Sous la section de détails de l’inscription, sélectionnez l’icône du crayon en regard de :
    - Affichage des frais pour l’administrateur de service
    - Affichage des frais pour le propriétaire du compte
 1. Sélectionnez **Enabled**.
 1. Cliquez sur **Enregistrer**.

### <a name="to-view-reports"></a>Pour afficher des rapports :

1. Connectez-vous à Azure EA Portal en tant qu’administrateur de service ou propriétaire du compte.
1. Cliquez sur **Rapports** dans la barre de navigation à gauche.
1. Cliquez sur l’onglet **Résumé de l’utilisation** pour afficher des informations sur le compte et l’abonnement.
1. Cliquez sur **Usage Download** (Téléchargement de l’utilisation) pour afficher les rapports. csv.

Les administrateurs de service et les propriétaires de comptes ne peuvent pas afficher les frais lors de l’utilisation de la fonction _Téléchargement des rapports avancés_. Les coûts s’affichent sous la forme $0.

L’affichage des frais pour le propriétaire du compte s’étend aux propriétaires de comptes et à tous les utilisateurs qui disposent d’autorisations sur les abonnements associés. Si vous êtes un client indirect, les fonctionnalités de coût doivent être activées par votre partenaire de canal.

## <a name="move-usage-data-to-another-enrollment"></a>Déplacer des données d’utilisation vers une autre inscription

Les données d’utilisation sont déplacées uniquement lorsqu’un transfert est antidaté. Il existe deux options pour déplacer des données d’utilisation d’une inscription à une autre :

- Transfert de comptes depuis une inscription vers une autre
- Transfert d’inscriptions depuis une inscription vers une autre

Quelle que soit l’option, vous devez envoyer une [demande de support](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) à l’équipe du support technique EA pour obtenir de l’aide. 

## <a name="azure-ea-pricing-overview"></a>Vue d’ensemble de la tarification d’Azure EA

Ce document fournit des détails sur le calcul de l’utilisation et répond à la plupart des questions les plus fréquemment posées sur la tarification de différents services Azure dans les programmes d’entreprise où les calculs sont plus complexes.

### <a name="price-protection"></a>Protection des prix

Les clients et partenaires de canaux (dans le cas de nos canaux indirects) sont assurés de recevoir des prix inférieurs ou égaux à ceux indiqués dans leur grille tarifaire client (CPS) ou aux prix en vigueur à la date d’effet de leur achat Azure. Ce prix est appelé « prix de base ». Pour les services introduits après cet achat, il s’agit du prix en vigueur au niveau de remise applicable lors de la première mise à disposition du service. Cette protection des prix s’applique pour la durée de la période d’engagement, qui peut être d’un ou trois ans en fonction du programme d’entreprise.

### <a name="price-changes"></a>Changements de prix

Microsoft peut supprimer le prix actuel du programme d’entreprise pour les services Azure individuels au cours de la période d’inscription.  Nous étendrons ces tarifs réduits aux clients existants alors que le prix inférieur est en vigueur.  Si ces tarifs sont augmentés ultérieurement, le prix de l’inscription pour un service n’augmente pas au-delà du plafond de protection des prix du client, tel que défini ci-dessus, mais peut augmenter par rapport au prix réduit antérieur.  Dans les deux cas, Microsoft informera les clients et les partenaires de canaux indirects des changements de prix à venir par l’envoi d’e-mails aux administrateurs d’entreprise et de partenaires associés à l’inscription.

### <a name="current-effective-pricing"></a>Tarification effective actuelle

Les clients et partenaires de canaux peuvent consulter la tarification actuelle d’une inscription en se connectant au [Azure Enterprise Portal](https://ea.azure.com/) et en accédant à la page de la grille tarifaire de cette inscription.  Si vous achetez Azure indirectement par le biais d’un de nos partenaires de canaux, vous devez obtenir vos mises à jour tarifaires auprès de votre partenaire de canal s’il n’a pas activé l’affichage de la tarification de majoration pour votre inscription.

### <a name="introduction-of-new-azure-services"></a>Présentation des nouveaux services Azure

Nous améliorons en permanence Azure et ajoutons régulièrement de nouveaux services qui sont facturés séparément des services existants.  Certains services de préversion sont automatiquement disponibles, tandis que d’autres nécessitent une action du client via le [portail des comptes Azure](https://account.windowsazure.com/PreviewFeatures). Notez également que lorsque les services passent de la préversion à la disponibilité générale, les prix peuvent augmenter à mesure que des contrats de niveau de service de performances et de fiabilité sont appliqués. Enfin, certains services sont proposés aux tarifs promotionnels en vigueur lors de la première mise à disposition, qui peuvent être augmentés à une date ultérieure. Toute augmentation liée au passage de la préversion ou du tarif promotionnel à la disponibilité générale n’est pas contrainte par la protection des prix de base normale et s’applique à l’utilisation ultérieure de ces services. Les clients peuvent éviter les frais liés à ces services en choisissant de ne pas utiliser le nouveau service.

### <a name="introduction-of-regional-pricing"></a>Présentation de la tarification régionale

Outre l’introduction de nouveaux services, les services changent également régulièrement d’une base globale unique vers un modèle plus régional à mesure que la prise en charge régionale de ces services évolue. Lors de la première mise à disposition de la régionalisation d’un service, la protection des prix s’applique à ces nouvelles régions par rapport au prix global antérieur applicable à l’inscription. Toutefois, d’autres régions introduites ultérieurement pour ce même service sont considérées comme de nouveaux services et sont proposées à leurs propres tarifs individuels indépendamment de la protection des prix de base.

### <a name="enterprise-msdn-devtest"></a>Enterprise MSDN Dev/Test

Les administrateurs d’entreprise peuvent autoriser les propriétaires de comptes à créer des abonnements basés sur l’offre EA MSDN Dev/Test. Pour que cela fonctionne correctement, le propriétaire du compte doit configurer les abonnements EA MSDN Dev/Test nécessaires pour les abonnés MSDN sous-jacents. Cela permet aux abonnés MSDN actifs d’exécuter des charges de travail de développement et de test sur Azure à des tarifs de développement/test spéciaux. Pour plus d’informations, consultez [EA MSDN Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="enterprise-program-usage-calculation-guidelines"></a>Instructions de calcul de l’utilisation du programme d’entreprise

Reportez-vous à [Services Azure](https://azure.microsoft.com/services/) et à [Tarification Azure](https://azure.microsoft.com/pricing/) pour obtenir des informations sur la tarification publique de base, les unités de mesure, la FAQ et des instructions de création de rapports d’utilisation pour chaque service individuel. En outre, lors du calcul de l’utilisation des services, vous devez suivre les instructions du programme d’entreprise ci-dessous.

### <a name="enterprise-program-units-of-measure"></a>Unités de mesure du programme d’entreprise

Les unités de mesure des programmes d’entreprise sont souvent différentes de celles présentées dans nos autres programmes tels que le programme de Contrat d’abonnement à Microsoft Online (MOSA). Cela signifie que, pour un certain nombre de services, l’unité de mesure est agrégée pour fournir la tarification normalisée. L’unité de mesure affichée dans la vue Résumé de l’utilisation du Enterprise Portal est toujours la mesure Enterprise. Une liste complète des unités de mesure et des conversions actuelles pour chaque service est fournie avec des [noms de services conviviaux](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx).

### <a name="rounding-rules"></a>Règles d’arrondi

L’arrondi effectué dans le Enterprise Portal utilise la logique d’arrondi bancaire ou d’arrondi Gaussien standard IEEE. Celle-ci arrondit au chiffre pair le plus proche pour les valeurs à demi-chiffre, où les fonctions d’arrondi au demi supérieur les plus courantes arrondissent toujours les demi-chiffres au chiffre le plus élevé supérieur. Cette méthode fournit en fait une somme totale plus précise sur le groupe par rapport à la logique Excel standard.

Lorsque le premier chiffre supprimé est 5 et qu’il n’y a pas de chiffres après, ou que les chiffres suivants sont des zéros, le chiffre impair précédent s’applique (par exemple, arrondissez au chiffre pair le plus proche). Par exemple, 2,315 et 2,325 deviennent 2,32 lorsqu’ils sont arrondis au centième le plus proche.

Pour référence, lorsque vous utilisez Excel pour modéliser les règles d’arrondi et de conversion utilisées dans le Enterprise Portal, les formules MROUND doivent être utilisées comme indiqué ci-dessous.

| Scénario | Formule de logique bancaire |
| --- | --- |
| Arrondi de l’utilisation | =MROUND({_source_}, 0.0002) |
| Arrondi de la tarification (2 décimales) | =MROUND({_source_}, 0.02) |
| Arrondi de la tarification (0 décimale) | =MROUND({_source_}, 2) |

**Tableau** **2** **– Service cloud et conversion d’heures de machine virtuelle**

### <a name="conversion-between-download-usage-file-and-usage-summary-portal-view"></a>Conversion entre l’affichage de téléchargement du fichier d’utilisation et du résumé de l’utilisation du portail

Les données d’utilisation des ressources brute sont rapportées jusqu’à un maximum de six décimales, comme indiqué dans le rapport de téléchargement des données d’utilisation. Toutefois, l’Azure Enterprise Portal Azure arrondit l’utilisation à quatre décimales pour les unités d’engagement et tronque à zéro décimale pour les unités de dépassement. L’utilisation brute est tout d’abord arrondie à quatre chiffres avant la conversion en unité de mesure d’entreprise, et les unités d’entreprise obtenues sont arrondies à quatre chiffres. Les heures consommées réelles avant la conversion sont affichées uniquement dans le rapport de téléchargement de l’utilisation et non dans l’interface utilisateur elle-même.

Par exemple, supposons que 694,533404 heures SQL Server réelles sont indiquées dans le rapport de téléchargement de l’utilisation. Ces unités sont ensuite converties en unités 6,94533404 de 100 heures de calcul, puis arrondies à 6,9453 pour être affichées dans le portail Enterprise.

Ces unités sont ensuite simplement multipliées par le prix unitaire de l’engagement, et le résultat est tronqué à deux décimales pour déterminer le montant de facturation total. Pour le yen japonais (JPY) et le won coréen (KRW), le montant total est arrondi à zéro décimale.

Pour ce même exemple de dépassement, les unités facturables sont tronquées à six, puis multipliées par le prix unitaire de dépassement pour déterminer le montant de facturation total. Pour la facturation du fournisseur de services managés (MSP), toute utilisation associée à un service marqué comme MSP est tronquée à zéro décimale après la conversion en unité de mesure EA comme c’est le cas pour la création de rapports de dépassement. En conséquence, la somme de cette utilisation peut être inférieure à la somme totale de l’utilisation indiquée dans l’interface utilisateur, selon que le MSP se trouve toujours dans le solde de son engagement financier ou qu’il est déjà en dépassement.

### <a name="graduated-pricing"></a>Tarification progressive

La tarification du programme d’entreprise n’inclut pas actuellement la tarification progressive (où la tarification par unité diminue à mesure que l’utilisation augmente). Lors du passage d’un programme MOSA avec une tarification progressive à un programme d’entreprise, la tarification est définie en fonction du niveau de base de ce service, après ajustement des remises du programme d’entreprise, le cas échéant.

### <a name="partial-hour-billing"></a>Facturation à l’heure partielle

Toute l’utilisation est facturée sur la base des minutes converties en heures partielles, à la place d’incréments d’heures entières.  Les tarifs d’entreprise répertoriés sous forme horaire sont simplement appliqués aux heures totales, y compris les heures partielles.

### <a name="average-daily-consumption"></a>Consommation quotidienne moyenne

Lorsqu’un service est facturé sur une base mensuelle mais que l’utilisation est rapportée sur une base quotidienne, cette utilisation est évaluée une fois par jour, divisée par 31 et totalisée sur le nombre de jours de ce mois de facturation. Cela entraîne des tarifs qui ne sont jamais plus élevés que prévu pour un mois et qui sont légèrement inférieurs pour les mois comptant moins de 31 jours.

### <a name="compute-hours-conversion"></a>Conversion des heures de calcul

Précédemment, toute utilisation de machines virtuelles A0, A2, A3 et A4 Standard et De base et de services cloud était calculée en fractions (pour les modèles A0) ou multiples (pour les modèles A2, A3 et A4) de minutes de machines virtuelles A1. Cela constituait une source de confusion pour nos clients. Un changement a ainsi été implémenté afin d’assigner une utilisation par minute aux mesures dédiées des machines virtuelles A0, A2, A3 et A4.

La nouvelle mesure prend effet entre le 27 janvier et le 28 janvier 2016. Dans le téléchargement .csv de votre déploiement pendant cette période de transition, vous remarquerez deux éléments de ligne : Un pour l’utilisation générée sur le compteur A1 et un autre pour l’utilisation générée sur le nouveau compteur dédié correspondant à la taille de votre déploiement.

| **Taille du déploiement** | **Utilisation calculée en multiples de A1 jusqu’au 26 janvier 2016** | **Utilisation calculée sur une mesure dédiée à partir du 27 janvier 2016** |
| --- | --- | --- |
| A0 | 0,25 heure de A1 | 1 heure de A0 |
| A2 | 2 heures de A1 | 1 heure de A2 |
| A3 | 4 heures de A1 | 1 heure de A3 |
| A4 | 8 heures de A1 | 1 heure de A4 |

### <a name="regions"></a>Régions

Pour les services où la zone et la région affectent la tarification, le tableau suivant présente le mappage des zones géographiques et des régions :

| Zone géographique | Régions de transfert de données | Régions CDN |
| --- | --- | --- |
| Zone 1 | Europe septentrionale <br> Europe occidentale <br> USA Ouest <br> USA Est <br> USA Centre Nord <br> USA Centre Sud <br> USA Est 2 <br> USA Centre | Amérique du Nord <br> Europe |
| Zone 2 | Est de l’Asie-Pacifique <br> Sud-est de l’Asie-Pacifique <br> Japon Est <br> OuJapon Est <br> Australie Est <br> Sud-Australie Est | Asie-Pacifique <br> Japon <br> Amérique latine <br> Moyen-Orient / Afrique <br> Australie Est <br> Sud-Australie Est |
| Zone 3 | Brésil Sud |   |

**Tableau** **4** **– Régions de transfert de données**

Aucun frais n’est facturé pour la sortie de données entre des services (par exemple, O365 et Azure) hébergés dans le même centre de données.

### <a name="monetary-commitment-and-unbilled-usage"></a>Engagement financier et utilisation non facturée

L’engagement financier Azure est un montant payé à l’avance pour les services Azure. Il est consommé à fur et à mesure que des services sont utilisés. Les services Azure internes utilisent l’engagement financier. Toutefois, il existe des exceptions pour les frais facturés séparément et les services marketplace.

### <a name="charges-billed-separately"></a>Frais facturés séparément

Certains produits et services fournis par des sources tierces ne consomment pas l’engagement financier Azure. Au lieu de cela, ces éléments sont facturés séparément dans le cadre de la facture de dépassement du cycle de facturation standard.

Nous avons regroupé tous les frais liés à Azure et à la Place de marché dans une facture unique qui s’aligne sur le cycle de facturation de l’inscription. (Cela ne s’applique pas aux clients en Australie, au Japon ou à Singapour.)

La facture consolidée affiche d’abord l’utilisation d’Azure, suivie des frais liés à la Place de marché. Les clients en Australie, au Japon ou à Singapour continuent de voir les frais liés à la Place de marché sur une facture distincte.

S’il n’y a pas d’utilisation de dépassement à la fin du cycle de facturation standard, les frais facturés séparément sont facturés séparément. (Applicable aux clients en Australie, au Japon et à Singapour)

**Les services facturés séparément incluent :**

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

## <a name="azure-marketplace-for-ea-customers"></a>Place de marché Azure pour les clients EA

Pour les clients directs, les frais de la Place de marché sont visibles sur Azure EA Portal. Les achats et consommations de la Place de marché sont facturés hors engagement financier, tous les trimestres/mois et à terme échu.

Les clients indirects peuvent consulter leurs abonnements Place de marché Azure sur la page « Gérer les abonnements » d’Azure EA Portal, mais la tarification est masquée. Les clients doivent contacter leur fournisseur de services de concession de licences (LSP) pour obtenir des informations sur les frais Marketplace.

Les nouveaux achats récurrents mensuels ou annuels de la Place de marché sont facturés en totalité dans la période au cours de laquelle les articles de la Place de marché ont été achetés. Ces articles sont renouvelés automatiquement dans la période suivante, le même jour que l’achat d’origine.

Cela n’affecte pas les frais récurrents existants de la Place de marché. Les frais récurrents mensuels continuent d’être renouvelés le premier jour de chaque mois calendaire, et les frais annuels sont renouvelés à l’anniversaire de la date d’achat.

Certains services de revendeur tiers sur la Place de marché Azure utilisent maintenant votre solde d’engagement financier Contrat Entreprise (EA). Ces services étaient précédemment facturés séparément, c’est-à-dire en dehors de l’engagement financier EA. L’engagement financier EA pour ces services de la Place de marché permet de simplifier la gestion des achats et des paiements clients. Pour obtenir la liste complète des services qui consomment désormais l’engagement financier, consultez le [site web d’Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/?WT.mc_id=azurebg_email_Trans_33771_1695_Release_Direct_Tier2_March14).

### <a name="partners"></a>Partenaires

Les LSP peuvent télécharger une liste de prix spécifique de la Place de marché en accédant à la grille tarifaire dans Azure EA Portal et en cliquant sur le lien **Marketplace Price List** (Liste des prix de la Place de marché) dans le coin supérieur droit. La liste des prix de la Place de marché répertorie tous les services disponibles et leurs prix.

**Pour télécharger la liste des prix, effectuez les étapes suivantes :**

1. Accédez à Rapports > Grille tarifaire.
1. Dans le coin supérieur droit, recherchez le lien vers la liste des prix de la Place de marché Azure sous votre nom d’utilisateur.
1. Cliquez sur le lien avec le bouton droit et sélectionnez **Enregistrer la cible sous**.
1. Dans la fenêtre d’enregistrement, remplacez le titre du document par _AzureMarketplacePricelist.zip_, ce qui remplacera le fichier xlsx par un fichier zip.
1. Une fois le téléchargement terminé, vous disposez d’un fichier zip avec des listes de prix spécifiques des pays.
1. Les LSP doivent se référer au fichier de pays pour la tarification spécifique du pays. Les LSP doivent utiliser l’onglet « Notifications » pour voir les nouvelles références SKU sur la Place de marché, ainsi que les références SKU qui ont été supprimées.
1. Les changements de prix ne sont pas fréquents mais, lorsqu’ils se produisent, les LSP sont informés par e-mail des augmentations de prix et des changements FX 30 jours à l’avance.
1. Les LSP reçoivent une facture par inscription, par ISV et par trimestre.

### <a name="enabling-marketplace-purchases"></a>Activation des achats de la Place de marché

Les administrateurs d’entreprise ont la possibilité de désactiver ou d’activer des achats de la Place de marché pour tous les abonnements Azure dans le cadre de cette inscription. Si l’administrateur d’entreprise désactive les achats et que des abonnements Azure incluent déjà des abonnements de Place de marché, ceux-ci ne sont pas annulés ni affectés.

Bien que les clients puissent convertir leurs abonnements Azure directs en EA en les associant à leur inscription dans Azure EA Portal, cette action ne convertit pas automatiquement les abonnements de Place de marché enfants.

**Pour activer les achats de la Place de marché :**

1. Connectez-vous au portail Azure EA en tant qu’administrateur d’entreprise.
1. Accédez à _Gérer_.
1. Sous _Détails de l’inscription_, cliquez sur l’icône du crayon en regard de la ligne _Place de marché Azure_.
1. Basculez sur _Activé/désactivé_ ou sur _BYOL SKU`s Only\*_ (Références SKU BYOL uniquement) en fonction des besoins.
1. Cliquez sur **Enregistrer**.

### <a name="marketplace-charges-in-azure-ea-portal-reports"></a>Frais de la Place de marché dans les rapports Azure EA Portal

Vous trouverez des informations supplémentaires sur les frais de la Place de marché [ici.](https://azure.microsoft.com/marketplace/faq/)

Il existe deux types de frais de la Place de marché :

- **Basés sur l’utilisation :** Les produits sont mesurés en unités transactionnelles.  Par exemple, les machines virtuelles sont facturées toutes les heures et les recherches d’API Bing sont facturées en fonction du nombre de recherches.
- **Non basés sur l’utilisation :** Frais mensuels à usage unique ou récurrents qui sont indépendants de l’utilisation.

Les frais basés sur l’utilisation et non basés sur l’utilisation sont répertoriés dans le rapport de frais de la Place de marché.

Notez que la Place de marché Azure n’est pas disponible pour les inscriptions MPSA.

\*BYOL (apportez votre propre licence) et l’option gratuite uniquement limitent l’achat et l’acquisition de références SKU de la Place de marché Azure aux références SKU BYOL et gratuites uniquement.

### <a name="services-billed-hourly-for-ea"></a>Services facturés à l’heure pour EA

Application Delivery Network et le pare-feu d’applications web sont facturés à l’heure pour EA par rapport au tarif mensuel dans MOSP.

### <a name="remote-app"></a>Application distante

Les clients EA paient une application distante en fonction de leur niveau de prix EA et ne sont pas facturés en plus. Le prix standard inclut une durée initiale de 40 heures, tandis que le niveau de prix illimité couvre une durée initiale de 80 heures. L’application distante cesse d’émettre de l’utilisation au-delà des 80 heures.

## <a name="azure-marketplace-faq"></a>FAQ de la Place de marché Azure

Ce document FAQ passe en revue les mises à jour apportées à l’application de l’engagement financier d’Azure à certains services tiers publiés dans la Place de marché Azure.

### <a name="what-are-we-changing-with-respect-to-marketplace-services-and-azure-monetary-commitment"></a>Quelles sont les modifications relatives aux services de la Place de marché et à l’engagement financier Azure ?

Outre les instances de machines virtuelles réservées Azure (RI), les clients reçoivent une facture distincte pour tous les services qu’ils achètent sur la Place de marché Azure. Nous développons l’engagement financier Azure pour inclure désormais certains des services de la Place de marché Azure publiés par des tiers qui sont le plus fréquemment achetés par nos clients.

### <a name="why-are-we-making-this-change"></a>Pourquoi apportons-nous cette modification ?

Les clients recherchent continuellement des nouveaux moyens pour tirer parti du paiement initial qu’ils ont effectué sous la forme d’un engagement financier Azure.  Nous répondons à une demande fréquente des clients et touchons une grande partie de nos clients de la Place de marché Azure en étendant l’engagement financier à ces services.

### <a name="what-is-the-customer-benefit"></a>Quel est l’avantage pour le client ?

Les clients bénéficient d’une expérience de facturation plus simple et de la possibilité d’utiliser leur engagement financier Azure.  L’ajout de cet avantage à l’engagement financier prépayé et aux RI à l’aide de l’engagement financier ajoute davantage de valeur à l’engagement financier Azure.

### <a name="what-services-will-deduct-from-azure-monetary-commitment-and-how-will-my-customer-know"></a>Quels services seront déduits de l’engagement financier Azure et comment mes clients le sauront-ils ?

Pendant l’expérience d’achat de la Place de marché Azure, nous distinguerons chaque service qui utilisera l’engagement financier à l’aide d’une exclusion de responsabilité. Les serveurs de publication actuellement pris en charge incluent certains services publiés par Red Hat, SUSE, Autodesk et Oracle. Les services qui ont des conventions d’affectation de noms similaires mais qui sont publiés par d’autres parties non identifiées ci-dessus ne seront pas déduits de l’engagement financier. Une liste complète est disponible à la fin de cette FAQ.

### <a name="what-if-my-customer-runs-out-of-monetary-commitment"></a>Que se passe-t-il si mon client vient à manquer d’engagement financier ?

Pour les clients qui ont consommé la totalité de leur engagement financier et qui sont désormais en dépassement, les frais liés à ces services apparaissent sur la facture de dépassement suivante, avec les autres services de consommation.  Il s’agit d’une modification, car ces frais auraient été facturés sur leur propre facture avec d’autres offres de la Place de marché Azure.

### <a name="why-are-we-not-enabling-azure-monetary-commitment-for-all-marketplace-purchases"></a>Pourquoi n’activons-nous pas l’engagement financier Azure pour tous les achats de la Place de marché ?

Nous nous efforçons d’offrir la meilleure expérience client liée à l’engagement financier Azure. Cette modification concernera un grand nombre de clients et à une part importante du total des dépenses dans la Place de marché Azure. D’autres services peuvent être ajoutés à l’avenir.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Comment cela affecte-t-il l’inscription et les partenaires indirects ?

Il n’y a aucun impact sur nos clients ou partenaires d’inscription indirects. Ces services sont soumis aux mêmes fonctionnalités de majoration partenaire que les autres services de consommation. La seule modification sera la facture sur laquelle ils apparaissent et le paiement des frais liés à l’engagement financier.

### <a name="list-of-services-that-will-deduct-from-azure-monetary-commitment"></a>Liste des services qui seront déduits de l’engagement financier Azure

Des offres de la Place de marché Azure spécifiques peuvent utiliser des fonds d’engagement financier. Pour la liste complète des produits inclus dans ce programme, consultez [Engagement monétaire Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment).

## <a name="additional-information"></a>Informations supplémentaires

Pour plus d’informations, consultez ces sources d’informations supplémentaires. Ces fichiers sont mis à jour deux fois par mois, le 6 et le 20 de chaque mois. Les détails de chaque fichier figurent ci-dessous :

| Titre de l’annexe | Description | Convention d’affectation de noms d’URL |
| --- | --- | --- |
| [**Noms de services conviviaux**](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | Fournit une liste de tous les services actifs avec la catégorie de service, le nom de service convivial, le nom et le numéro de référence de l’engagement, le nom et le numéro de référence de la consommation, les unités de mesure et les facteurs de conversion entre l’utilisation rapportée et l’utilisation affichée dans l’Enterprise Portal. | Friendly\_Service\_Names.xlsx |
| [**Champs de téléchargement de service**](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | Cette feuille de calcul fournit une liste de toutes les combinaisons possibles de champs liés au service dans le rapport de téléchargement de l’utilisation. | Service\_Download\_Fields.xlsx |

**Tableau** **5** **– Sources d’informations supplémentaires**

## <a name="power-bi-reporting"></a>Rapports Power BI

### <a name="power-bi-pro"></a>Power BI Pro

Power BI Pro est désormais disponible pour les clients EA. Avec Power BI Pro, vous pouvez générer et partager des rapports pour gérer efficacement vos données de coûts, avec des fonctionnalités de collaboration et d’actualisation des données supplémentaires. Power BI Pro offre une capacité de données et des limites de diffusion des données supérieures. De nouvelles fonctionnalités passionnantes de gestion des coûts pour les clients Azure Enterprise seront bientôt disponibles.

Les utilisateurs actuels de la version gratuite de Power BI utilisant le pack de contenu Microsoft Azure Consumption Insights peuvent bénéficier d’une version d’évaluation gratuite de 60 jours de Power BI Pro. Si vous souhaitez continuer à utiliser Power BI Pro après l’évaluation gratuite, vous pouvez le faire via l’ajout d’une licence.

Pour vous inscrire à la version d’évaluation gratuite, accédez à l’icône d’engrenage et sélectionnez **Gérer le stockage personnel**. Sélectionnez ensuite **Essayez gratuitement la version Pro** à droite. Pour plus d’informations sur l’évaluation gratuite de Power BI Pro, consultez [Inscription en libre-service de Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial).

### <a name="microsoft-azure-ea-power-bi-pro-trial-terms"></a>Conditions d’évaluation gratuite de Microsoft Azure EA Power BI Pro

- **Usage général** : Power BI Pro étendu pour l’offre d’évaluation gratuite du pack de contenu « Microsoft Azure Enterprise » (l’« Offre ») est disponible pour les utilisateurs qualifiés existants pendant la période de l’Offre, afin de leur permettre d’accéder à certaines informations relatives à leur consommation Microsoft Azure via un pack de contenu Power BI spécifique.
- **Éligibilité** : Les utilisateurs d’un Contrat Entreprise (EA) peuvent bénéficier de l’Offre s’ils occupent une fonction en lien avec la facturation, le service, la gestion des services et/ou des coûts Microsoft Azure de leur organisation.
- **Exclusions** :
  - Les utilisateurs qui disposent déjà de la version d’évaluation gratuite de Power BI Pro étendu continueront à bénéficier de l’offre préexistante et ne peuvent pas bénéficier de l’offre d’évaluation gratuite Azure EA Power BI Pro.
  - Les utilisateurs bénéficiant de l’offre peuvent uniquement utiliser Power BI Pro avec le pack de contenu Microsoft Azure Enterprise. Toute autre utilisation de Power BI Pro est interdite.
  - Terme : L’Offre commence le 1er juin 2017 et se termine le 31 mai 2018.  L’acceptation peut se faire à tout moment pendant la période de 12 mois, bien que l’offre se termine le 31 mai 2018 pour tous les utilisateurs, quel que soit le moment où ils ont accepté l’Offre.

### <a name="to-access-the-microsoft-azure-consumption-insights-content-pack"></a>Pour accéder au pack de contenu Microsoft Azure Consumption Insights :

1. Accédez à [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Cliquez sur **Obtenir maintenant**.
1. Indiquez le numéro d’inscription et le nombre de mois. Cliquez sur **Suivant**.
1. Fournissez votre clé d’accès API pour la connexion. Vous pouvez trouver la clé de votre inscription dans le [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Cliquez sur **Se connecter**.
1. Le processus d’importation démarre automatiquement. Une fois l’opération terminée, un nouveau tableau de bord, un nouveau rapport et un nouveau modèle s’affichent dans le volet de navigation. Cliquez sur le tableau de bord pour afficher vos données importées.

Pour plus d’informations sur la génération de la clé API pour votre inscription, consultez le fichier d’aide des rapports d’API sur le [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Pour plus d’informations sur le nouveau pack de contenu, téléchargez le document [Microsoft Azure Consumption Insights](https://automaticbillingspec.blob.core.windows.net/spec/Microsoft%20Azure%20Consumption%20Insights.docx?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>Pour accéder au pack de contenu Power BI EA hérité :

 1. Accédez au [site web de Power BI](https://app.powerbi.com/getdata/services/azure-enterprise).
 1. Connectez-vous avec un compte professionnel ou scolaire valide.
    - Le compte professionnel ou scolaire peut être identique ou différent de celui utilisé pour accéder à l’inscription via le portail Azure EA.
 1. Dans le tableau de bord de services, sélectionnez **Microsoft Azure Enterprise** et cliquez sur **Connexion**.
 1. Sur l’écran « Se connecter à Azure Enterprise », choisissez :
    - URL d’environnement Azure : [https://ea.azure.com](https://ea.azure.com/).
    - Nombre de mois : choisissez entre 1 et 36.
    - Numéro d’inscription : entrez le numéro d’inscription.
    - Cliquez sur **Suivant**.
 1. Dans la zone de la clé d’authentification, entrez la clé API. Vous pouvez accéder à la clé API ici dans le portail Azure Enterprise, sous l’onglet « Télécharger l’utilisation » ci-dessus. Cliquez sur « Clé d’accès API »
    - Copiez et collez la clé dans la zone de la « Clé de compte »
 1. Le chargement des données dure entre 5 et 30 minutes environ dans Power BI, en fonction de la taille des jeux de données.

La création de rapports de Power BI est disponible pour les clients EA directs, les partenaires et les clients indirects qui ont accès aux informations de facturation.

## <a name="report-faq"></a>FAQ sur les rapports

Cette section de l’article répond aux questions courantes relatives à l’interprétation des rapports.

### <a name="why-is-my-cost-showing-as-0"></a>Pourquoi mon coût affiche-t-il $0 ?

**Inscription directe** : si vous êtes propriétaire de compte ou administrateur de service, contactez votre administrateur EA pour activer la fonctionnalité de tarification :

1. Cliquez sur **Gérer** dans le volet de navigation de gauche.
1. Cliquez sur le crayon bleu en regard de l’affichage des frais pour le DA (administrateur de service).
1. Sélectionnez **Activé** et enregistrez.
1. Cliquez sur le crayon bleu en regard de l’affichage des frais pour l’AO (propriétaire du compte).
1. Sélectionnez **Activé** et enregistrez.

Cette action permet aux propriétaires de comptes et aux administrateurs de service d’accéder aux informations de coût/tarification sur les rapports d’utilisation.

**Inscription indirecte** : veuillez vérifier auprès de votre partenaire s’il a activé la fonctionnalité de tarification pour vous. Cette opération ne peut être effectuée que par le partenaire et une fois qu’il a activé la fonctionnalité, vous pouvez afficher le coût et la tarification de votre inscription en tant qu’administrateur EA.

Si vous souhaitez activer la fonctionnalité d’affichage des frais pour vos propriétaires de comptes et administrateur de service, suivez les étapes ci-dessus sous **Inscription directe**.

### <a name="there-is-no-sku-information-on-the-usage-detail-report"></a>Il n’existe pas aucune information de référence SKU dans le rapport de détails de l’utilisation

Le rapport de détails de l’utilisation ne contient aucune information de référence SKU. Vous pourrez toutefois afficher les informations de service utilisées dans le rapport. Vous pouvez ensuite télécharger le rapport de grille tarifaire pour obtenir les informations de référence SKU.

### <a name="the-total-amount-on-marketplace-does-not-match-on-usage-summary-and-csv-report"></a>Le montant total sur la Place de marché ne correspond pas au résumé de l’utilisation et au rapport CSV

Le rapport de frais de la Place de marché affiche uniquement les frais de la Place de marché basés sur l’utilisation. Les frais à usage unique ne sont pas affichés. Vous pouvez vous reporter à la page Résumé de l’utilisation pour connaître les utilisations à jour sur les frais basés sur l’utilisation et les frais à usage unique.

### <a name="there-is-no-information-on-my-api-report"></a>Il n’existe aucune information sur mon rapport d’API

Les clés API expirent tous les six mois. Si vous rencontrez un problème, générez une nouvelle clé API. Il est également important de demander à votre administrateur EA de générer les nouvelles clés API et de suivre les étapes de la FAQ sur le rapport d’API.

### <a name="my-power-bi-report-isnt-working"></a>Mon rapport Power BI ne fonctionne pas

En cas de problème avec Power BI, veuillez ouvrir un ticket technique auprès de l’équipe Power BI à l’adresse [https://support.powerbi.com](https://support.powerbi.com) afin que l’équipe puisse vous aider.

### <a name="my-resource-tags-arent-showing-up-on-my-reports"></a>Mes balises de ressources ne s’affichent pas dans mes rapports

Les balises de ressources sont gérées sur le portail Azure. Vous pouvez contacter l’équipe des abonnements Azure à l’adresse [https://portal.azure.com](https://portal.azure.com). Suivez les étapes de [ce lien](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) pour savoir comment ouvrir une demande de support.

### <a name="why-does-my-resource-rate-change-every-day"></a>Pourquoi mon taux de ressource change-t-il tous les jours ?

Le taux de ressource dans le rapport de détails de l’utilisation est une valeur calculée et représente le tarif mensuel moyen facturé pour un service. Ce taux est calculé à l’aide de la moyenne de l’engagement mensuel et des frais de dépassement mensuels pour une unité de service. La partie de l’utilisation facturée par rapport à l’engagement et aux tarifs de dépassement change à chaque fin de journée. Pour cette raison, le taux de ressource change également au cours du mois. Le taux de ressource est verrouillé le cinquième jour suivant la fin du mois.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Glossaire des processus de calcul du taux de ressource

**Nombre total d’unités brutes :** Quantité consommée dans le rapport de détails de l’utilisation.
**Ressource MOCP par unité :** Le système d’utilisation en amont émet les utilisations de chaque service dans des unités différentes. (Préréglage) **Consommation par unité :** Unité de mesure EA. (Préréglage) **Prix :** Prix unitaire dans le portail Azure EA.
**Coût total :** Coût total du rapport de détails de l’utilisation ou utilisation de l’engagement + dépassement dans le portail Azure EA.


### <a name="charges-calculation"></a>Calcul des frais

**Conversion en ressource MOCP par unité** = ROUND(Nombre total d’unités brutes * Ressource MOCP par unité,4) **Conversion en unités** = Unités après conversion en ressource MOCP par unité/Consommation par unité **Coût total** = Unités * Prix

### <a name="download-usage-calculation-logic"></a>Logique de calcul Télécharger l’utilisation

**Taux de ressource** = Coût total/(Nombre total d’unités brutes / Ressource MOCP par unité)

Le taux de ressource est dérivé en fonction des frais et ne correspond souvent pas au prix unitaire réel dans la grille tarifaire.

Pour les calculs de frais de dépassement, les données d’utilisation des ressources brute sont rapportées jusqu’à un maximum de six décimales, comme indiqué dans le rapport de téléchargement des données d’utilisation. Toutefois, le portail Azure EA arrondit l’utilisation à quatre décimales pour les unités d’engagement et tronque à zéro décimale pour les unités de dépassement. Cela signifie que, dans le portail Azure EA, pour toute utilisation facturée en tant que dépassement, nous facturons uniquement les unités complètes. Il y aura une différence importante entre le prix unitaire et le taux de ressource pour l’utilisation facturée en tant que dépassement ou pour plusieurs mois.

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur la compréhension de votre facture et de vos frais, consultez [Comprendre votre facture Contrat Entreprise Azure](billing-understand-your-bill-ea.md).
- Pour commencer à utiliser le portail Azure EA, consultez [Bien démarrer avec le portail Azure EA](billing-ea-portal-get-started.md).
