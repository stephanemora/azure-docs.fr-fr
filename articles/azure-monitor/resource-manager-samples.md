---
title: Exemples de modèle Resource Manager pour Azure Monitor
description: Déployer et configurer des fonctionnalités Azure Monitor à l’aide de modèles Resource Manager
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b67a1dcb2f6df42c95bb0b5c20ae4397582112db
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111966914"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>Exemples de modèle Resource Manager pour Azure Monitor

Azure Monitor peut être déployé et configuré à grande échelle à l’aide d’un [modèle Azure Resource Manager](../azure-resource-manager/templates/syntax.md). Les articles suivants fournissent des exemples de modèle pour différentes fonctionnalités d’Azure Monitor. Ces exemples peuvent être modifiés en fonction de vos besoins spécifiques, et déployés par le biais de n’importe quelle méthode standard de déploiement de modèles Resource Manager. 

## <a name="deploying-the-sample-templates"></a>Déploiement des exemples de modèle
Les étapes de base pour utiliser les exemples sont les suivantes :

1. Copie du modèle et enregistrement de celui-ci sous la forme d’un fichier JSON.
2. Modification des paramètres de votre environnement et enregistrement de celui-ci sous la forme d’un fichier JSON.
4. Déploiement du modèle au moyen de [n’importe quelle méthode de déploiement des modèles Resource Manager](../azure-resource-manager/templates/deploy-powershell.md). 

Par exemple, utilisez les commandes suivantes pour déployer le fichier de modèle et celui des paramètres sur un groupe de ressources à l’aide de PowerShell ou d’Azure CLI.


```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

```azurecli
az login
az deployment group create \
    --name AzureMonitorDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file azure-monitor-deploy.json \
    --parameters azure-monitor-deploy.parameters.json
```

## <a name="list-of-sample-templates"></a>Liste des exemples de modèle

- [Agents](agents/resource-manager-agent.md) : déploiement et configuration de l’agent Log Analytics et de l’extension de diagnostic associé.
- Alertes
  - [Règles d’alerte de journal](alerts/resource-manager-alerts-log.md) : alertes provenant des requêtes de journal et du journal d’activité Azure.
  - [Règles d’alerte de métriques](alerts/resource-manager-alerts-metric.md) : alertes issues des métriques en utilisant différents types de logiques.
- [Application Insights](app/resource-manager-app-resource.md)
- [Paramètres de diagnostic](essentials/resource-manager-diagnostic-settings.md) : création des paramètres de diagnostic permettant de transférer journaux et métriques depuis différents types de ressources.
- [Requêtes de journal](logs/resource-manager-log-queries.md) : création de requêtes de journal enregistrées dans un espace de travail Log Analytics.
- [Espace de travail Log Analytics](logs/resource-manager-workspace.md) : création d’un espace de travail Log Analytics et configuration de la collecte de différentes sources de données à partir de l’agent Log Analytics.
- [Classeurs](visualize/resource-manager-workbooks.md) : création de classeurs.
- [Container Insights](containers/resource-manager-container-insights.md) : intégration de clusters à Container Insights.
- [Azure Monitor pour machines virtuelles](vm/resource-manager-vminsights.md) : intégration de machines virtuelles à Azure Monitor pour machines virtuelles.



## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [modèles Resource Manager](../azure-resource-manager/templates/overview.md)