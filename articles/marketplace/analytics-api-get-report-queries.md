---
title: API Obtenir des requêtes de rapport
description: Utilisez cette API pour obtenir toutes les requêtes pouvant être utilisées dans les rapports analytiques de la place de marché commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: f3a6351f726a5b1bd408764280ad2487cf7ecd95
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563095"
---
# <a name="get-report-queries-api"></a>API Obtenir des requêtes de rapport

L’API Obtenir des requêtes de rapport obtient toutes les requêtes pouvant être utilisées dans les rapports. Elle obtient toutes les requêtes système et définies par l’utilisateur par défaut.

**Syntaxe de la requête**

| **Méthode** | **URI de demande** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries?queryId={QueryID}&queryName={QueryName}&includeSystemQueries={include_system_queries}&includeOnlySystemQueries={include_only_system_queries}` |
|||

**En-tête de requête**

| **En-tête** | **Type** | **Description** |
| --- | --- | --- |
| Autorisation | string | Obligatoire. Le jeton d’accès Azure Active Directory (Azure AD) au format `Bearer <token>` |
| Content-Type | string | `Application/JSON` |
||||

**Paramètre de chemin**

Aucun

**Paramètre de requête**

| **Nom du paramètre** | **Type** | **Obligatoire** | **Description** |
| --- | --- | --- | --- |
| `queryId` | string | Non | Filtre permettant d’obtenir les détails des seules requêtes avec l’ID fourni dans l’argument |
| `queryName` | string | Non | Filtre permettant d’obtenir les détails des seules requête avec le nom founi dans l’argument |
| `IncludeSystemQueries` | boolean | Non | Inclure les requêtes système prédéfinies dans la réponse |
| `IncludeOnlySystemQueries` | boolean | Non | Inclure uniquement les requêtes système dans la réponse |
|||||

**Charge utile de requête**

Aucun

**Glossaire**

Aucune

**Réponse**

La charge utile de la réponse est structurée comme suit :

Code de réponse : 200, 400, 401, 403, 404, 500

Charge utile de réponse :

```json
{
  "Value": [
    {
      "QueryId": "string",
      "Name": "string",
      "Description": "string",
      "Query": "string",
      "Type": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**Glossaire**

Ce tableau répertorie les définitions clés des éléments dans la réponse.

| **Paramètre** | **Description** |
| --- | --- |
| `QueryId` | UUID unique de la requête |
| `Name` | Nom donné à la requête au moment de sa création |
| `Description` | Description donnée lors de la création de la requête |
| `Query` | Chaîne de requête de rapport |
| `Type` | Définir sur `userDefined` pour les requêtes créées par l’utilisateur et `system` pour les requêtes système prédéfinies |
| `User` | ID de l’utilisateur qui a créé la requête |
| `CreatedTime` | Heure de création de la requête |
| `TotalCount` | Nombre de jeux de données dans le tableau de valeur |
| `StatusCode` | Code de résultat. Les valeurs possibles sont 200, 400, 401, 403, 500 |
|||
