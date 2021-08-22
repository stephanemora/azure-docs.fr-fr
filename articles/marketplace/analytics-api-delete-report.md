---
title: API Supprimer le rapport
description: Utilisez cette API afin de supprimer tous les enregistrements de rapport et d’exécution de rapport pour les rapports analytiques de place de marché commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 9cb8f12061e53a89fa0380d717c0ad947515ab5f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532618"
---
# <a name="delete-report-api"></a>API Supprimer le rapport

Lors de son exécution, cette API supprime tous les enregistrements de rapport et d’exécution de rapport.

**Syntaxe de la requête**

| Méthode | URI de demande |
| ------------ | ------------- |
| Suppression | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
|||

**En-tête de requête**

| En-tête | Type | Description |
| ------------ | ------------- | ------------- |
| Autorisation | string | Obligatoire. Le jeton d’accès Azure AD au format `Bearer <token>` |
| Type de contenu | string | `Application/JSON` |
||||

**Paramètre de chemin**

Aucun

**Paramètre de requête**

| Nom du paramètre | Obligatoire | string | Description |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Oui | string | ID du rapport en cours de modification |
|||||

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
| `ReportId` | UUID unique du rapport supprimé |
| `ReportName` | Nom donné au rapport lors de la création |
| `Description` | Description donnée lors de la création du rapport |
| `QueryId` | ID de requête passé au moment de la création du rapport |
| `Query` | Texte de requête qui sera exécuté pour ce rapport |
| `User` | ID d’utilisateur utilisé pour créer le rapport |
| `CreatedTime` | Heure de création du rapport. Le format d’heure est aaaa-MM-jjThh:mm:ssZ |
| `ModifiedTime` | Heure de dernière modification du rapport. Le format d’heure est aaaa-MM-jjThh:mm:ssZ |
| `StartTime` | Heure de début de l’exécution du rapport. Le format d’heure est aaaa-MM-jjThh:mm:ssZ |
| `ReportStatus` | État de l’exécution du rapport. Les valeurs possibles sont Paused, Active et Inactive. |
| `RecurrenceInterval` | Intervalle de périodicité fourni lors de la création du rapport |
| `RecurrenceCount` | Nombre de récurrences fourni lors de la création du rapport |
| `CallbackUrl` | URL de rappel fournie dans la requête |
| `Format` | Format des fichiers de rapport |
|||
