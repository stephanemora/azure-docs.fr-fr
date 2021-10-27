---
title: Afficher les détails de votre récapitulatif d’utilisation Azure et télécharger des rapports pour les inscriptions EA directes
description: Cet article explique comment les administrateurs d’entreprise d’inscriptions Contrat Entreprise (EA) directes peuvent afficher un récapitulatif de leurs données d’utilisation, le paiement anticipé Azure consommé, et les frais associés à d’autres utilisations dans le portail Azure.
author: bandersmsft
ms.author: banders
ms.date: 10/12/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: sapnakeshari
ms.openlocfilehash: 005f2c16e463bd01c7e453548ddb29c50e2f937b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007457"
---
# <a name="view-your-usage-summary-details-and-download-reports-for-direct-ea-enrollments"></a>Afficher les détails de votre récapitulatif d’utilisation et télécharger des rapports pour les inscriptions EA directes

Cet article explique comment les administrateurs d’entreprise d’inscriptions Contrat Entreprise (EA) directes peuvent afficher un récapitulatif de leurs données d’utilisation, le paiement anticipé Azure consommé, et les frais associés à d’autres utilisations dans le portail Azure. Les frais sont présentés au niveau du résumé pour tous les comptes et abonnements de l’inscription.

## <a name="prerequisites"></a>Prérequis

Pour consulter et vérifier les frais figurant sur votre facture, vous devez être administrateur d’entreprise. Pour plus d’informations, consultez [Comprendre les rôles d’administrateur Contrat Entreprise Azure dans Azure](understand-ea-roles.md). Si vous ne connaissez pas l’administrateur d’entreprise de votre organisation, créez une demande de support dans le portail Azure.

## <a name="review-usage-charges"></a>Afficher les frais d’utilisation

Pour voir l’utilisation détaillée de comptes spécifiques, téléchargez le rapport de détails de l’utilisation :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez **Cost Management + Billing**, puis sélectionnez cette option.
1. Sélectionnez **Étendues de facturation** dans le menu de navigation, puis sélectionnez le compte de facturation que vous souhaitez utiliser.
1. Dans le menu de navigation, sélectionnez **Utilisation + frais**.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/navigation-usage-charges.png" alt-text="Capture d’écran montrant la page utilisation + frais." lightbox="./media/direct-ea-azure-usage-charges-invoices/navigation-usage-charges.png" :::
1. Pour afficher les détails des années précédentes, sélectionnez un **intervalle de temps**.

Le tableau suivant répertorie les termes et descriptions figurant sur la page Utilisation + frais dans le portail Azure. Les frais indiqués dans le portail Azure sont exprimés en dollars américains (USD).

| **Terme** | **Description** |
| --- | --- |
| Month | Mois d’utilisation |
| Frais et crédits | Crédit appliqué pendant cette période spécifique. |
| Dépassement de service | Les frais d’utilisation de votre organisation dépassent votre solde de crédit |
| Facturé séparément | les services utilisés par votre organisation ne sont pas couverts par le crédit. |
| Place de marché Azure | Les achats et l’utilisation liés à la Place de marché Azure ne sont pas couverts par le crédit de votre organisation et sont facturés séparément |
| Charges totales | Frais et crédits + Dépassement de service + Facturé séparément + Place de marché Azure |

## <a name="download-usage-charges-csv-file"></a>Télécharger le fichier CSV des frais d’utilisation

Les administrateurs d’entreprise utilisent la page Télécharger l'utilisation + les frais pour télécharger les rapports suivants dans des fichiers au format CSV.

- **Détails d’utilisation** : en fonction de votre sélection, le fichier CSV fournit tous les frais (utilisation et achats), y compris les achats RI (réservation). Ou les frais amortis (utilisation et achats), y compris les achats de réservation.
- **Marketplace Store Charge** (Frais du magasin de la Place de marché) : le fichier CSV Marketplace Store Charge retourne la répartition des frais de la Place de marché en fonction de l’utilisation par jour et pour la période de facturation spécifiée.
- **Price sheet** (Grille tarifaire) : le fichier CSV Price sheet contient les tarifs applicables pour chaque compteur selon l’abonnement et la période de facturation donnés.
- **Balance and Summary** (Solde et résumé) : le fichier CSV Balance and Summary contient un récapitulatif mensuel des informations sur les soldes, nouveaux achats, frais de service de la Place de marché, ajustements et dépassement des frais.

1. Sélectionnez le symbole **Download** (Télécharger) à côté du rapport mensuel qui vous intéresse.  
:::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/download-usage-charges-csv.png" alt-text="Capture d’écran montrant l’option Télécharger." :::
1. Sur la page Télécharger l’utilisation + frais, sélectionnez **Préparer le document** pour le rapport que vous souhaitez télécharger.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/prepare-document.png" alt-text="Capture d’écran montrant la page préparer le document." lightbox="./media/direct-ea-azure-usage-charges-invoices/prepare-document.png" :::
1. Azure peut prendre un certain temps pour préparer votre téléchargement, en fonction de votre utilisation mensuelle. Quand le rapport est prêt à être téléchargé, sélectionnez **Télécharger le fichier CSV**.

Les administrateurs d’entreprise peuvent également afficher un récapitulatif global des frais pour la période sélectionnée au bas de la page Utilisation + frais.

:::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/usage-charges-summary.png" alt-text="Capture d’écran montrant un résumé des frais d’utilisation." lightbox="./media/direct-ea-azure-usage-charges-invoices/prepare-document.png":::

## <a name="download-or-view-your-azure-billing-invoice"></a>Télécharger ou afficher votre facture Azure

Vous pouvez télécharger votre facture à partir du [portail Azure](https://portal.azure.com) ou la faire envoyer par courrier électronique. Les factures sont envoyées à toute personne autorisée à recevoir des factures pour l’inscription.

Seul un administrateur d’entreprise est autorisé à afficher et à obtenir la facture. Pour en savoir plus sur l’accès aux informations de facturation, consultez [Manage access to Azure billing using roles](manage-billing-access.md) (Utiliser des rôles pour gérer l’accès à la facturation Azure).

Vous recevez une facture Azure si l’un des événements suivants se produit au cours de la période de facturation :

- **Dépassement de service** : les frais d’utilisation de votre organisation dépassent votre solde de crédit.
- **Frais facturés séparément** : les services utilisés par votre organisation ne sont pas couverts par le crédit. Les services suivants vous sont facturés, malgré votre solde de crédit :
  - Canonical
  - Citrix XenApp Essentials
  - Citrix XenDesktop
  - Utilisateur inscrit
  - Openlogic
  - Utilisateur inscrit de Remote Access Rights XenApp Essentials
  - Ubuntu Advantage
  - Visual Studio Enterprise (mensuel)
  - Visual Studio Enterprise (annuel)
  - Visual Studio Professional (mensuel)
  - Visual Studio Professional (annuel)
- **Frais de la Place de marché** : les achats et l’utilisation liés à la Place de marché Azure ne sont pas couverts par le crédit de votre organisation. Les frais de la Place de marché vous sont donc facturés, malgré votre solde de crédit. Dans le portail Azure, un administrateur d’entreprise peut activer et désactiver les achats liés à la Place de marché.

Votre facture présente les frais d’utilisation d’Azure avec les coûts associés affichés en premier, suivis des frais relatifs à la Place de marché. Si vous avez un solde créditeur, il s’appliquera à l’utilisation d’Azure et votre facture présentera l’utilisation d’Azure et celle de la Place de marché en dernier, sans aucun coût.

### <a name="download-your-azure-invoices-pdf"></a>Télécharger vos factures Azure (.pdf)

Pour la plupart des abonnements, vous pouvez télécharger votre facture dans le portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez **Cost Management + Billing**, puis sélectionnez cette option.
1. Sélectionnez **Étendues de facturation** dans le menu de navigation, puis sélectionnez le compte de facturation que vous souhaitez utiliser.
1. Dans le menu de navigation, sélectionnez  **Factures**. La page Factures affiche toutes les factures et notes de crédit générées au cours des 12 derniers mois.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/invoices-page.png" alt-text="Capture d’écran montrant la page Factures." lightbox="./media/direct-ea-azure-usage-charges-invoices/invoices-page.png" :::
    
1. Sur la page des factures, recherchez la ligne de la facture que vous souhaitez télécharger. À droite de la ligne, sélectionnez le symbole des points de suspension ( **…** ).
1. Dans le menu contextuel, sélectionnez **Télécharger**.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/download-context-menu.png" alt-text="Capture d’écran montrant le menu contextuel Télécharger."  :::

Vous pouvez sélectionner un intervalle de temps pour afficher les trois dernières années des détails de la facture.

Si la facture comporte plusieurs éléments de ligne dépassant 40 pages pour un fichier PDF, elle est divisée en plusieurs numéros de document.

Le tableau suivant répertorie les termes et les descriptions indiqués sur la page Factures :

| **Terme** | **Description** |
| --- | --- |
| Date du document | Date à laquelle la facture ou la note de crédit a été générée. |
| Numéro du document | Numéro de la facture ou de la note de crédit. |
| Période de facturation | Période de facturation de la facture ou de la note de crédit. |
| Numéro de BDC | Numéro de bon de commande de la facture ou de la note de crédit. |
| Montant total | Montant total de la facture ou du crédit. |

## <a name="review-credit-charges"></a>Afficher les frais de crédit

Les informations de cette section expliquent comment afficher le solde initial, le solde de fin et les ajustements de crédit pour votre paiement anticipé Azure (précédemment appelé engagement financier).

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez **Cost Management + Billing**, puis sélectionnez cette option.
1. Sélectionnez **Étendues de facturation** dans le menu de navigation, puis sélectionnez le compte de facturation que vous souhaitez utiliser.
1. Dans le menu de navigation, sélectionnez **Crédits + engagements**.
1. L’onglet Crédits affiche une ventilation de vos crédits et un graphique indiquant votre solde au fil du temps.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/credits-tab.png" alt-text="Capture d’écran montrant l’onglet Crédits." lightbox="./media/direct-ea-azure-usage-charges-invoices/credits-tab.png" :::

Le tableau suivant répertorie les termes et les descriptions indiqués sur l’onglet Crédits.

| **Terme** | **Description** |
| --- | --- |
| Month | Mois du crédit |
| Crédit de départ | Crédit de départ pour le mois en cours |
| Nouveau crédit | Nouveaux crédits ajoutés |
| Ajustements | Ajustements effectués au cours du mois |
| Crédit imputé aux frais | Montant total de la facture ou du crédit généré |
| Crédit de fin | Solde final du crédit |

## <a name="review-reservation-transaction-details"></a>Afficher les détails de la transaction de réservation

Vous pouvez afficher toutes les réservations placées pour un Contrat Entreprise dans le portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez **Cost Management + Billing**, puis sélectionnez cette option.
1. Sélectionnez **Étendues de facturation** dans le menu de navigation, puis sélectionnez le compte de facturation que vous souhaitez utiliser.
1. Dans le menu de navigation, sélectionnez **Transactions de réservation**. Les prix affichés dans l’image suivante sont des exemples.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/reservation-transactions.png" alt-text="Capture d’écran montrant la page des transactions de réservation." lightbox="./media/direct-ea-azure-usage-charges-invoices/reservation-transactions.png" :::

Le tableau suivant répertorie les termes et les descriptions indiqués sur la page Transactions de réservation.

| **Terme** | **Description** |
| --- | --- |
| Date | Date à laquelle la réservation a été effectuée |
| Nom | Nom de la réservation |
| Description | Description de la réservation |
| Achat d'abonnement | Abonnement sous lequel la réservation a été effectuée |
| Achat de compte | Compte d’achat sous lequel la réservation a été effectuée |
| Périodicité de facturation | Fréquence de facturation de la réservation |
| Type | Type de la transaction. Par exemple, Achat ou Remboursement. |
| Quantité | Quantité de réservations achetée |
| Montant (USD) | Coût de réservation |

## <a name="csv-report-formatting-issues"></a>Problèmes de mise en forme des rapports CSV

Lorsque vous affichez des rapports CSV dans Excel et que votre devise de facturation est en Euros, vous pouvez rencontrer des problèmes de mise en forme liés aux virgules et aux points.

Les coûts indiqués sont des exemples.

Par exemple, vous pouvez voir :

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
|---|--- | ---|---|
| Heures | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Vous *devriez* néanmoins voir ce qui suit :

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Heures | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

Le problème de mise en forme est dû aux paramètres par défaut de la fonctionnalité d'importation Excel. Excel importe tous les champs en texte *général* et suppose qu’un nombre est séparé dans la norme mathématique. Par exemple : *1,000.00*.

Si votre devise utilise un point ( **.** ) comme séparateur de milliers et une virgule comme séparateur décimal ( **,** ), ce nombre s’affichera de manière incorrecte. Par exemple : *1.000,00*. Les résultats de l’importation peuvent varier en fonction de votre paramètre de langue régionale.

Pour importer le fichier CSV sans problèmes de mise en forme :

1. Dans Microsoft Excel, sélectionnez **Fichier** > **Ouvrir**. L’Assistant Importation de texte s’affiche.
1. Sous **Type de données d’origine**, choisissez **Délimité**. La valeur par défaut est **Largeur fixe**.
1. Sélectionnez **Suivant**.
1. Sous **Délimiteurs**, cochez la case **Virgule**. Désactivez l’option **Tabulation** si elle est activée.
1. Sélectionnez **Suivant**.
1. Faites défiler jusqu’aux colonnes **ResourceRate** et **ExtendedCost**.
1. Sélectionnez la colonne **ResourceRate**. Elle apparaît en surbrillance en noir.
1. Sous la section **Format des données en colonne**, sélectionnez **Texte** au lieu de **Général**. L'en-tête de la colonne passera de **Général** à **Texte**.
1. Répétez les étapes 8 et 9 pour la colonne **Extended Cost**, puis sélectionnez **Terminer**.

> [!TIP]
> Si Excel est configuré pour ouvrir automatiquement des fichiers CSV, vous devez utiliser la fonction **Ouvrir** dans Excel à la place. Dans Excel, sélectionnez **Fichier** > **Ouvrir**.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les tâches courantes qu’un administrateur de l’entreprise directe effectue dans le Portail Azure, consultez [Administration d’Azure direct EA](direct-ea-administration.md).