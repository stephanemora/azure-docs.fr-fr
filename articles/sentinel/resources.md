---
title: Ressources utiles lorsque vous travaillez avec Azure Sentinel | Microsoft Docs
description: Ce document vous fournit une liste des ressources utiles lorsque vous travaillez avec Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: rkarlin
ms.openlocfilehash: fe0eb6c2305dd01d8ff3df5be7056cbeae92fff2
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74762715"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Ressources utiles lorsque vous travaillez avec Azure Sentinel



Cet article répertorie les ressources qui peuvent vous aider à obtenir plus d’informations sur l’utilisation d’Azure Sentinel.

Connecteurs Azure Logic Apps : <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Audit et création de rapports
Les journaux d’audit Azure Sentinel sont conservés dans les [journaux d'activité Azure](../azure-monitor/platform/activity-logs-overview.md).

Parmi les opérations prises en charge, voici celles qui peuvent faire l’objet d’un audit.

|Nom d’opération|    Type de ressource|
|----|----|
|Créer ou mettre à jour un classeur  |Microsoft.Insights/workbooks|
|Supprimer un classeur    |Microsoft.Insights/workbooks|
|Définir un workflow   |Microsoft.Logic/workflows|
|Supprimer un workflow    |Microsoft.Logic/workflows|
|Créer une recherche enregistrée    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Supprimer une recherche enregistrée    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Définir un tableau de bord  |Microsoft.Portal/dashboards|
|Supprimer un tableau de bord   |Microsoft.Portal/dashboards|
|Mettre à jour des règles d’alerte |Microsoft.SecurityInsights/alertRules|
|Supprimer des règles d’alerte |Microsoft.SecurityInsights/alertRules|
|Mettre à jour des actions de réponse aux règles d’alerte |Microsoft.SecurityInsights/alertRules|
|Supprimer des actions de réponse aux règles d’alerte |Microsoft.SecurityInsights/alertRules|
|Mettre à jour des signets   |Microsoft.SecurityInsights/bookmarks|
|Supprimer des signets   |Microsoft.SecurityInsights/bookmarks|
|Mettre à jour des cas   |Microsoft.SecurityInsights/Cases|
|Mettre à jour une investigation de cas  |Microsoft.SecurityInsights/Cases|
|Créer des commentaires de cas   |Microsoft.SecurityInsights/Cases|
|Mettre à jour des connecteurs de données |Microsoft.SecurityInsights/dataConnectors|
|Supprimer des connecteurs de données |Microsoft.SecurityInsights/dataConnectors|
|Mettre à jour les paramètres    |Microsoft.SecurityInsights/settings|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Afficher des données audit et de rapport dans Azure Sentinel

Vous pouvez afficher ces données en les transmettant en continu du journal d’activité Azure vers Azure Sentinel, d’où vous pouvez ensuite effectuer des recherches et des analyses à partir de ces données.

1. Connectez la source de données [Activité Azure](connect-azure-activity.md). Une fois cette opération effectuée, les événements d’audit sont transmis en continu à une nouvelle table dans l’écran **Journaux** appelée AzureActivity.
2. Ensuite, interrogez les données en utilisant KQL, comme vous le feriez avec n’importe quelle autre table.



## <a name="vendor-documentation"></a>Documentation du fournisseur

| **Fournisseur**  | **Utiliser un incident dans Azure Sentinel** | **Lien**|
|----|----|----|
| GitHub| Utilisé pour accéder à la page de la communauté| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| Configurer CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Cours sur le langage de requête Kusto| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blogs et forums

Publiez vos questions sur l’[espace TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) pour Azure Sentinel.

Consultez les billets de blog Azure Sentinel dans [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) et [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/).


## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez obtenu une liste des ressources qui sont utiles lorsque vous travaillez avec Azure Sentinel. Vous trouverez des informations supplémentaires sur la sécurité et la conformité Azure dans le [Blog sur la sécurité et la conformité Microsoft Azure](https://blogs.msdn.com/b/azuresecurity/).
