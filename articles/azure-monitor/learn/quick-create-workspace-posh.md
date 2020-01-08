---
title: Créer un espace de travail Log Analytics à l’aide d’Azure PowerShell | Microsoft Docs
description: Découvrez comment créer un espace de travail Log Analytics pour permettre la collecte de données et les solutions de gestion à partir de vos environnements locaux et cloud avec Azure PowerShell.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 303f255057b414bc06cd7ae803fe368c2acc1a1a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75399442"
---
# <a name="create-a-log-analytics-workspace-with-azure-powershell"></a>Créer un espace de travail Log Analytics avec Azure PowerShell

Le module Azure PowerShell est utilisé pour créer et gérer des ressources Azure à partir de la ligne de commande PowerShell ou dans des scripts. Ce guide de démarrage rapide explique comment utiliser le module PowerShell pour déployer un espace de travail Log Analytics dans Azure. Un espace de travail Log Analytics est un environnement unique pour les données de journal d’activité Azure Monitor. Chaque espace de travail dispose d’un référentiel de données et d’une configuration propres. Les sources de données et les solutions sont configurées de façon à stocker leurs données dans un espace de travail particulier. Vous devez avoir un espace de travail Log Analytics si vous souhaitez collecter des données à partir des sources suivantes :

* Ressources Azure dans votre abonnement  
* Ordinateurs locaux surveillés par System Center Operations Manager  
* Collections d’appareils de System Center Configuration Manager  
* Données de diagnostics et de journaux du Stockage Azure  
 
Pour les autres sources, telles que les machines virtuelles Azure et les machines virtuelles Windows ou Linux dans votre environnement, consultez les rubriques suivantes :

* [Collect data from Azure virtual machines](../learn/quick-collect-azurevm.md) (Collecter des données à partir de machines virtuelles Azure)
* [Collect data from hybrid Linux computer](../learn/quick-collect-linux-computer.md) (Collecter des données à partir d’un ordinateur Linux hybride)
* [Collect data from hybrid Windows computer](quick-collect-windows-computer.md) (Collecter des données à partir d’un ordinateur Windows hybride)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell Az pour les besoins de ce didacticiel. Exécutez `Get-Module -ListAvailable Az` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="create-a-workspace"></a>Créer un espace de travail
Créez un espace de travail avec [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). L’exemple suivant crée un espace de travail à l’emplacement *eastus* à l’aide d’un modèle Resource Manager à partir de votre ordinateur local. Le modèle JSON est configuré pour vous demander uniquement le nom de l’espace de travail et spécifie une valeur par défaut pour les autres paramètres susceptibles d’être utilisés comme configuration standard dans votre environnement. 

Pour plus d’informations sur les régions prises en charge, consultez les [régions dans lesquelles Log Analytics est disponible](https://azure.microsoft.com/regions/services/) et recherchez Azure Monitor à partir du champ **Rechercher un produit**. 

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
            "apiVersion": "2015-11-01-preview",
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

2. Modifiez le modèle en fonction de vos besoins. Consultez la référence [Microsoft.OperationalInsights/workspaces modèle](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) pour découvrir les propriétés et les valeurs prises en charge. 
3. Enregistrez ce fichier au format **deploylaworkspacetemplate.json** dans un dossier local.   
4. Vous êtes prêt à déployer ce modèle. Utilisez les commandes suivantes à partir du dossier qui contient le modèle. Lorsque vous êtes invité à entrer un nom d’espace de travail, fournissez un nom globalement unique dans tous les abonnements Azure.

    ```powershell
        New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
    ```

Le déploiement peut prendre plusieurs minutes. Lorsqu’il est terminé, vous voyez un message similaire au message suivant qui inclut le résultat :

![Exemple de résultat lorsque le déploiement est achevé](media/quick-create-workspace-posh/template-output-01.png)

## <a name="next-steps"></a>Étapes suivantes
Disposant à présent d’un espace de travail, vous pouvez configurer la collecte des données de télémétrie de surveillance, exécuter des recherches dans les journaux pour analyser ces données et ajouter une solution de gestion pour fournir des données et insights analytiques supplémentaires.  

* Pour activer la collecte de données à partir de ressources Azure avec Diagnostics Azure ou le stockage Azure, consultez [Collecte des journaux et des métriques des services Azure à utiliser dans Azure Monitor](../platform/collect-azure-metrics-logs.md).  
* Ajoutez [System Center Operations Manager comme source de données](../platform/om-agents.md) pour collecter des données à partir des agents rattachés à votre groupe d’administration Operations Manager et les stocker dans votre espace de travail Log Analytics.  
* Connectez [Configuration Manager](../platform/collect-sccm.md) pour importer des ordinateurs qui sont membres de collections dans la hiérarchie.  
* Examinez les [solutions de surveillance](../insights/solutions.md) disponibles et la façon d’ajouter ou de supprimer une solution dans votre espace de travail.
