---
title: API Mettre à jour le rapport
description: Utilisez cette API pour signaler des paramètres pour les rapports analytiques de place de marché commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 38680eb291417ded4c2f93539e8d1ae091b1d560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583451"
---
# <a name="update-report-api"></a>API Mettre à jour le rapport

Cette API vous permet de modifier un paramètre de rapport.

**Syntaxe de la requête**

| Méthode | URI de requête |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
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

**Charge utile de demande**

```json
{
  "ReportName": "string",
  "Description": "string",
  "StartTime": "string",
  "RecurrenceInterval": 0,
  "RecurrenceCount": 0,
  "Format": "string",
  "CallbackUrl": "string"
}
```

**Glossaire**

Ce tableau liste les définitions clés des éléments dans la charge utile de la requête.

| Paramètre | Obligatoire | Description | Valeurs autorisées |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Oui | Nom à assigner au rapport | string |
| `Description` | Non | Description du rapport créé | string |
| `StartTime` | Oui | Horodatage à partir duquel la génération du rapport commence | string |
| `RecurrenceInterval` | Non | Fréquence à laquelle le rapport doit être généré, en heures. La valeur minimale est 4 | entier |
| `RecurrenceCount` | Non | Nombre de rapports à générer. La valeur par défaut est « illimité » | entier |
| `Format` | Oui | Format du fichier exporté. La valeur par défaut est CSV. | CSV/TSV |
| `CallbackUrl` | Oui | URL de rappel HTTPS à appeler lors de la génération du rapport | string |
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
| `ReportId` | Identificateur unique universel (UUID) du rapport en cours de mise à jour |
| `ReportName` | Nom donné au rapport dans la charge utile de la requête |
| `Description` | Description donnée lors de la création du rapport |
| `QueryId` | ID de requête passé au moment de la création du rapport |
| `Query` | Texte de requête qui sera exécuté pour ce rapport |
| `User` | ID d’utilisateur utilisé pour créer le rapport |
| `CreatedTime` | Heure de création du rapport. Le format d’heure est aaaa-MM-jjThh:mm:ssZ |
| `ModifiedTime` | Heure de dernière modification du rapport. Le format d’heure est aaaa-MM-jjThh:mm:ssZ |
| `StartTime` | Heure de début de l’exécution du rapport. Le format d’heure est aaaa-MM-jjThh:mm:ssZ |
| `ReportStatus` | État d’exécution du rapport. Les valeurs possibles sont Paused, Active et Inactive. |
| `RecurrenceInterval` | Intervalle de périodicité fourni lors de la création du rapport |
| `RecurrenceCount` | Nombre de récurrences fourni lors de la création du rapport |
| `CallbackUrl` | URL de rappel fournie dans la requête |
| `Format` | Format des fichiers de rapport |
|||
