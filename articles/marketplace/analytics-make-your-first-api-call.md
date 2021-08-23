---
title: Effectuer votre premier appel d’API pour accéder aux données d’analyse de la place de marché commerciale
description: Exemples pour apprendre à utiliser l’API pour accéder aux données d’analyse de la place de marché commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: f4437782f46e3144b3917a16fec14c9e92dfd303
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563168"
---
# <a name="make-your-first-api-call-to-access-commercial-marketplace-analytics-data"></a>Effectuer votre premier appel d’API pour accéder aux données d’analyse de la place de marché commerciale

Pour obtenir la liste des API permettant d’accéder aux données d’analyse de la place de marché commerciale, consultez [API pour accéder aux données d’analyse de la place de marché commerciale](analytics-available-apis.md). Avant d’effectuer votre premier appel d’API, veillez à respecter les [conditions préalables](analytics-prerequisites.md) pour accéder par programme aux données d’analyse de la place de marché commerciale.

## <a name="token-generation"></a>Génération de jetons

Avant d’appeler l’une des méthodes, vous devez obtenir un jeton d’accès Azure Active Directory (Azure AD). Vous devez transmettre le jeton d’accès Azure AD à l’en-tête d’autorisation de chaque méthode de l’API. Après avoir obtenu un jeton d’accès, vous disposez de 60 minutes pour l’utiliser avant qu’il n’expire. Après expiration, vous pouvez l’actualiser pour pouvoir continuer à l’utiliser dans de nouveaux appels à l’API.

Reportez-vous à l’exemple de requête ci-dessous pour générer un jeton. Les trois valeurs requises pour générer le jeton sont `clientId`, `clientSecret` et `tenantId`. Le paramètre `resource` doit être défini sur `https://graph.windows.net`.

***Exemple de requête*** :

```json
curl --location --request POST 'https://login.microsoftonline.com/{TenantId}/oauth2/token' \
--header 'return-client-request-id: true' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'resource=https://graph.windows.net' \
--data-urlencode 'client_id={client_id}' \
--data-urlencode 'client_secret={client_secret}' \
--data-urlencode 'grant_type=client_credentials'
```

***Exemple de réponse*** :

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1612794445",
    "not_before": "1612790545",
    "resource": "https://graph.windows.net",
    "access_token": {Token}
}
```

Pour plus d’informations sur la façon d’obtenir un jeton Azure AD pour votre application, consultez [Accéder aux données d’analyse à l’aide des services de magasin](/windows/uwp/monetize/access-analytics-data-using-windows-store-services#step-2-obtain-an-azure-ad-access-token).

## <a name="programmatic-api-call"></a>Appel d’API programmatique

Après avoir obtenu le jeton Azure AD comme décrit dans la section précédente, suivez ces étapes pour créer votre premier rapport d’accès programmatique.

Les données peuvent être téléchargées à partir des jeux de données suivants (datasetName) :

- ISVCustomer
- ISVMarketplaceInsights
- ISVUsage
- ISVOrder

Dans les sections suivantes, nous verrons des exemples montrant comment accéder par programme à `OrderId` à partir du jeu de données ISVOrder.

### <a name="step-1-make-a-rest-call-using-the-get-datasets-api"></a>Étape 1 : Effectuer un appel REST à l’aide de l’API Obtenir des jeux de données

La réponse de l’API fournit le nom du jeu de données à partir duquel vous pouvez télécharger le rapport. Pour le jeu de données spécifique, la réponse de l’API fournit également la liste des colonnes sélectionnables pouvant être utilisées pour votre modèle de rapport personnalisé. Vous pouvez également consulter les tableaux suivants pour connaître les noms des colonnes :

- [Tableau Détails des commandes](orders-dashboard.md#orders-details-table)
- [Tableau des détails de l’utilisation](usage-dashboard.md#usage-details-table)
- [Tableau des détails des commandes](customer-dashboard.md#customer-details-table)
- [Insights sur la place de marché – Tableau des détails](insights-dashboard.md#marketplace-insights-details-table)

***Exemple de requête*** :

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset ' \ 
--header 'Authorization: Bearer <AzureADToken>'
```

***Exemple de réponse*** :

```json
{
    "value": [
        {
            "datasetName": "ISVOrder",
            "selectableColumns": [
                "MarketplaceSubscriptionId",
                "MonthStartDate",
                "OfferType",
                "AzureLicenseType",
                "Sku",
                "CustomerCountry",
                "IsPreviewSKU",
                "OrderId",
                "OrderQuantity",
                "CloudInstanceName",
                "IsNewCustomer",
                "OrderStatus",
                "OrderCancelDate",
                "CustomerCompanyName",
                "CustomerName",
                "OrderPurchaseDate",
                "OfferName",
                "TrialEndDate",
                "CustomerId",
                "BillingAccountId"
            ],
            "availableMetrics": [],
            "availableDateRanges": [
                "LAST_MONTH",
                "LAST_3_MONTHS",
                "LAST_6_MONTHS",
                "LIFETIME"
            ]
        },
    ],
    "totalCount": 1,
    "message": "Dataset fetched successfully",
    "statusCode": 200
}
```

### <a name="step-2-create-the-custom-query"></a>Étape 2 : Créer la requête personnalisée

Dans cette étape, nous allons utiliser l’ID de commande du rapport Commandes pour créer une requête personnalisée correspondant au rapport souhaité. La valeur par défaut `timespan`, si elle n’est pas spécifiée dans la requête, est de six mois.

***Exemple de requête*** :

```json
curl 
--location 
--request POST ' https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries' \ 
--header ' Authorization: Bearer <AzureAD_Token>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
            '{ 
                "Query": "SELECT OrderId from ISVOrder", 
                "Name": "ISVOrderQuery1", 
                "Description": "Get a list of all Order IDs" 
             }'
```

***Exemple de réponse*** :

```json
{
    "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": "ISVOrderQuery1",
            "description": "Get a list of all Order IDs”,
            "query": "SELECT OrderId from ISVOrder",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34",
            "modifiedTime": null
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

Si l’exécution de la requête aboutit, un `queryId` est généré et doit être utilisé pour générer le rapport.

### <a name="step-3-execute-test-query-api"></a>Étape 3 : Exécuter l’API de requête de test

Dans cette étape, nous allons utiliser l’API de requête de test afin d’obtenir les 100 premières lignes pour la requête créée.

***Exemple de requête*** :

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery=SELECT%20OrderId%20from%20ISVOrder' \ 
--header ' Authorization: Bearer <AzureADToken>'
```

***Exemple de réponse*** :

```json
{
    "value": [
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2ba8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bb8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bc8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bd8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2be8"
        },
               .
               .
               .

        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf0"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf1"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf2"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf3"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf4"
        }
    ],
    "totalCount": 100,
    "message": null,
    "statusCode": 200
}
```

### <a name="step-4-create-the-report"></a>Étape 4 : Créer le rapport

Dans cette étape, nous allons utiliser le `QueryId` généré précédemment pour créer le rapport.

***Exemple de requête*** :

```json
curl 
--location 
--request POST 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport' \ 
--header ' Authorization: Bearer <AzureADToken>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
                 '{
                   “ReportName": "ISVReport1",
                   "Description": "Report for getting list of Order Ids",
                   "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
                   "StartTime": "2021-01-06T19:00:00Z”,
                   "RecurrenceInterval": 48,
                   "RecurrenceCount": 20,
                    "Format": "csv"
                  }'
```

<br>

_**Tableau 1 : Description des paramètres utilisés dans cet exemple de requête**_

| Paramètre | Description |
| ------------ | ------------- |
| `Description` | Fournissez une brève description du rapport en cours de génération. |
| `QueryId` | Il s’agit du `queryId` généré lors de la création de la requête à l’Étape 2 : Créer une requête personnalisée. |
| `StartTime` | Heure de début de la première exécution du rapport. |
| `RecurrenceInterval` | Intervalle de périodicité fourni lors de la création du rapport |
| `RecurrenceCount` | Nombre de récurrences fourni lors de la création du rapport |
| `Format` | Les formats de fichier CSV et TSV sont pris en charge. |
|||

***Exemple de réponse*** :

```json
{
    "value": [
        {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVReport1",
            "description": "Report for getting list of Order Ids",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT OrderId from ISVOrder",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv"
        }
    ],
    "totalCount": 1,
    "message": "Report created successfully",
    "statusCode": 200
}
```

Si l’exécution aboutit, un `reportId` est généré et doit être utilisé pour planifier un téléchargement du rapport.

### <a name="step-5-execute-report-executions-api"></a>Étape 5 : Exécuter l’API des exécutions de rapport

Pour obtenir l’emplacement sécurisé (URL) du rapport, nous allons maintenant exécuter l’API des exécutions de rapport.

***Exemple de requête*** :

```json
Curl
--location
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/72fa95ab-35f5-4d44-a1ee-503abbc88003' \
--header ' Authorization: Bearer <AzureADToken>' \
```

***Exemple de réponse*** :

```json
{
    "value": [
        {
            "executionId": "1f18b53b-df30-4d98-85ee-e6c7e687aeed",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Pending",
            "reportAccessSecureLink": null,
            "reportExpiryTime": null,
            "reportGeneratedTime": null
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez tester les API par le biais de l’[URL d’API Swagger](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html)
- [Paradigme de l’accès programmatique](analytics-programmatic-access.md)
