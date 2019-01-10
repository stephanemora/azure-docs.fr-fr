---
title: Créer un espace de travail Log Analytics à l’aide d’Azure PowerShell | Microsoft Docs
description: Découvrez comment créer un espace de travail Log Analytics pour permettre la collecte de données et les solutions de gestion à partir de vos environnements locaux et cloud avec Azure PowerShell.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: 1e447f149c54e4c095d376db3f06dc0a0c399542
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104911"
---
# <a name="create-a-log-analytics-workspace-with-azure-powershell"></a>Créer un espace de travail Log Analytics avec Azure PowerShell

Le module Azure PowerShell est utilisé pour créer et gérer des ressources Azure à partir de la ligne de commande PowerShell ou dans des scripts. Ce démarrage rapide vous montre comment utiliser le module Azure PowerShell pour déployer dans Azure un espace de travail Log Analytics, environnement unique doté de ses propres référentiels de données, sources de données et solutions.  Vous devez suivre les étapes décrites dans cet article si vous souhaitez collecter des données à partir des sources suivantes :

* Ressources Azure dans votre abonnement  
* Ordinateurs locaux surveillés par System Center Operations Manager  
* Collections d’appareils de System Center Configuration Manager  
* Données de diagnostics et de journaux du Stockage Azure  
 
Pour les autres sources, telles que les machines virtuelles Azure et les machines virtuelles Windows ou Linux dans votre environnement, consultez les rubriques suivantes :

* [Collect data from Azure virtual machines](../../azure-monitor/learn/quick-collect-azurevm.md) (Collecter des données à partir de machines virtuelles Azure)
* [Collect data from hybrid Linux computer](../../azure-monitor/learn/quick-collect-linux-computer.md) (Collecter des données à partir d’un ordinateur Linux hybride)
* [Collect data from hybrid Windows computer](quick-collect-windows-computer.md) (Collecter des données à partir d’un ordinateur Windows hybride)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.7.0 ou version ultérieure pour les besoins de ce tutoriel. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="create-a-workspace"></a>Créer un espace de travail
Créez un espace de travail avec [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment). L’exemple suivant montre comment créer un espace de travail nommé *TestWorkspace* dans le groupe de ressources *Lab* à l’emplacement *eastus* à l’aide d’un modèle Resource Manager à partir de votre ordinateur local. Le modèle JSON est configuré pour vous demander uniquement le nom de l’espace de travail et spécifie une valeur par défaut pour les autres paramètres susceptibles d’être utilisés comme configuration standard dans votre environnement. 

Les paramètres suivants sont définis comme une valeur par défaut :

* Emplacement : par défaut, USA Est
* Référence SKU : la valeur par défaut est le nouveau niveau de tarification par Go publié dans le modèle de tarification d’avril 2018.

>[!WARNING]
>Lors de la création ou de la configuration d’un espace de travail Log Analytics dans un abonnement pour lequel le nouveau modèle de tarification d’avril 2018 a été choisi, le seul niveau tarifaire Log Analytics valide est **PerGB2018**. 
>

### <a name="create-and-deploy-template"></a>Créer et déployer un modèle

1. Copiez et collez la syntaxe JSON suivante dans votre fichier :

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Modifiez le modèle en fonction de vos besoins.  Consultez la référence [Microsoft.OperationalInsights/workspaces modèle](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) pour découvrir les propriétés et les valeurs prises en charge. 
3. Enregistrez ce fichier au format **deploylaworkspacetemplate.json** dans un dossier local.   
4. Vous êtes prêt à déployer ce modèle. Utilisez les commandes suivantes à partir du dossier qui contient le modèle :

    ```powershell
        New-AzureRmResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
    ```

Le déploiement peut prendre plusieurs minutes. Lorsqu’il est terminé, vous voyez un message similaire au message suivant qui inclut le résultat :

![Exemple de résultat lorsque le déploiement est achevé](media/quick-create-workspace-posh/template-output-01.png)

## <a name="next-steps"></a>Étapes suivantes
Disposant à présent d’un espace de travail, vous pouvez configurer la collecte des données de télémétrie de surveillance, exécuter des recherches dans les journaux pour analyser ces données et ajouter une solution de gestion pour fournir des données et insights analytiques supplémentaires.  

* Pour activer la collecte de données à partir de ressources Azure avec Azure Diagnostics ou le stockage Azure, consultez [Collecte des journaux et des métriques des services Azure à utiliser dans Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md).  
* Ajoutez [System Center Operations Manager comme source de données](../../azure-monitor/platform/om-agents.md) pour collecter des données à partir des agents rattachés à votre groupe d’administration Operations Manager et les stocker dans votre espace de travail Log Analytics.  
* Connectez [Configuration Manager](../../azure-monitor/platform/collect-sccm.md) pour importer des ordinateurs qui sont membres de collections dans la hiérarchie.  
* Examinez les [solutions de gestion](../../azure-monitor/insights/solutions.md) disponibles et la façon d’ajouter ou de supprimer une solution dans votre espace de travail.
