---
title: Affichage et analyse de données dans Azure Log Analytics | Microsoft Docs
description: Assistance pour les utilisateurs qui passent de la recherche dans les journaux Log Analytics aux journaux Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: bwren
ms.openlocfilehash: 89811e95ec24eb354020dd6384f6fdab6cee8c8f
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54392570"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Passer de la recherche dans les journaux Log Analytics aux journaux Azure Monitor
La recherche dans les journaux Log Analytics vient d’être remplacée par une nouvelle expérience d’analyse des journaux Azure Monitor. Actuellement, la page de recherche dans les journaux est toujours accessible par le biais de l’élément de menu **Journaux (classiques)** de la page **Espaces de travail Log Analytics** dans le portail Azure, mais elle sera supprimée le 15 février 2019. Cet article décrit les différences entre les deux expériences pour vous aider à remplacer la recherche dans les journaux. 

## <a name="extract-custom-fields"></a>Extraire des champs personnalisés 
Avec la recherche dans les journaux, vous extrayez des [champs personnalisés](../platform/custom-fields.md) à partir de la vue Liste, où le menu d’un champ inclut l’action _Extraire des champs de Table_.

![Extraction de champs avec la recherche dans les journaux](media/log-search-transition/extract-fields-log-search.png)

Avec les journaux Azure Monitor, vous extrayez des champs personnalisés à partir de la vue Table. Développez un enregistrement en cliquant sur la flèche à sa gauche, puis cliquez sur le bouton de sélection pour accéder à l’action _Extraire des champs_.

![Extraction de champs avec les journaux](media/log-search-transition/extract-fields-logs.png)

## <a name="filter-results-of-a-query"></a>Filtrer les résultats d’une requête
Avec la recherche dans les journaux, une liste de filtres apparaît à mesure que les résultats de la recherche sont fournis. Sélectionnez un filtre et cliquez sur **Appliquer** pour exécuter la requête avec le filtre sélectionné.

![Filtre avec la recherche dans les journaux](media/log-search-transition/filter-log-search.png)

Dans les journaux Azure Monitor, sélectionnez *Filtre (préversion)* pour afficher des filtres. Cliquez sur l’icône de filtre pour afficher les filtres d’ajout. Sélectionnez un filtre et cliquez sur **Appliquer et exécuter** pour exécuter la requête avec le filtre sélectionné.

![Filtre avec les journaux](media/log-search-transition/filter-logs.png)

## <a name="functions-and-computer-groups"></a>Fonctions et groupes d’ordinateurs
Pour enregistrer une recherche avec la recherche dans les journaux, sélectionnez **Recherches enregistrées** et **Ajouter** pour indiquer un nom, une catégorie et le texte de la requête. Créez un [groupe d’ordinateurs](../platform/computer-groups.md) en ajoutant un alias de fonction.

![Enregistrer la recherche dans les journaux](media/log-search-transition/save-search-log-search.png)

Pour enregistrer la requête actuelle dans les journaux Azure Monitor, sélectionnez **Enregistrer**. Remplacez **Enregistrer sous** par _Fonction_ et indiquez un **alias de fonction** pour créer une [fonction](functions.md).

![Enregistrer une requête de journal](media/log-search-transition/save-query-logs.png)

## <a name="saved-searches"></a>Recherches enregistrées
Avec la recherche dans les journaux, vos recherches enregistrées sont disponibles par le biais de l’élément de barre d’action **Recherches enregistrées**. Dans les journaux Azure Monitor, accédez aux requêtes enregistrées à partir de l’**Explorateur de requêtes**.

![Explorateur de requêtes](media/log-search-transition/query-explorer.png)

## <a name="take-action"></a>Effectuer une action
Avec la recherche dans les journaux, vous pouvez [démarrer un runbook](take-action.md) à partir d’un résultat de recherche en sélectionnant **Effectuer une action**.

![Effectuer une action](media/log-search-transition/take-action-log-search.png)

Dans les journaux Azure Monitor, [créez une alerte à partir de la requête de journal](../platform/alerts-log.md). Configurez un groupe d’actions avec une ou plusieurs actions à exécuter en réponse à l’alerte.

![Groupe d’actions](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez-en plus sur la nouvelle [expérience des journaux Azure Monitor](get-started-portal.md).