---
title: API Obtenir le rapport
description: Utilisez cette API pour obtenir les rapports analytiques planifiés dans l’Espace partenaires.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: d9248559f785ffb3b63e1f3d275e33a205cb682f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525187"
---
# <a name="get-report-api"></a>API Obtenir le rapport

Cette API permet d’obtenir tous les rapports planifiés.

**Syntaxe de la requête**

| **Méthode** | **URI de demande** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport?reportId={Report ID}&reportName={Report Name}&queryId={Query ID} ` |

**En-tête de requête**

| **En-tête** | **Type** | **Description** |
| --- | --- | --- |
| Autorisation | string | Obligatoire. Le jeton d’accès Azure Active Directory (Azure AD) au format `Bearer <token>` |
| Content-Type | string | `Application/JSON` |

**Paramètre de chemin**

Aucun

**Paramètre de requête**

| **Nom du paramètre** | **Obligatoire** | **Type** | **Description** |
| --- | --- | --- | --- |
| `reportId` | Non | string | Filtre permettant d’afficher uniquement les détails des rapports avec l’élément `reportId` indiqué dans cet argument |
| `reportName` | Non | string | Filtre permettant d’afficher uniquement les détails des rapports avec l’élément `reportName` indiqué dans cet argument |
| `queryId` | Non | boolean | Inclure les requêtes système prédéfinies dans la réponse |

**Glossaire**

Aucune

**Réponse**

La charge utile de la réponse est structurée au format JSON comme suit :

Code de réponse : 200, 400, 401, 403, 404, 500

Charge utile de réponse :

```json
{
  "Value": [
    {
      "ReportId": "string",
      "ReportName": "string",
      "Description": "string",
      "QueryId": "string",
      "Query": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string",
      "StartTime": "string",
      "ReportStatus": "string",
      "RecurrenceInterval": 0,
      " RecurrenceCount": 0,
      "CallbackUrl": "string",
      "Format": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**Glossaire**

Ce tableau liste les définitions clés des éléments dans la réponse.

| **Paramètre** | **Description** |
| --- | --- |
| `ReportId` | UUID unique du rapport créé |
| `ReportName` | Nom donné au rapport dans la charge utile de la requête |
| `Description` | Description fournie lors de la création du rapport |
| `QueryId` | ID de requête transmis au moment de la création du rapport |
| `Query` | Texte de requête qui sera exécuté pour ce rapport |
| `User` | ID d’utilisateur utilisé pour créer le rapport |
| `CreatedTime` | Heure de création du rapport. Le format d’heure est aaaa-MM-jjThh:mm:ssZ |
| `ModifiedTime` | Heure de dernière modification du rapport. Le format d’heure est aaaa-MM-jjThh:mm:ssZ |
| `StartTime` | Heure de début de l’exécution. Le format d’heure est aaaa-MM-jjThh:mm:ssZ |
| `ReportStatus` | État d’exécution du rapport. Les valeurs possibles sont Paused, Active et Inactive. |
| `RecurrenceInterval` | Intervalle de périodicité fourni lors de la création du rapport |
| `RecurrenceCount` | Nombre de récurrences fourni lors de la création du rapport |
| `CallbackUrl` | URL de rappel fournie dans la requête |
| `Format` | Format des fichiers de rapport |
