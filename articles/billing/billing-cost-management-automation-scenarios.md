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
ms.date: 6/13/2018
ms.author: erikre
ms.openlocfilehash: 09ecd46837a12f5f1a8278b1644dc099701bcd00
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584721"
---
# <a name="billing-and-cost-management-automation-scenarios"></a>Scénarios d’automatisation de la facturation et de la gestion des coûts

Les scénarios courants pour l’espace de facturation et de gestion des coûts sont identifiés ci-dessous et mappés vers différentes API qui peuvent être utilisées dans ces scénarios. Un résumé de toutes les API disponibles et les fonctionnalités qu’elles proposent sont accessibles sous le mappage du scénario à l’API. 

## <a name="common-scenarios"></a>Scénarios courants

Vous pouvez utiliser les API de facturation et de gestion des coûts dans divers scénarios pour répondre aux questions liées aux coûts et à l’utilisation.  Vous trouverez ci-dessous une description des scénarios courants.

- **Rapprochement de facture** : Microsoft m’a-t-il facturé le montant correct ?  Quelle est ma facture et puis-je la calculer moi-même ?

- **Frais croisés** : Maintenant que je sais combien je suis facturé, qui dans mon organisation doit payer ?

- **Optimisation des coûts** : Je sais combien j’ai été facturé, cependant, comment puis-je obtenir davantage de l’argent que je dépense sur Azure ?

- **Suivi des coûts** : Je souhaite voir combien je dépense et utilise sur Azure au fil du temps. Quelles sont les tendances ? Comment pourrais-je procéder mieux ?

- **Dépenses Azure au cours du mois** – À combien s’élèvent mes dépenses sur Azure pour le mois en cours jusqu’à la date actuelle ? Dois-je procéder à quelques ajustements dans mes dépenses et/ou mon utilisation d’Azure ? Quand, dans le mois, ma consommation d’Azure est-elle la plus élevée ?

- **Configurer des alertes** - Je souhaite configurer des alertes monétaires ou de consommation basées sur les ressources.

## <a name="scenario-to-api-mappings"></a>Mappages scénario vers API

|         API/scénario        | Rapprochement de facture    | Frais croisés    | Optimisation des coûts    | Suivi des coûts    | Dépenses en milieu de mois    | Alertes    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budgets                     |                           |                  |           X          |                  |                    |     X     |
| Marketplaces                |             X             |         X        |           X          |         X        |          X         |     X     |
| Price Sheet                 |             X             |         X        |           X          |         X        |          X         |           |
| Reservation recommendations |                           |                  |           X          |                  |                    |           |
| Reservation details         |                           |                  |           X          |         X        |                    |           |
| Reservation summaries       |                           |                  |           X          |         X        |                    |           |
| Usage details               |             X             |         X        |           X          |         X        |          X         |     X     |
| Billing periods             |             X             |         X        |           X          |         X        |                    |           |
| Factures                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Unrated usage               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Les mappages scénario vers API ci-dessous n’incluent pas les API Enterprise Consumption (Consommation de l’entreprise). Si possible, veuillez utiliser les API Consommation générales pour traiter les nouveaux scénarios de développement net à partir de maintenant.

## <a name="api-summaries"></a>Résumés des API

### <a name="consumption"></a>Consommation
(*Clients Web Direct + Enterprise pour toutes les API sauf celles indiquées ci-dessous*)

-   **Budgets** (*clients Enterprise UNIQUEMENT*) : utilisez [l’API Budgets](https://docs.microsoft.com/rest/api/consumption/budgets) pour créer des budgets de coût ou d’utilisation pour les ressources, les groupes de ressources ou les compteurs de facturation.  Une fois que vous avez créé des budgets, la génération d’alertes peut être configurée pour avertir lorsque les seuils de budget définis par l’utilisateur sont atteints. Des actions peuvent également être configurées pour se produire lorsque les montants de budget sont atteints.
-   **Marketplaces** (Places de marché) : utilisez [l’API Marketplaces Charges](https://docs.microsoft.com/rest/api/consumption/marketplaces) pour obtenir les données d’utilisation et de facturation sur toutes les ressources de la Place de marché (offres tierces Azure). Ces données peuvent être utilisées pour ajouter les coûts pour toutes les ressources de la Place de marché ou pour examiner les coûts/l’utilisation de ressources spécifiques.
-   **Price sheet** (Grille tarifaire) (*clients Enterprise UNIQUEMENT*) : les clients Enterprise peuvent utiliser [l’API Price Sheet](https://docs.microsoft.com/rest/api/consumption/pricesheet) pour récupérer leurs prix personnalisés pour tous les compteurs. Les entreprises peuvent utiliser ces données en association avec les détails sur l’utilisation et l’utilisation des Places de marché pour effectuer des calculs de coût à l’aide des données relatives à l’utilisation et à la Place de marché. 
-   **Reservation recommendations** (Recommandations de réservation) : utilisez [l’API Reservation Recommendations](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) pour obtenir des recommandations sur l’achat d’instances de machine virtuelle réservées. Les recommandations sont conçues pour permettre aux clients d’analyser les économies et les montants d’achat attendus.
-   **Reservation details** (Détails de la réservation) : utilisez [l’API Reservation Details](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) pour afficher des informations sur les réservations de machine virtuelle déjà achetées, comme la quantité de consommation réservée par rapport à la quantité actuellement utilisée. Vous pouvez voir les données au niveau de la machine virtuelle.
-   **Reservation summaries** (Résumés de réservation) : utilisez [l’API Reservation Summaries](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) pour afficher des informations agrégées sur les réservations de machine virtuelle déjà achetées, comme la quantité de consommation réservée par rapport à la quantité actuellement utilisée dans l’agrégation. 
-   **Usage details** (Détails d’utilisation) : utilisez [l’API Usage Details](https://docs.microsoft.com/rest/api/consumption/usagedetails) pour obtenir la facturation et l’utilisation sur toutes les ressources propriétaires Azure. Les informations sont sous la forme d’enregistrements détaillés de l’utilisation, qui sont actuellement émis une fois par compteur par jour. Les informations peuvent être utilisées pour ajouter les coûts pour toutes les ressources ou pour examiner les coûts/l’utilisation de ressources spécifiques.
-   **RateCard** : les clients Web Direct peuvent utiliser [l’API RateCard](https://msdn.microsoft.com/library/azure/mt219005.aspx) pour obtenir les tarifs de leur compteur. Ils peuvent ensuite utiliser les informations retournées avec leurs informations sur l’utilisation de la ressource pour calculer manuellement la facture attendue. 
-   **Unrated usage** (Utilisation non autorisée) : vous pouvez utiliser [l’API Unrated Usage](https://msdn.microsoft.com/library/azure/mt219003.aspx) pour obtenir des informations d’utilisation brute, avant tout contrôle de compteur/facturation effectué(e) par Azure.

### <a name="billing"></a>Facturation
-   **Billing periods** (Périodes de facturation) : utilisez [l’API Billing Periods](https://docs.microsoft.com/rest/api/billing/billingperiods) afin de déterminer une période de facturation à analyser, ainsi que l’ID de facture pour cette période. L’ID de facture peut être utilisé avec l’API Invoice ci-dessous. 
-   **Invoices** (Factures) : utilisez [l’API Invoices](https://docs.microsoft.com/rest/api/billing/invoices) pour obtenir l’URL de téléchargement pour une facture pour une période de facturation donnée sous forme de fichier PDF.

### <a name="enterprise-consumption"></a>Consommation de l’entreprise
*(Toutes les API version Enterprise UNIQUEMENT)*

-   **Balance summary** (Résumé de solde) : utilisez [l’API Balance Summary](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) pour obtenir un résumé mensuel des informations sur les soldes, nouveaux achats, frais de service de la Place de marché Azure, ajustements et dépassement des frais. Vous pouvez obtenir ces informations pour la période de facturation en cours ou n’importe quelle période passée. Les entreprises peuvent utiliser ces données pour les comparer avec les frais résumés calculés. Cette API ne fournit pas d’informations propres à la ressource ou une vue agrégée des coûts.
-   **Usage details** (Détails d’utilisation) : utilisez [l’API Usage Details](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) pour obtenir des informations propriétaires détaillées sur l’utilisation d’Azure pour le mois en cours, une période de facturation spécifique ou une période de dates personnalisée. Les entreprises peuvent utiliser ces données pour calculer manuellement la facture en fonction du taux et de la consommation, et peuvent également utiliser les informations relatives au département/à l’organisation pour attribuer les coûts entre plusieurs organisations. Les données fournissent une vue propre à la ressource de l’utilisation/des coûts.
-   **Marketplace store charge** (Frais Magasin Place de marché) : utilisez [l’API Marketplace Store Charge](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) pour obtenir des informations tierces détaillées sur l’utilisation d’Azure pour le mois en cours, une période de facturation spécifique ou une période de dates personnalisée. Les entreprises peuvent utiliser ces données pour calculer manuellement la facture en fonction du taux et de la consommation, et peuvent également utiliser les informations relatives au département/à l’organisation pour attribuer les coûts entre plusieurs organisations. L’API Marketplace Store Charge fournit une vue propre à la ressource de l’utilisation/des coûts.
-   **Price sheet** (Grille tarifaire) : [l’API Price Sheet](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) fournit les tarifs applicables pour chaque compteur selon l’abonnement et la période de facturation donnés. Ces informations sur les tarifs peuvent être utilisées en association avec les informations sur les détails de l’utilisation et l’utilisation des Places de marché pour calculer manuellement la facture attendue.
-   **Billing periods** (Périodes de facturation) : utilisez [l’API Billing Periods](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) pour obtenir une liste de périodes de facturation, ainsi que d’une propriété qui pointe vers l’itinéraire de l’API pour les quatre jeux de données de l’API Enterprise qui se rapportent à cette période de facturation : BalanceSummary, UsageDetails, Marketplace Charges et PriceSheet.
-   **Azure reservation recommendations** (Recommandations sur les réservations) : [l’API Reserved Instance Recommendations](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) consulte les données sur 7, 30 et 60 jours, relatives à l’utilisation de la machine virtuelle et propose des recommandations d’achat unique et partagé. L’API Reserved Instance permet aux clients d’analyser les économies et les montants d’achat recommandé attendus.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="what-is-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Quelle est la différence entre les API Enterprise Reporting et Consumption ? Quand utiliser chaque API ?
Ces API offrent un ensemble similaire de fonctionnalités et peuvent répondre à la même série de questions dans les domaines de la facturation et de la gestion des coûts. Toutefois, chaque API cible des publics différents : 

- **API Enterprise Reporting** : ces API sont accessibles aux clients qui ont signé un Contrat Entreprise avec Microsoft. Ce contrat leur donne accès à des engagements monétaires négociés et à des tarifs personnalisés. Les API nécessitent l’utilisation d’une clé qui peut être obtenue dans [Enterprise Portal](https://ea.azure.com). Pour obtenir une description de ces API, consultez [Vue d’ensemble des API Reporting pour les clients Enterprise](billing-enterprise-api.md).

- **API Consumption** : ces API sont accessibles à tous les clients, à quelques exceptions près. Pour plus d’informations, consultez [Vue d’ensemble des API Azure Consumption](billing-consumption-api-overview.md) et [Informations de référence sur les API Azure Consumption](https://docs.microsoft.com/rest/api/consumption/). Les API fournies constituent la solution recommandée pour les scénarios de développement les plus récents. 

### <a name="what-is-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Quelle est la différence entre les API Usage Details et Usage ?
Ces API fournissent des données fondamentalement différentes :

- **Usage Details**: [l’API Usage Details](https://docs.microsoft.com/rest/api/consumption/usagedetails) fournit des informations relatives au coût et à l’utilisation d’Azure par instance de compteur. Les données fournies sont déjà passées par le système de comptage des coûts d’Azure, sont associées à des coûts associés et reflètent d’autres changements éventuels :

    - Changements pour tenir compte de l’utilisation des engagements monétaires prépayés
    - Changements pour tenir compte des différences d’utilisation découvertes par Azure

- **Usage** : [l’API Usage](https://msdn.microsoft.com/library/Mt219003.aspx) fournit des informations brutes sur l’utilisation d’Azure avant qu’elles ne passent par le système de comptage des coûts d’Azure. Ces données peuvent ne présenter aucune corrélation avec l’utilisation et/ou les frais constatés après leur passage dans le système de comptage des coûts d’Azure.

### <a name="what-is-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Quelle est la différence entre les API Invoice et Usage Details ?
Ces API offrent une vue différente des mêmes données. [L’API Invoice](https://docs.microsoft.com/rest/api/billing/invoices) s’adresse uniquement aux clients Web Direct et fournit une synthèse mensuelle de votre facture en fonction des frais cumulés pour chaque type de compteur. [L’API Usage Details](https://docs.microsoft.com/rest/api/consumption/usagedetails) offre une vue granulaire des enregistrements sur l’utilisation/les coûts pour chaque jour. Elle peut être utilisée par les clients Enterprise et Web Direct.

### <a name="what-is-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Quelle est la différence entre les API Price Sheet et RateCard ?
Ces API fournissent des ensembles de données similaires, mais ont des publics différents. Vous trouverez des informations ci-dessous.

- API Price Sheet : [l’API Price Sheet](https://docs.microsoft.com/rest/api/consumption/pricesheet) fournit les tarifs personnalisés qui ont été négociés pour un client Enterprise.
- API RateCard : [l’API RateCard](https://msdn.microsoft.com/library/mt219005.aspx) fournit les tarifs publics qui s’appliquent aux clients Web Direct.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’utilisation des API Azure pour obtenir par programmation des insights sur votre utilisation d’Azure, consultez [Présentation des API Azure Consumption](billing-consumption-api-overview.md) et [Présentation des API Azure Billing](billing-usage-rate-card-overview.md).
- Pour comparer votre facture au fichier détaillé d’utilisation quotidienne aux rapports de gestion des coûts du portail Azure, consultez [Comprendre votre facture pour Microsoft Azure](billing-understand-your-bill.md).
- Si vous avez des questions ou besoin d’aide, créez une [demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
