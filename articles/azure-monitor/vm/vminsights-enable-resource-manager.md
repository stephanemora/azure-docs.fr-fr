---
title: Activer VM Insights à l’aide de modèles Resource Manager
description: Cet article décrit l’activation de VM Insights sur une ou plusieurs machines virtuelles ou groupes de machines virtuelles identiques à l’aide des modèles Azure PowerShell ou Azure Resource Manager.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: fc0c304a3fea81f44e01d3e815f34e44728ea42e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031862"
---
# <a name="enable-vm-insights-using-resource-manager-templates"></a>Activer VM Insights à l’aide de modèles Resource Manager
Cet article explique comment activer VM Insights pour une machine virtuelle ou un groupe de machines virtuelles identiques à l’aide de modèles Resource Manager. Cette procédure peut être utilisée pour les éléments suivants :

- Machine virtuelle Azure
- Groupe de machines virtuelles identiques Azure
- Machine virtuelle hybride connectée avec Azure Arc

## <a name="prerequisites"></a>Prérequis

- [Créer et configurer un espace de travail Log Analytics](./vminsights-configure-workspace.md) 
- Pour vous assurer que le système d’exploitation de la machine virtuelle ou du groupe de machines virtuelles identiques que vous activez est pris en charge, consulter [Systèmes d’exploitation pris en charge](./vminsights-enable-overview.md#supported-operating-systems). 

## <a name="resource-manager-templates"></a>Modèles Resource Manager

Nous avons créé des exemples de modèles Azure Resource Manager pour l’intégration de vos machines virtuelles et groupes de machines virtuelles identiques. Ces modèles incluent des scénarios que vous pouvez utiliser pour activer la surveillance sur une ressource existante et pour créer une ressource où la surveillance est activée.

>[!NOTE]
>Le modèle doit être déployé dans le même groupe de ressources que la machine virtuelle ou le groupe de machines virtuelles identiques sont activés.


Les modèles Azure Resource Manager sont fournis dans un fichier d’archive (.zip) que vous pouvez [télécharger](https://aka.ms/VmInsightsARMTemplates) à partir de notre référentiel GitHub. Le fichier contient des dossiers représentant chaque scénario de déploiement, avec un fichier de modèle et un fichier de paramètres. Avant de les exécuter, modifiez le fichier des paramètres et spécifiez les valeurs requises. 

Le fichier de téléchargement contient les modèles suivants pour différents scénarios :

- Le modèle **ExistingVmOnboarding** active VM Insights si la machine virtuelle existe déjà.
- Le modèle **NewVmOnboarding** crée une machine virtuelle et active VM Insights à des fins de surveillance.
- Le modèle **ExistingVmssOnboarding** active VM Insights si le groupe de machines virtuelles identiques existe déjà.
- Le modèle **NewVmssOnboarding** crée un groupe de machines virtuelles identiques et active VM Insights pour les surveiller.
- Le modèle **ConfigureWorkspace** configure votre espace de travail Log Analytics pour prendre en charge VM Insights en activant les solutions et la collection de compteurs de performances pour les systèmes d’exploitation Linux et Windows.

>[!NOTE]
>Si des groupes de machines virtuelles identiques sont déjà présents et que la stratégie de mise à niveau est **manuelle**, VM Insights n’est pas activé pour les instances par défaut après l’exécution du modèle Resource Manager **ExistingVmssOnboarding**. Vous devez mettre à niveau les instances manuellement.

## <a name="deploy-templates"></a>Déployer des modèles
Les modèles peuvent être déployés à l’aide de [n’importe quelle méthode de déploiement pour les modèles Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md), y compris les exemples suivants utilisant PowerShell et CLI.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az deployment group create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>Étapes suivantes

Une fois la surveillance activée pour vos machines virtuelles, ces informations sont disponibles pour analyse avec VM Insights.

- Pour afficher les dépendances d’applications découvertes, consultez [Afficher la Carte VM Insights](vminsights-maps.md).

- Pour identifier les goulots d’étranglement et l’utilisation globale avec les performances de votre machine virtuelle, consultez [Afficher les performances des machines virtuelles Azure](vminsights-performance.md).