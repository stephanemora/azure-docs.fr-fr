---
title: Exemples de requêtes pour l’analytique programmatique
description: Utilisez ces exemples de requêtes pour accéder par programme aux données d’analyse de la place de marché commerciale Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7d788448fb3f8a849f79e43fcb0737898f4c9e15
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583502"
---
# <a name="sample-queries-for-programmatic-analytics"></a>Exemples de requêtes pour l’analytique programmatique

Cet article fournit des exemples de requêtes pour les rapports relatifs aux commandes, à l’utilisation et aux clients de la place de marché commerciale Microsoft. Vous pouvez utiliser ces requêtes en appelant le point de terminaison de l’API [Créer une requête de rapport](analytics-programmatic-access.md#create-report-query-api). Si nécessaire, l’appel de l’API [Créer une requête de rapport](analytics-programmatic-access.md#create-report-query-api) peut être modifié pour ajouter d’autres colonnes, ajuster la période de calcul et ajouter des conditions de filtre. Les périodes prises en charge sont les suivantes : six mois (6M), 12 mois (12M) et une période personnalisée.

Pour plus d’informations sur les noms des colonnes, les attributs et leur description, reportez-vous aux tableaux suivants :

- [Tableau des détails des commandes](customer-dashboard.md#customer-details-table)
- [Tableau Détails des commandes](orders-dashboard.md#orders-details-table)
- [Tableau des détails de l’utilisation](usage-dashboard.md#usage-details-table)

## <a name="customers-report-queries"></a>Requêtes de rapport clients

Ces exemples de requêtes s’appliquent au rapport clients.

| **Description de la requête** | **Exemple de requête** |
| --- | --- |
| Clients actifs du partenaire jusqu’à la date choisie | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 1` |
| Clients perdus du partenaire jusqu’à la date choisie | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 0` |
| Liste des nouveaux clients d’une zone géographique spécifique au cours des six derniers mois | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE DateAcquired <= ‘2020-06-30’ AND CustomerCountryRegion = ‘United States’` |
|||

## <a name="usage-report-queries"></a>Requêtes de rapport d’utilisation

Ces exemples de requêtes s’appliquent au rapport d’utilisation.

| **Description de la requête** | **Exemple de requête** |
| --- | --- |
| Utilisation normalisée des machines virtuelles pour le type de licence de place de marché « Facturé via Azure » pour les six derniers mois | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Utilisation brute des machines virtuelles pour le type de licence de la place de marché « Facturé via Azure » pour les 12 derniers mois | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_1_YEAR` |
| Utilisation normalisée des machines virtuelles pour le type de licence de la place de marché « BYOL (apportez votre propre licence) » pour les six derniers mois | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Utilisation brute des machines virtuelles pour le type de licence de la place de marché « BYOL (apportez votre propre licence) » pour les six derniers mois | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| En fonction de la date d’utilisation, utilisation normalisée totale quotidienne et des « Frais étendus estimés (PC/CC) » pour les plans payants pour le mois passé | `SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST_MONTH` |
| En fonction de la date d’utilisation, utilisation brute totale quotidienne et des « Frais étendus estimés (PC/CC) » pour les plans payants pour le mois passé | `SELECT UsageDate, RawUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST\_MONTH` |
| Pour un nom d’offre spécifique, utilisation normalisée des machines virtuelles pour le type de licence « Facturé via Azure » pour les six derniers mois | `SELECT OfferName, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Pour un nom d’offre spécifique, utilisation facturée à l’usage pour les six derniers mois | `SELECT OfferName, MeteredUsage FROM ISVUsage WHERE OfferName = ‘Example Offer Name’ AND OfferType IN (‘SaaS’, ‘Azure Applications’) TIMESPAN LAST_6_MONTHS` |
|||

## <a name="orders-report-queries"></a>Requêtes de rapport commandes

Ces exemples de requêtes s’appliquent au rapport commandes.

| **Description de la requête** | **Exemple de requête** |
| --- | --- |
| Rapport commandes pour le type de licence Azure « Enterprise » pour les six derniers mois | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Enterprise’ TIMESPAN LAST\_6\_MONTHS` |
| Rapport commandes pour le type de licence Azure « Paiement à l’utilisation » pour les six derniers mois | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Pay as You Go’ TIMESPAN LAST_6_MONTHS` |
| Rapport commandes pour le nom de l’offre spécifique pour les six derniers mois | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Rapport commandes pour les commandes actives pour les six derniers mois | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| Rapport commandes pour les commandes annulées pour les six derniers mois | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Cancelled’ TIMESPAN LAST_6_MONTHS` |
|||

## <a name="next-steps"></a>Étapes suivantes

- [API pour accéder aux données d’analytique de place de marché commerciale](analytics-available-apis.md)
