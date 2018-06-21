---
title: Scénarios d’automatisation de la facturation et de la gestion des coûts Azure | Microsoft Docs
description: Découvrez comment des scénarios de facturation et de gestion des coûts courants sont mappés vers différentes API.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 204b15b2-6667-4b6c-8ea4-f32c06f287fd
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/07/2018
ms.author: erikre
ms.openlocfilehash: f84071577e9636e40d621093e2c57e3e9adf4913
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247664"
---
# <a name="billing-and-cost-management-automation-scenarios"></a>Scénarios d’automatisation de la facturation et de la gestion des coûts

Les scénarios courants pour l’espace de facturation et de gestion des coûts sont identifiés ci-dessous et mappés vers différentes API qui peuvent être utilisées dans ces scénarios. Un résumé de toutes les API disponibles et les fonctionnalités qu’elles proposent sont accessibles sous le mappage du scénario à l’API. 

## <a name="common-scenarios"></a>Scénarios courants 

Vous pouvez utiliser les API de facturation et de gestion des coûts dans divers scénarios pour répondre aux questions liées aux coûts et à l’utilisation.  Vous trouverez ci-dessous une description des scénarios courants.

- **Rapprochement de facture** - Microsoft m’a-t-il facturé le montant correct ?  Quelle est ma facture et puis-je la calculer moi-même ?

- **Frais croisés** - Maintenant que je sais combien je suis facturé, qui dans mon organisation doit payer ?

- **Optimisation des coûts** - Je sais combien j’ai été facturé, cependant, comment puis-je obtenir davantage de l’argent que je dépense sur Azure ?

- **Suivi des coûts** - Je souhaite voir combien je dépense et utilise sur Azure au fil du temps. Quelles sont les tendances ? Comment pourrais-je procéder mieux ?

- **Dépenses Azure au cours du mois** – À combien s’élèvent mes dépenses sur Azure pour le mois en cours jusqu’à la date actuelle ? Dois-je procéder à quelques ajustements dans mes dépenses et/ou mon utilisation d’Azure ? Quand, dans le mois, ma consommation d’Azure est-elle la plus élevée ?

- **Configurer des alertes** - Je souhaite configurer des alertes monétaires ou de consommation basées sur les ressources.

## <a name="scenario-to-api-mappings"></a>Mappages scénario vers API

|         API/scénario        | Rapprochement de facture    | Frais croisés    | Optimisation des coûts    | Suivi des coûts    | Dépenses en milieu de mois    | Alertes    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budgets                     |                           |                  |           X          |                  |                    |     X     |
| Marketplaces                |             X             |         X        |           X          |         X        |          X         |     X     |
| Price Sheet                 |             X             |         X        |           X          |         X        |          X         |           |
| Reservation Recommendations |                           |                  |           X          |                  |                    |           |
| Reservation Details         |                           |                  |           X          |         X        |                    |           |
| Reservation Summaries       |                           |                  |           X          |         X        |                    |           |
| Usage Details               |             X             |         X        |           X          |         X        |          X         |     X     |
| Billing Periods             |             X             |         X        |           X          |         X        |                    |           |
| Factures                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Unrated Usage               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Les mappages scénario vers API ci-dessous n’incluent pas les API Enterprise Consumption (Consommation de l’entreprise). Si possible, veuillez utiliser les API Consommation générales pour traiter les nouveaux scénarios de développement net à partir de maintenant.

## <a name="api-summaries"></a>Résumés des API

### <a name="consumption"></a>Consommation
(*Clients Web Direct + Enterprise pour toutes les API sauf celles indiquées ci-dessous*)

-   **Budgets** (*clients Enterprise UNIQUEMENT*) : utilisez [l’API Budgets](https://docs.microsoft.com/rest/api/consumption/budgets) pour créer des budgets de coût ou d’utilisation pour les ressources, les groupes de ressources ou les compteurs de facturation.  Une fois que vous avez créé des budgets, la génération d’alertes peut être configurée pour avertir lorsque les seuils de budget définis par l’utilisateur sont atteints. Des actions peuvent également être configurées pour se produire lorsque les montants de budget sont atteints.
-   **Marketplaces** (Places de marché) : utilisez [l’API Marketplaces Charges](https://docs.microsoft.com/rest/api/consumption/marketplaces) pour obtenir les données d’utilisation et de facturation sur toutes les ressources de la Place de marché (offres tierces Azure). Ces données peuvent être utilisées pour ajouter les coûts pour toutes les ressources de la Place de marché ou pour examiner les coûts/l’utilisation de ressources spécifiques.
-   **Price Sheet** (Grille tarifaire) (*clients Enterprise UNIQUEMENT*) : les clients Enterprise peuvent utiliser [l’API Price Sheet](https://docs.microsoft.com/rest/api/consumption/pricesheet) pour récupérer leur tarification personnalisée pour tous les compteurs. Les entreprises peuvent utiliser ces données en association avec les détails sur l’utilisation et l’utilisation des Places de marché pour effectuer des calculs de coût à l’aide des données relatives à l’utilisation et à la Place de marché. 
-   **Reservation Recommendations** (Recommandations de réservation) : utilisez [l’API Reservation Recommendations](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) pour obtenir des recommandations sur l’achat d’instances réservées de machine virtuelle. Les recommandations sont conçues pour permettre aux clients d’analyser les économies et les montants d’achat attendus.
-   **Reservation Details** (Détails de la réservation) : utilisez [l’API Reservation Details](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) pour afficher des informations sur les réservations de machine virtuelle déjà achetées, comme la quantité de consommation réservée par rapport à la quantité actuellement utilisée. Vous pouvez voir les données au niveau de la machine virtuelle.
-   **Reservation Summaries** (Résumés de réservation) : utilisez [l’API Reservation Summaries](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) pour afficher des informations agrégées sur les réservations de machine virtuelle déjà achetées, comme la quantité de consommation réservée par rapport à la quantité actuellement utilisée dans l’agrégation. 
-   **Usage Details** (Détails d’utilisation) : utilisez [l’API Usage Details](https://docs.microsoft.com/rest/api/consumption/usagedetails) pour obtenir la facturation et l’utilisation sur toutes les ressources premières Azure. Les informations sont sous la forme d’enregistrements détaillés de l’utilisation, qui sont actuellement émis une fois par compteur par jour. Les informations peuvent être utilisées pour ajouter les coûts pour toutes les ressources ou pour examiner les coûts/l’utilisation de ressources spécifiques.
-   **RateCard** : les clients Web Direct peuvent utiliser [l’API RateCard](https://msdn.microsoft.com/library/azure/mt219005.aspx) pour obtenir les tarifs de leur compteur. Ils peuvent ensuite utiliser les informations retournées avec leurs informations sur l’utilisation de la ressource pour calculer manuellement la facture attendue. 
-   **Unrated Usage** (Utilisation non autorisée) : vous pouvez utiliser [l’API Unrated Usage](https://msdn.microsoft.com/library/azure/mt219003.aspx) pour obtenir des informations d’utilisation brute, avant tout contrôle de compteur/facturation effectué(e) par Azure.

### <a name="billing"></a>Facturation
-   **Billing Periods** (Périodes de facturation) : utilisez [l’API Billing Periods](https://docs.microsoft.com/rest/api/billing/billingperiods) afin de déterminer une période de facturation à analyser, ainsi que l’ID de facture pour cette période. L’ID de facture peut être utilisé avec l’API Invoice ci-dessous. 
-   **Invoices** (Factures) : utilisez [l’API Invoices](https://docs.microsoft.com/rest/api/billing/invoices) pour obtenir l’URL de téléchargement pour une facture pour une période de facturation donnée sous forme de fichier PDF.

### <a name="enterprise-consumption"></a>Consommation de l’entreprise
*(Toutes les API version Enterprise UNIQUEMENT)*

-   **Balance Summary** (Résumé de solde) : utilisez [l’API Balance Summary](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) pour obtenir un résumé mensuel des informations sur les soldes, nouveaux achats, frais de service de la Place de marché Azure, ajustements et dépassement des frais. Vous pouvez obtenir ces informations pour la période de facturation en cours ou n’importe quelle période passée. Les entreprises peuvent utiliser ces données pour les comparer avec les frais résumés calculés. Cette API ne fournit pas d’informations propres à la ressource ou une vue agrégée des coûts.
-   **Usage Details** (Détails d’utilisation) : utilisez [l’API Usage Details](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) pour obtenir des informations propriétaires détaillées sur l’utilisation d’Azure pour le mois en cours, une période de facturation spécifique ou une période de dates personnalisée. Les entreprises peuvent utiliser ces données pour calculer manuellement la facture en fonction du taux et de la consommation, et peuvent également utiliser les informations relatives au département/à l’organisation pour attribuer les coûts entre plusieurs organisations. Les données fournissent une vue propre à la ressource de l’utilisation/des coûts.
-   **Marketplace Store Charge** (Frais Magasin Place de marché) : utilisez [l’API Marketplace Store Charge](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) pour obtenir des informations tierces détaillées sur l’utilisation d’Azure pour le mois en cours, une période de facturation spécifique ou une période de dates personnalisée. Les entreprises peuvent utiliser ces données pour calculer manuellement la facture en fonction du taux et de la consommation, et peuvent également utiliser les informations relatives au département/à l’organisation pour attribuer les coûts entre plusieurs organisations. L’API Marketplace Store Charge fournit une vue propre à la ressource de l’utilisation/des coûts.
-   **Price Sheet** (Grille tarifaire) : [l’API Price Sheet](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) fournit les tarifs applicables pour chaque compteur selon l’abonnement et la période de facturation donnés. Ces informations sur les tarifs peuvent être utilisées en association avec les informations sur les détails de l’utilisation et l’utilisation des Places de marché pour calculer manuellement la facture attendue.
-   **Billing Periods** (Périodes de facturation) : utilisez [l’API Billing Periods](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) pour obtenir une liste de périodes de facturation, ainsi que d’une propriété qui pointe vers l’itinéraire de l’API pour les quatre jeux de données de l’API Enterprise qui se rapportent à cette période de facturation : BalanceSummary, UsageDetails, Marketplace Charges et PriceSheet.
-   **Reserved Instance Recommendations** (Recommandations d’instance réservée) : [l’API Reserved Instance Recommendations](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) consulte les données sur 7 jour, 30 jours et 60 jours, relatives à l’utilisation de la machine virtuelle et propose des recommandations d’achat unique et partagé. L’API Reserved Instance permet aux clients d’analyser les économies et les montants d’achat recommandé attendus.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’utilisation des API Facturation Azure pour obtenir par programme des insights sur votre utilisation d’Azure, consultez [Présentation de l’API Facturation Azure](billing-usage-rate-card-overview.md).
