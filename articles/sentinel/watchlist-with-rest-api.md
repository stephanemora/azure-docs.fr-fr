---
title: Gérer les listes de surveillance dans Azure Sentinel à l’aide de l’API REST | Microsoft Docs
description: Cet article décrit la manière de gérer les listes de surveillance Azure Sentinel à l’aide de l’API REST de Log Analytics pour créer, modifier et supprimer les listes de surveillance et leurs éléments.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2021
ms.author: yelevin
ms.openlocfilehash: ea571f9b033ba82709a13c6d32649f3228ee04b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98798008"
---
# <a name="manage-watchlists-in-azure-sentinel-using-rest-api"></a>Gérer les listes de surveillance dans Azure Sentinel à l’aide de l’API REST

> [!IMPORTANT]
> La fonctionnalité des listes de surveillance est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Azure Sentinel, construit en partie sur Azure Monitor Log Analytics, vous permet d’utiliser l’API REST de Log Analytics pour gérer des listes de surveillance. Ce document vous montre comment créer, modifier et supprimer des listes de surveillance et leurs éléments à l’aide de l’API REST.  Les listes de surveillance créées de cette façon s’affichent dans l’interface utilisateur d’Azure Sentinel.

## <a name="common-uri-parameters"></a>Paramètres URI communs

Voici les paramètres URI communs de toutes les commandes API de liste de surveillance :

| Nom | Dans | Obligatoire | Type | Description |
|-|-|-|-|-|
| **{subscriptionId}** | path | yes | GUID | ID d’abonnement Azure |
| **{resourceGroupName}** | path | yes | string | Nom du groupe de ressources dans l’abonnement |
| **{workspaceName}** | path | yes | string | Nom de l’espace de travail Log Analytics |
| **{watchlistAlias}** | path | oui* | string | Nom d’une liste de surveillance donnée<br>\* Facultatif lors de la récupération de toutes les listes de surveillance |
| **{watchlistItemId}** | path | oui** | GUID | ID de l’élément à créer, ajouter ou supprimer dans la liste de surveillance<br>\*\* Obligatoire uniquement pour les commandes d’élément de liste de surveillance |
| **{api-version}** | query | yes | string | Version du protocole utilisé pour faire cette demande Depuis le 29 octobre 2020, la version de l’API Watchlist est *2019-01-01-preview*. |
| **{bearerToken}** | autorisation | yes | token | Jeton d’autorisation du porteur |
|  

## <a name="retrieve-all-watchlists"></a>Récupérer tous les listes de surveillance

Cette commande récupère toutes les listes de surveillance associées à un espace de travail, sans leurs éléments.

### <a name="request-uri"></a>URI de requête
(L’URI correspond à une seule ligne, coupée pour en faciliter la lecture.)

| Méthode | URI de demande |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists?api-version={{api-version}}` |
|

### <a name="responses"></a>Réponses

| Code d’état | Response body | Description |
|-|-|-|
| 200 / OK | Liste des listes de surveillance existantes, ou vide si aucune liste de surveillance n’a été trouvé |  |
| 400 / Requête incorrecte |  | Syntaxe de requête incorrecte, paramètre de requête non valide, etc. |
|

## <a name="look-up-a-watchlist-by-name"></a>Rechercher une liste de surveillance par nom

Cette commande récupère une liste de surveillance spécifique associée à un espace de travail, sans ses éléments.

### <a name="request-uri"></a>URI de requête
(L’URI correspond à une seule ligne, coupée pour en faciliter la lecture.)

| Méthode | URI de demande |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Réponses

| Code d’état | Response body | Description |
|-|-|-|
| 200 / OK | Liste de surveillance demandée |  |
| 400 / Requête incorrecte |  | Syntaxe de requête incorrecte, paramètre de requête non valide, etc. |
| 404 / Introuvable |  | Aucune liste de surveillance portant le nom demandé n’a été trouvée |
|

## <a name="create-a-watchlist"></a>Créer une liste de surveillance

Cette commande crée une liste de surveillance et y ajoute des éléments. Elle prend deux appels à ce point de terminaison pour créer la liste de surveillance et ses éléments : le premier crée la liste de surveillance (parent), tandis que le second ajoute les éléments.

### <a name="request-uri"></a>URI de requête
(L’URI correspond à une seule ligne, coupée pour en faciliter la lecture.)

| Méthode | URI de requête |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>Corps de la demande

Voici un exemple de corps de la demande pour une demande de création de liste de surveillance :

```json
{
    "properties": {
        "displayName": "High Value Assets Watchlist",
        "source": "Local file",
        "provider": "Microsoft",
        "numberOfLinesToSkip":"1",
        "rawContent": "metadata line\nheader1, header2\nval1, val2",
        "contentType": "text/csv"
    }
}
```

### <a name="responses"></a>Réponses

| Code d’état | Response body | Description |
|-|-|-|
| 200 / OK | Liste de surveillance créée par la requête, sans éléments |  |
| 400 / Requête incorrecte |  | Syntaxe de requête incorrecte, paramètre de requête non valide, etc. |
| 409 / Conflit |  | L’opération a échoué, car il existe une liste de surveillance portant le même alias |
|

## <a name="delete-a-watchlist"></a>Supprimer une liste de surveillance

Cette commande supprime une liste de surveillance et ses éléments.

### <a name="request-uri"></a>URI de requête
(L’URI correspond à une seule ligne, coupée pour en faciliter la lecture.)

| Méthode | URI de demande |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Réponses

| Code d’état | Response body | Description |
|-|-|-|
| 200 / OK | Corps de réponse vide |  |
| 204 / Aucun contenu | Corps de réponse vide | Rien n’a été supprimé |
| 400 / Requête incorrecte |  | Syntaxe de requête incorrecte, paramètre de requête non valide, etc. |
|

## <a name="add-or-update-a-watchlist-item"></a>Ajouter ou mettre à jour un élément de liste de surveillance

Quand cette commande est exécutée sur un *watchlisItemId* (GUID) existant, elle met à jour l’élément avec les données fournies dans le corps de la demande. Dans le cas contraire, un nouvel élément est créé.

### <a name="request-uri"></a>URI de requête
(L’URI correspond à une seule ligne, coupée pour en faciliter la lecture.)

| Méthode | URI de requête |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>Corps de la demande

Voici un exemple de corps de la demande pour une demande d’ajout/mise à jour de liste de surveillance :

```json
{
    "properties": {
      "itemsKeyValue": {
           "Gateway subnet": "10.0.255.224/27",
           "Web Tier": "10.0.1.0/24",
           "Business tier": "10.0.2.0/24",
           "Private DMZ in": "10.0.0.0/27",
           "Public DMZ out": "10.0.0.96/27"
      }
}
}
```

### <a name="responses"></a>Réponses

| Code d’état | Response body | Description |
|-|-|-|
| 200 / OK | Élément de liste de surveillance créé ou mis à jour par la demande |  |
| 400 / Requête incorrecte |  | Syntaxe de requête incorrecte, paramètre de requête non valide, etc. |
| 409 / Conflit |  | L’opération a échoué, car il existe une liste de surveillance portant le même alias |
|

## <a name="delete-a-watchlist-item"></a>Supprimer un élément de liste de surveillance

Cette commande supprime un élément de liste de surveillance existant.

### <a name="request-uri"></a>URI de requête
(L’URI correspond à une seule ligne, coupée pour en faciliter la lecture.)

| Méthode | URI de demande |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Réponses

| Code d’état | Response body | Description |
|-|-|-|
| 200 / OK | Corps de réponse vide |  |
| 204 / Aucun contenu | Corps de réponse vide | Rien n’a été supprimé |
| 400 / Requête incorrecte |  | Syntaxe de requête incorrecte, paramètre de requête non valide, etc. |
|

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à gérer les listes de surveillance et leurs éléments dans Azure Sentinel à l’aide de l’API Log Analytics. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- En savoir plus sur les [listes de surveillance](watchlists.md)
- Explorer d’autres utilisations de l’[API Azure Sentinel](/rest/api/securityinsights/)