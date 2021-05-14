---
title: Paradigme de l’accès programmatique
description: Comprendre le flux général du modèle d’appel d’API pour l’analytique programmatique. Les API permettant d’accéder aux rapports analytiques sur la place de marché commerciale Microsoft sont également décrites.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 907b3020091de1be9a037d149de737c14de125ad
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108207336"
---
# <a name="programmatic-access-paradigm"></a>Paradigme de l’accès programmatique

Ce diagramme montre le modèle d’appel d’API utilisé pour créer un nouveau modèle de rapport, planifier le rapport personnalisé et récupérer les données d’échec.

[![Illustre le modèle d’appel d’API utilisé pour créer un modèle de rapport, planifier le rapport personnalisé et récupérer les données d’échec.](./media/analytics-programmatic-access/analytics-high-level-flow.png)](./media/analytics-programmatic-access/analytics-high-level-flow.png#lightbox) 
 ***Figure 1 : flux général du modèle d’appel d’API***

Cette liste fournit plus de détails sur la Figure 1.

1. L’application cliente peut définir le schéma/modèle de rapport personnalisé en appelant l'[API Créer une requête de rapport](#create-report-query-api). Vous pouvez également utiliser un modèle de rapport (`QueryId`) à partir de [la liste des requêtes système](analytics-system-queries.md).
1. En cas de réussite, l’API Créer un modèle de rapport renvoie `QueryId`.
1. L’application cliente appelle ensuite l'[API Créer un rapport ](#create-report-api) à l’aide de `QueryID` avec la date de début du rapport, l’intervalle de répétition, la périodicité et un URI de rappel facultatif.
1. En cas de réussite, l'[API Créer un rapport](#create-report-api) renvoie le `ReportID`.
1. L’application cliente est avertie au niveau de l’URI de rappel dès que les données du rapport sont prêtes à être téléchargées.
1. L’application cliente utilise ensuite l'[API Obtenir des exécutions de rapport](#get-report-executions-api) pour interroger l’état du rapport avec `Report ID` et la plage de dates.
1. En cas de réussite, le lien de téléchargement du rapport est renvoyé et l’application peut lancer le téléchargement des données.

## <a name="report-query-language-specification"></a>Spécification du langage de requête de rapport

Bien que nous fournissions des [requêtes système](analytics-system-queries.md) à utiliser pour créer des rapports, vous pouvez également créer vos propres requêtes en fonction des besoins de votre entreprise. Pour en savoir plus sur les requêtes personnalisées, consultez [Spécification de requête personnalisée](analytics-custom-query-specification.md).

## <a name="create-report-query-api"></a>API Créer une requête de rapport

Cette API permet de créer des requêtes personnalisées qui définissent le jeu de données à partir duquel les colonnes et les mesures doivent être exportées. L’API offre la flexibilité nécessaire pour créer un nouveau modèle de rapport basé sur les besoins de votre entreprise.

Vous pouvez également utiliser les [requêtes système](analytics-system-queries.md) que nous fournissons. Si les modèles de rapport personnalisés ne sont pas nécessaires, vous pouvez appeler l'[API Créer un rapport](#create-report-api) directement à l’aide des [QueryIds](analytics-system-queries.md) des requêtes système que nous fournissons.

L’exemple suivant montre comment créer une requête personnalisée pour obtenir une _utilisation normalisée et des frais financiers estimés pour les références SKU payantes_ à partir du jeu de données [ISVUsage](analytics-make-your-first-api-call.md#programmatic-api-call) pour le mois passé.

*Syntaxe de la requête*

| Méthode | URI de demande |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries` |
|||

*En-tête de requête*

| En-tête | Type | Description |
| ------------- | ------------- | ------------- |
| Autorisation | string | Obligatoire. Jeton d’accès Azure Active Directory (Azure AD). Le format est `Bearer <token>`. |
| Content-Type | `string` | `application/JSON` |
||||

*Paramètre de chemin*

Aucun

*Paramètre de requête.*

Aucun

*Exemple de charge utile de requête*

```json
{
    "Name": "ISVUsageQuery",
    "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
    "Query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"
}
```

*Glossaire*

Ce tableau fournit les définitions clés des éléments dans la charge utile de requête.

| Paramètre | Obligatoire | Description | Valeurs autorisées |
| ------------ | ------------- | ------------- | ------------- |
| `Name` | Oui | Nom convivial de la requête | string |
| `Description` | Non | Description de ce que la requête renvoie | string |
| `Query` | Oui | Chaîne de requête de rapport | Type de données : chaîne<br>[Requête personnalisée](analytics-sample-queries.md) basée sur les besoins de l’entreprise |
|||||

> [!NOTE]
> Pour obtenir des exemples de requêtes personnalisées, consultez [Exemples de requêtes](analytics-sample-queries.md).

*Exemple de réponse*

La charge utile de la réponse est structurée comme suit :

Codes de réponse : 200, 400, 401, 403, 500

Exemple de charge utile de réponse :

```json
{
  "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": " ISVUsageQuery",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs”,
            "query": " SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34Z"
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

*Glossaire*

Ce tableau fournit les définitions clés des éléments dans la réponse.

| Paramètre | Description |
| ------------ | ------------- |
| `QueryId` | Identificateur unique universel (UUID) de la requête créée |
| `Name` | Nom convivial donné à la requête dans la charge utile de requête |
| `Description` | Description donnée lors de la création de la requête |
| `Query` | Requête de rapport transmise en tant qu’entrée lors de la création de la requête |
| `Type` | Paramètre à définir sur `userDefined` |
| `User` | ID d’utilisateur utilisé pour créer la requête |
| `CreatedTime` | Heure UTC de création de la requête au format suivant : aaaa-MM-jjThh:mm:ssZ |
| `TotalCount` | Nombre de jeux de données dans le tableau de valeur |
| `StatusCode` | Code de résultat<br>Les valeurs possibles sont 200, 400, 401, 403, 500 |
| `message` | Message d’état d’’exécution de l’API |
|||

## <a name="create-report-api"></a>API Créer un rapport

Lors de la création d’un modèle de rapport personnalisé et de la réception de `QueryID` dans la réponse [Créer une requête de rapport](#create-report-query-api), cette API peut être appelée pour planifier l’exécution d’une requête à intervalles réguliers. Vous pouvez définir une fréquence et une planification pour le rapport à remettre. Pour les requêtes système que nous fournissons, l’API Créer un rapport peut également être appelée avec [QueryId](analytics-sample-queries.md).

*Syntaxe de la requête*

| Méthode | URI de demande |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport` |
|||

*En-tête de requête*

| En-tête | Type | Description |
| ------ | ---- | ----------- |
| Autorisation | string | Obligatoire. Jeton d’accès Azure Active Directory (Azure AD). Le format est `Bearer <token>`. |
| Type de contenu | string | `application/JSON` |
||||

*Paramètre de chemin*

Aucun

*Paramètre de requête.*

Aucun

*Exemple de charge utile de requête*

```json
{
  "ReportName": "ISVUsageReport",
  "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
  "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c ",
  "StartTime": "2021-01-06T19:00:00Z ",
  "RecurrenceInterval": 48,
  "RecurrenceCount": 20,
  "Format": "csv",
  "CallbackUrl": "https://<SampleCallbackUrl>"
}
```

*Glossaire*

Ce tableau fournit les définitions clés des éléments dans la charge utile de requête.

| Paramètre | Obligatoire | Description | Valeurs autorisées |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Oui | Nom attribué au rapport. | string |
| `Description` | Non | Description du rapport créé | string |
| `QueryId` | Oui | ID de equête de rapport | string |
| `StartTime` | Oui | Horodatage UTC à partir duquel la génération du rapport commence.<br>Le format doit être : aaaaa-MM-jjTHH:mm:ssZ | string |
| `RecurrenceInterval` | Oui | Fréquence en heures à laquelle le rapport doit être généré.<br>La valeur minimale est 4 et la valeur maximale 90. | entier |
| `RecurrenceCount` | Non | Nombre de rapports à générer. | entier |
| `Format` | Non | Format du fichier exporté.<br>Le format par défaut est. CSV. | CSV/TSV |
| `CallbackUrl` | Non | URL publiquement accessible pouvant être configurée en tant que destination de rappel. | Chaîne (URL http) |
| `ExecuteNow` | Non | Ce paramètre doit être utilisé pour créer un rapport à exécution unique. `StartTime`, `RecurrenceInterval` et `RecurrenceCount` sont ignorés s’il est défini sur `true`. Le rapport est immédiatement exécuté de manière asynchrone. | true/false |
| `QueryStartTime` | Non | (Facultatif) Spécifie l’heure de début de la requête qui extrait les données. Ce paramètre s’applique uniquement à un rapport à exécution ponctuelle dont la valeur `ExecuteNow` est définie sur `true`. Le format doit être aaaa-MM-jjTHH:mm:ssZ | Timestamp sous forme de chaîne |
| `QueryEndTime` | Non | (Facultatif) Spécifie l’heure de fin de la requête qui extrait les données. Ce paramètre s’applique uniquement à un rapport à exécution ponctuelle dont la valeur `ExecuteNow` est définie sur `true`. Le format doit être aaaa-MM-jjTHH:mm:ssZ | Timestamp sous forme de chaîne |
|||||

*Exemple de réponse*

La charge utile de la réponse est structurée comme suit :

Code de réponse : 200, 400, 401, 403, 404, 500

Charge utile de réponse :

```json
{
  "Value": [
    {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVUsageReport",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": "https://<SampleCallbackUrl>",
            "format": "csv"    
    }
  ],
  "TotalCount": 1,
  "Message": "Report created successfully",
  "StatusCode": 200
}
```

*Glossaire*

Ce tableau fournit les définitions clés des éléments dans la réponse.

| Paramètre | Description |
| ------------ | ------------- |
| `ReportId` | Identificateur unique universel (UUID) du rapport créé |
| `ReportName` | Nom donné au rapport dans la charge utile de la requête |
| `Description` | Description donnée lors de la création du rapport |
| `QueryId` | ID de requête transmis au moment où vous avez créé le rapport |
| `Query` | Texte de requête qui sera exécuté pour ce rapport |
| `User` | ID d’utilisateur utilisé pour créer le rapport |
| `CreatedTime` | Heure UTC de création du rapport au format suivant : aaaa-MM-jjTHH:mm:ssZ |
| `ModifiedTime` | Heure UTC de dernière modification du rapport au format suivant : aaaa-MM-jjTHH:mm:ssZ |
| `StartTime` | Heure UTC de début d’exécution du rapport au format suivant : aaaa-MM-jjTHH:mm:ssZ |
| `ReportStatus` | État d’exécution du rapport. Les valeurs possibles sont **Paused**, **Active** et **Inactive**. |
| `RecurrenceInterval` | Intervalle de périodicité fourni lors de la création du rapport |
| `RecurrenceCount` | Nombre de récurrences fourni lors de la création du rapport |
| `CallbackUrl` | URL de rappel fournie dans la requête |
| `Format` | Format des fichiers de rapport Les valeurs possibles sont CSV ou TSV. |
| `TotalCount` | Nombre de jeux de données dans le tableau de valeur |
| `StatusCode` | Code de résultat<br>Les valeurs possibles sont 200, 400, 401, 403, 500 |
| `message` | Message d’état d’’exécution de l’API |
|||

## <a name="get-report-executions-api"></a>API Obtenir les exécutions de rapport

Vous pouvez utiliser cette méthode pour interroger l’état d’exécution d’un rapport à l’aide du `ReportId` reçu à partir de l'[API Créer un rapport](#create-report-api). La méthode renvoie le lien de téléchargement du rapport si le rapport est prêt à être téléchargé. Sinon, la méthode renvoie l’état. Vous pouvez également utiliser cette API pour obtenir toutes les exécutions intervenues pour un rapport donné.

> [!IMPORTANT]
> Les paramètres de requête par défaut de cette API sont définis pour `executionStatus=Completed` et `getLatestExecution=true`. Par conséquent, l’appel de l’API avant la première exécution réussie du rapport renvoie 404. Les exécutions en attente peuvent être obtenues en définissant `executionStatus=Pending`.

*Syntaxe de la requête*

| Méthode | URI de demande |
| ------------ | ------------- |
| Obtenir | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/{reportId}?executionId={executionId}&executionStatus={executionStatus}&getLatestExecution={getLatestExecution}` |
|||

*En-tête de requête*

| En-tête | Type | Description |
| ------ | ------ | ------ |
| Autorisation | string | Obligatoire. Jeton d’accès Azure Active Directory (Azure AD). Le format est `Bearer <token>`. |
| Type de contenu | string | `application/json` |
||||

*Paramètre de chemin*

Aucun

*Paramètre de requête.*

| Nom du paramètre | Obligatoire | Type | Description |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Oui | string | Filtre permettant d’obtenir les détails d’exécution des rapports avec `reportId` fourni dans cet argument. Plusieurs `reportIds` peuvent être spécifiés en les séparant par un point-virgule « ; ». |
| `executionId` | Non | string | Filtre permettant d’obtenir les détails des seuls rapports avec `executionId` fourni dans cet argument. Plusieurs `executionIds` peuvent être spécifiés en les séparant par un point-virgule « ; ». |
| `executionStatus` | Non | chaîne/énumération | Filtre permettant d’obtenir les détails des seuls rapports avec `executionStatus` fourni dans cet argument.<br>Les valeurs valides sont `Pending`, `Running`, `Paused` et `Completed`. <br>La valeur par défaut est `Completed`. Plusieurs états peuvent être spécifiés en les séparant par un point-virgule « ; ». |
| `getLatestExecution` | Non | boolean | L’API renvoie les détails d’exécution du rapport le plus récent.<br>Par défaut, ce paramètre a la valeur `true`. Si vous choisissez de transmettre la valeur de ce paramètre en tant que `false`, l’API renvoie les instances d’exécution des 90 derniers jours. |
|||||

*Charge utile de demande*

Aucun

*Exemple de réponse*

La charge utile de la réponse est structurée comme suit :

Codes de réponse : 200, 400, 401, 403, 404, 500

Exemple de charge utile de réponse :

```json
{
    "value": [
        {
            "executionId": "a0bd78ad-1a05-40fa-8847-8968b718d00f",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 4,
            "recurrenceCount": 10,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Completed",
            "reportAccessSecureLink": "https://<path to report for download>",
            "reportExpiryTime": null,
            "reportGeneratedTime": "2021-01-13T14:40:46Z"
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

Une fois l’exécution du rapport terminée, l’état d’exécution `Completed` est affiché. Vous pouvez télécharger le rapport en sélectionnant l’URL dans le `reportAccessSecureLink`.

*Glossaire*

Définitions clés des éléments dans la réponse.

| Paramètre | Description |
| ------------ | ------------- |
| `ExecutionId` | Identificateur unique universel (UUID) de l’instance d’exécution |
| `ReportId` | ID de rapport associé à l’instance d’exécution |
| `RecurrenceInterval` | Intervalle de périodicité fourni lors de la création du rapport |
| `RecurrenceCount` | Nombre de récurrences fourni lors de la création du rapport |
| `CallbackUrl` | URL de rappel associée à l’instance d’exécution |
| `Format` | Format du fichier généré à la fin de l’exécution |
| `ExecutionStatus` | État de l’instance d’exécution du rapport.<br>Les valeurs valides sont `Pending`, `Running`, `Paused` et `Completed`. |
| `ReportAccessSecureLink` | Lien permettant d’accéder en toute sécurité au rapport |
| `ReportExpiryTime` | Heure UTC après laquelle le lien du rapport expire au format suivant : aaaa-MM-jjTHH:mm:ssZ |
| `ReportGeneratedTime` | Heure UTC à laquelle le rapport a été généré au format suivant : aaaa-MM-jjTHH:mm:ssZ |
| `TotalCount` | Nombre de jeux de données dans le tableau de valeur |
| `StatusCode` | Code de résultat <br>Les valeurs possibles sont 200, 400, 401, 403, 404 et 500. |
| `message` | Message d’état d’’exécution de l’API |
|||

## <a name="next-steps"></a>Étapes suivantes
- Vous pouvez tester les API par le biais de l’[URL d’API Swagger](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html)
- [Prise en main de l’accès programmatique aux données d’analyse](analytics-get-started.md)
