---
title: API Mettre en pause les exécutions de rapport
description: Utilisez cette API pour mettre en pause l’exécution planifiée d’un rapport analytique de place de marché commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 39b535278fef42818f572631cfa1cb1f923930a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583510"
---
# <a name="pause-report-executions-api"></a>API Mettre en pause les exécutions de rapport

Cette API met en pause l’exécution planifiée des rapports.

**Syntaxe de la requête**

| Méthode | URI de requête |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/pause/{Report ID}` |
|||

**En-tête de requête**

| En-tête | Type | Description |
| ------------ | ------------- | ------------- |
| Autorisation | string | Obligatoire. Le jeton d’accès Azure Active Directory (Azure AD) au format `Bearer <token>` |
| Content-Type | string | `Application/JSON` |
||||

**Paramètre de chemin**

Aucun

**Paramètre de requête.**

| Nom du paramètre | Obligatoire | Type | Description |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Oui | string | ID du rapport en cours de modification |
|||||

**Glossaire**

Aucune

**Réponse**

La charge utile de la réponse est structurée comme suit au format JSON :

Code de réponse : 200, 400, 401, 403, 404, 500 Charge utile de réponse :

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
      "RecurrenceCount": 0,
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

| Paramètre | Description |
| ------------ | ------------- |
| `ReportId` | Identificateur unique universel (UUID) du rapport supprimé |
| `ReportName` | Nom donné au rapport lors de la création |
| `Description` | Description donnée lors de la création du rapport |
| `QueryId` | ID de requête passé au moment de la création du rapport |
| `Query` | Texte de requête qui sera exécuté pour ce rapport |
| `User` | ID d’utilisateur utilisé pour la création du rapport |
| `CreatedTime` | Heure de création du rapport. Le format d’heure est aaaa-MM-jjThh:mm:ssZ |
| `ModifiedTime` | Heure de dernière modification du rapport. Le format d’heure est aaaa-MM-jjThh:mm:ssZ |
| `StartTime` | Heure de début de l’exécution du rapport. Le format d’heure est aaaa-MM-jjThh:mm:ssZ |
| `ReportStatus` | État d’exécution du rapport. Les valeurs possibles sont Paused, Active et Inactive. |
| `RecurrenceInterval` | Intervalle de périodicité fourni lors de la création du rapport |
| `RecurrenceCount` | Nombre de récurrences fourni lors de la création du rapport |
| `CallbackUrl` | URL de rappel fournie dans la requête |
| `Format` | Format des fichiers de rapport |
|||
