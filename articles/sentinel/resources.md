---
title: Ressources utiles lorsque vous travaillez avec Azure Sentinel | Microsoft Docs
description: Ce document vous fournit une liste des ressources utiles lorsque vous travaillez avec Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2021
ms.author: yelevin
ms.openlocfilehash: 2a6e31115fb2548f8248f741213970605f230036
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390723"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Ressources utiles lorsque vous travaillez avec Azure Sentinel



Cet article répertorie les ressources qui peuvent vous aider à obtenir plus d’informations sur l’utilisation d’Azure Sentinel.

- **Connecteurs Azure Logic Apps** : <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Audit et création de rapports
Les journaux d’audit Azure Sentinel sont conservés dans les [journaux d'activité Azure](../azure-monitor/platform/platform-logs-overview.md).

Parmi les opérations prises en charge, voici celles qui peuvent faire l’objet d’un audit.

|Nom d’opération|    Type de ressource|
|----|----|
|Créer ou mettre à jour un classeur  |Microsoft.Insights/workbooks|
|Supprimer un classeur    |Microsoft.Insights/workbooks|
|Définir un workflow   |Microsoft.Logic/workflows|
|Supprimer un workflow    |Microsoft.Logic/workflows|
|Créer une recherche enregistrée    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Supprimer une recherche enregistrée    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Mettre à jour des règles d’alerte |Microsoft.SecurityInsights/alertRules|
|Supprimer des règles d’alerte |Microsoft.SecurityInsights/alertRules|
|Mettre à jour des actions de réponse aux règles d’alerte |Microsoft.SecurityInsights/alertRules/actions|
|Supprimer des actions de réponse aux règles d’alerte |Microsoft.SecurityInsights/alertRules/actions|
|Mettre à jour des signets   |Microsoft.SecurityInsights/bookmarks|
|Supprimer des signets   |Microsoft.SecurityInsights/bookmarks|
|Mettre à jour des cas   |Microsoft.SecurityInsights/Cases|
|Mettre à jour une investigation de cas  |Microsoft.SecurityInsights/Cases/investigations|
|Créer des commentaires de cas   |Microsoft.SecurityInsights/Cases/comments|
|Mettre à jour des connecteurs de données |Microsoft.SecurityInsights/dataConnectors|
|Supprimer des connecteurs de données |Microsoft.SecurityInsights/dataConnectors|
|Mettre à jour les paramètres    |Microsoft.SecurityInsights/settings|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Afficher des données audit et de rapport dans Azure Sentinel

Vous pouvez afficher ces données en les transmettant en continu du journal d’activité Azure vers Azure Sentinel, d’où vous pouvez ensuite effectuer des recherches et des analyses à partir de ces données.

1. Connectez la source de données [Activité Azure](connect-azure-activity.md). Une fois cette opération effectuée, les événements d’audit sont transmis en continu à une nouvelle table dans l’écran **Journaux** appelée AzureActivity.

1. Ensuite, interrogez les données en utilisant KQL, comme vous le feriez avec n’importe quelle autre table.

    Par exemple, pour déterminer qui a été le dernier utilisateur à modifier une règle d’analyse particulière, utilisez la requête suivante (en remplaçant `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` par l’ID de la règle que vous souhaitez vérifier) :

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```


## <a name="blogs-and-forums"></a>Blogs et forums

Nous aimons recevoir des commentaires de nos utilisateurs !

- **Publiez vos questions** sur l’[espace TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) pour Azure Sentinel. 

- **Envoyez vos suggestions d’améliorations** via notre programme [User Voice](https://feedback.azure.com/forums/920458-azure-sentinel).

- **Affichez et commentez** sur nos billets de blog Azure Sentinel :

    - [Techcommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) 
    - [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/)


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Obtenir une certification](/learn/paths/security-ops-sentinel/)

> [!div class="nextstepaction"]
> [Lire les récits de cas d’usage client](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Sentinel%20%22&ff=&p=0&so=story_publish_date%20desc)

