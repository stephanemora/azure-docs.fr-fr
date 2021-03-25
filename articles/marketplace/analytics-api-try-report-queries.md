---
title: API Essayer des requêtes de rapport
description: Utilisez cette API afin d’exécuter une requête de rapport pour les rapports analytiques de place de marché commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 0db212be06182128bbd8a3bf694a2f893ce82eae
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583452"
---
# <a name="try-report-queries-api"></a>API Essayer des requêtes de rapport

Cette API exécute une instruction de requête de rapport. L’API retourne uniquement 10 enregistrements que vous pouvez utiliser, en votre qualité de partenaire, pour vérifier si les données sont identiques à celles attendues.

> [!IMPORTANT]
> Cette API a un délai d’expiration d’exécution de requête de 100 secondes. Si vous remarquez que l’API prend plus de 100 secondes, il est très probable que la requête soit syntaxiquement correcte, sinon vous auriez reçu un code d’erreur autre que 200. La génération du rapport réussit si la syntaxe de la requête est correcte.

**Syntaxe de la requête**

| **Méthode** | **URI de demande** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery={query text}` |
|||

**En-tête de requête**

| **En-tête** | **Type** | **Description** |
| --- | --- | --- |
| Autorisation | string | Obligatoire. Le jeton d’accès Azure Active Directory (Azure AD) au format `Bearer <token>` |
| Content-Type | string | `Application/JSON` |
|||

**QueryParameter**

| **Nom du paramètre** | **Type** | **Description** |
| --- | --- | --- |
| `exportQuery` | string | Chaîne de requête de rapport qui doit être exécutée |
| `queryId` | string | ID de requête existant valide |
|||

**Paramètre** **de chemin**

Aucun

**Charge utile de la requête**

Aucun

**Glossaire**

Aucune

**Réponse**

La charge utile de la réponse est structurée comme suit :

Code de réponse : 200, 400, 401, 403, 404, 500

Charge utile de la réponse : 10 premières lignes d’exécution de la requête
