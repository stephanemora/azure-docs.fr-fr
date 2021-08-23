---
title: API Reprendre les exécutions de rapport
description: Utilisez cette API pour reprendre l’exécution planifiée d’un rapport analytique de place de marché commerciale suspendu.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: caaf20a86e871e247f8aa6908672df8c36e8891d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532687"
---
# <a name="resume-report-executions-api"></a>API Reprendre les exécutions de rapport

Cette API, lorsqu’elle est exécutée, reprend l’exécution planifiée d’un rapport analytique de place de marché commerciale suspendu.

**Syntaxe de la requête**

| Méthode | URI de requête |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/resume/{reportId}` |
|||

**En-tête de requête**

| En-tête | Type | Description |
| ------------ | ------------- | ------------- |
| Autorisation | string | Obligatoire. Le jeton d’accès Azure Active Directory (Azure AD) au format `Bearer <token>` |
| Content-Type | string | `Application/JSON` |
||||

**Paramètre de chemin**

Aucun

**Paramètre de requête**

| Nom du paramètre | Obligatoire | Type | Description |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Oui | string | ID du rapport en cours de modification |
|||||

**Glossaire**

Aucune

**Réponse**

La charge utile de la réponse est structurée comme suit au format JSON :

Code de réponse : 200, 400, 401, 403, 404, 500

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
| `ReportId` | Identificateur unique universel (UUID) du rapport dont la reprise a été effectuée |
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
