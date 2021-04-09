---
title: API pour accéder aux données d’analyse de place de marché commerciale
description: Utilisez ces API pour accéder aux données d’analyse par programme dans l’Espace partenaires.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: ed6d658155267ab21fd4cdd28dd50fcbb258ee90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583543"
---
# <a name="apis-for-accessing-commercial-marketplace-analytics-data"></a>API pour accéder aux données d’analyse de place de marché commerciale

Voici la liste des API permettant d’accéder aux données d’analyse de la place de marché commerciale ainsi que leurs fonctionnalités associées.

- [API d’extraction de jeux de données](#dataset-pull-apis)
- [API de gestion des requêtes](#query-management-apis)
- [API de gestion des rapports](#report-management-apis)
- [API d’extraction des exécutions de rapports](#report-execution-pull-apis)

## <a name="dataset-pull-apis"></a>API d’extraction de jeux de données

***Tableau 1 : API d’extraction de jeux de données***

| **API** | **Fonctionnalité** |
| --- | --- |
| [Obtenir tous les jeux de données](analytics-api-get-all-datasets.md) | Obtient tous les jeux de données disponibles. Les jeux de données répertorient les tables, les colonnes, les métriques et les intervalles de temps. |
|||

## <a name="query-management-apis"></a>API de gestion des requêtes

***Tableau 2 : API de gestion des requêtes***

| **API** | **Fonctionnalité** |
| --- | --- |
| [Créer une requête de rapport](analytics-programmatic-access.md#create-report-query-api) | Crée des requêtes personnalisées qui définissent le jeu de données à partir duquel les colonnes et les métriques doivent être exportées. |
| [Obtenir des requêtes de rapport](analytics-api-get-report-queries.md) | Obtient toutes les requêtes disponibles à utiliser dans les rapports. Obtient par défaut toutes les requêtes système et définies par l’utilisateur. |
| [Supprimer des requêtes de rapport](analytics-api-delete-report-queries.md) | Supprime les requêtes définies par l’utilisateur. |
|||

## <a name="report-management-apis"></a>API de gestion des rapports

***Tableau 3 : API de gestion des rapports***

| **API** | **Fonctionnalité** |
| --- | --- |
| [Créer un rapport](analytics-programmatic-access.md#create-report-api) | Planifie l’exécution d’une requête à intervalles réguliers. |
| [Essayer des requêtes de rapport](analytics-api-try-report-queries.md) | Exécute une instruction de requête de rapport. Renvoie uniquement 10 enregistrements qu’un partenaire peut utiliser pour vérifier si les données sont identiques à celles attendues. |
| [Obtenir le rapport](analytics-api-get-report.md) | Récupère tous les rapports planifiés. |
| [Mettre à jour le rapport](analytics-api-update-report.md) | Modifie un paramètre de rapport. |
| [Supprimer le rapport](analytics-api-delete-report.md) | Supprime tous les enregistrements de rapport et d’exécution de rapport. |
| [Mettre en pause les exécutions de rapport](analytics-api-pause-report-executions.md) | Met en pause l’exécution planifiée des rapports. |
| [Reprendre les exécutions de rapport](analytics-api-resume-report-executions.md) | Reprend l’exécution planifiée d’un rapport mis en pause. |
|||

## <a name="report-execution-pull-apis"></a>API d’extraction d’exécution de rapport

***Tableau 4 : API d’extraction d’exécution de rapport***

| **API** | **Fonctionnalité** |
| --- | --- |
| [Obtenir les exécutions de rapport](analytics-programmatic-access.md#get-report-executions-api) | Obtient toutes les exécutions intervenues pour un rapport donné. |
|||

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez tester les API à l’aide de l’[URL d’API Swagger](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html).
