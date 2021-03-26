---
title: Liste des requêtes système
description: Découvrez les requêtes système que vous pouvez utiliser pour obtenir par programme des données d’analyse relatives à vos offres sur la place de marché commerciale Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: f2b5f7eb559e349947f88067a3d2ea53d99b7cbf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583499"
---
# <a name="list-of-system-queries"></a>Liste des requêtes système

Les requêtes système suivantes peuvent être utilisées dans l'[API Créer un rapport](analytics-programmatic-access.md#create-report-api) directement avec un `QueryId`. Les requêtes système sont similaires aux rapports d’exportation dans l’Espace partenaires pour une période de calcul de six mois (6M).

Pour plus d’informations sur les noms des colonnes, les attributs et leur description, consultez les articles suivants :

- [Tableau de bord Clients dans l’analytique de la Place de marché commerciale](customer-dashboard.md#customer-details-table)
- [Tableau de bord Commandes dans l’analytique de la place de marché commerciale](orders-dashboard.md#orders-details-table)
- [Tableau de bord Utilisation dans les données d’analytique de la Place de marché commerciale](usage-dashboard.md#usage-details-table)
- [Tableau de bord Insights de la Place de marché dans les données d’analytique de la Place de marché commerciale](insights-dashboard.md#marketplace-insights-details-table)

Les sections suivantes fournissent des requêtes de rapport pour différents rapports.

## <a name="customers-report-query"></a>Requête de rapport clients

**Description du rapport**: rapport clients pour les six derniers mois

**QueryID** :  `b9df4929-073f-4795-b0cb-a2c81b11e28d`

**Requête de rapport** :

`SELECT MarketplaceSubscriptionId,DateAcquired,DateLost,ProviderName,ProviderEmail,FirstName,LastName,Email,CustomerCompanyName,CustomerCity,CustomerPostalCode,CustomerCommunicationCulture,CustomerCountryRegion,AzureLicenseType,PromotionalCustomers,CustomerState,CommerceRootCustomer,CustomerId,BillingAccountId,ID  FROM ISVCustomer TIMESPAN LAST_6_MONTHS`

## <a name="orders-report-query"></a>Requête de rapport commandes

**Description du rapport**: rapport commandes pour les six derniers mois

**QueryID** :  `fd0f299c-5a1c-4929-9f48-bfc6cc44355d`

**Requête de rapport** :

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,Sku,CustomerCountry,IsPreviewSKU,OrderId,OrderQuantity,CloudInstanceName,IsNewCustomer,OrderStatus,OrderCancelDate,CustomerCompanyName,CustomerName,OrderPurchaseDate,OfferName,TrialEndDate,CustomerId,BillingAccountId FROM ISVOrder TIMESPAN LAST_6_MONTHS`

## <a name="usage-report-queries"></a>Requêtes de rapport d’utilisation

**Description du rapport** : rapport d’utilisation normalisée des machines virtuelles pour les six derniers mois

**QueryID** :  `2c6f384b-ad52-4aed-965f-32bfa09b3778`

**Requête de rapport** :

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,NormalizedUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Description du rapport** : rapport d’utilisation brute des machines virtuelles pour les six derniers mois

**QueryID** :  `3f19fb95-5bc4-4ee0-872e-cedd22578512`

**Requête de rapport** :

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,RawUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Description du rapport** : rapport d’utilisation facturée à l’usage pour les six derniers mois

**QueryID** :  `f0c4927f-1f23-4c99-be4a-1371a5a9a086`

**Requête de rapport** :

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,MeteredUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('SaaS', 'Azure Applications') TIMESPAN LAST_6_MONTHS`

## <a name="marketplace-insights-report-query"></a>Requête d’insights de la place de marché

**Description du rapport**: rapport d’insights de la place de marché pour les six derniers mois

**QueryID** :  `6fd7624b-aa9f-42df-a61d-67d42fd00e92`

**Requête de rapport** :

`Date,OfferName,ReferralDomain,CountryName,PageVisits,GetItNow,ContactMe,TestDrive,FreeTrial FROM ISVMarketplaceInsights TIMESPAN LAST_6_MONTHS`

## <a name="next-steps"></a>Étapes suivantes

- [API permettant d’accéder aux données d’analyse de la place de marché commerciale](analytics-available-apis.md)
- [Exemple d’application permettant d’accéder aux données d’analyse de place de marché commerciale](analytics-sample-application.md)
