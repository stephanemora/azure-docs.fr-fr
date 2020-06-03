---
title: Créer un compte Automation à l’aide d’un modèle Azure Resource Manager | Microsoft Docs
description: Cet article explique comment utiliser un modèle Azure Resource Manager pour créer un compte Azure Automation.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2020
ms.openlocfilehash: 1418b26a2a498c43ff61f42b2761c59cbca5d0f4
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837142"
---
# <a name="create-an-automation-account-using-an-azure-resource-manager-template"></a>Créer un compte Automation à l’aide d’un modèle Azure Resource Manager

Vous pouvez utiliser des [modèles Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) pour créer un compte Azure Automation dans votre groupe de ressources. Cet article fournit un exemple de modèle qui effectue les opérations suivantes :

* Automatise la création d’un espace de travail Azure Monitor Log Analytics.
* Automatise la création d’un compte Azure Automation.
* Lie le compte Automation à l’espace de travail Log Analytics.

Le modèle n’automatise pas l’activation de machines virtuelles Azure ou non-Azure. 

>[!NOTE]
>La création du compte d’identification Automation n’est pas prise en charge si vous utilisez un modèle Azure Resource Manager. Pour créer manuellement un compte d’identification à partir du portail ou avec PowerShell, consultez [Gérer les comptes d’identification](manage-runas-account.md).

Une fois ces étapes accomplies, vous devez [configurer des paramètres de diagnostic](automation-manage-send-joblogs-log-analytics.md) pour votre compte Automation, afin d’envoyer l’état de la tâche du Runbook et les flux de tâches à l’espace de travail Log Analytics lié. 

## <a name="api-versions"></a>Versions d’API

La table suivante répertorie la version d’API pour les ressources utilisées dans cet exemple.

| Ressource | Type de ressource | Version de l'API |
|:---|:---|:---|
| Espace de travail | workspaces | 2017-03-15-preview |
| Compte Automation | automation | 2015-10-31 | 

## <a name="before-you-use-the-template"></a>Avant d’utiliser le modèle

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Az d’Azure PowerShell. Exécutez `Get-Module -ListAvailable Az` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzAccount` pour créer une connexion avec Azure. Avec PowerShell, le déploiement utilise [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Si vous choisissez d’installer et d’utiliser Azure CLI localement, pour cet article, vous devez exécuter la version 2.1.0 ou une version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Avec Azure CLI, ce déploiement utilise la commande [az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

Le modèle JSON est configuré de manière à vous demander les éléments suivants :

* Nom de l’espace de travail.
* Région dans laquelle créer l’espace de travail.
* Nom du compte Automation.
* Région dans laquelle créer le compte.

Les paramètres suivants dans le modèle sont définis avec une valeur par défaut pour l’espace de travail Log Analytics :

* La valeur par défaut de *sku* est le niveau de tarification par Go publié dans le modèle de tarification d’avril 2018.
* La valeur par défaut de *dataRetention* est 30 jours.
* La valeur par défaut de *capacityReservationLevel* est de 100 Go.

>[!WARNING]
>Si vous voulez créer ou configurer un espace de travail Log Analytics dans un abonnement pour lequel le modèle de tarification d’avril 2018 a été choisi, le seul niveau tarifaire Log Analytics valide est *PerGB2018*.
>

Le modèle JSON spécifie une valeur par défaut pour les autres paramètres susceptibles d’être utilisés comme configuration standard dans votre environnement. Vous pouvez stocker le modèle dans un compte de stockage Azure pour mettre en place un accès partagé dans votre organisation. Pour plus d’informations sur l’utilisation des modèles, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Si vous ne connaissez pas encore Azure Automation et Azure vous devez bien comprendre les détails de configuration suivants. Ils peuvent vous aider à éviter des erreurs quand vous essayez de créer, de configurer et d’utiliser un espace de travail Log Analytics lié à votre nouveau compte Automation. 

* Examinez les [détails supplémentaires](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) pour comprendre pleinement les options de configuration de l’espace de travail, telles que le mode de contrôle d’accès, le niveau tarifaire, la conservation des données et le niveau de réservation de capacité.

* Passez en revue les [mappages d’espace de travail](how-to/region-mappings.md) pour spécifier les régions prises en charge incluses ou figurant dans un fichier de paramètres. Seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation dans votre abonnement.

* Si vous ne connaissez pas les journaux Azure Monitor et n’avez pas encore déployé un espace de travail, vous devez consulter les [conseils pour la conception de l’espace de travail](../azure-monitor/platform/design-logs-deployment.md). Ils vous aideront à vous familiariser avec le contrôle d’accès et à comprendre les stratégies d’implémentation de conception que nous recommandons pour votre organisation.

## <a name="deploy-the-template"></a>Déployer le modèle

1. Copiez et collez la syntaxe JSON suivante dans votre fichier :

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "pergb2018",
                "Free",
                "Standalone",
                "PerNode",
                "Standard",
                "Premium"
            ],
            "defaultValue": "pergb2018",
            "metadata": {
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can have only 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This applies only when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        },
        "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "sampleGraphicalRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.graphrunbook"
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePowerShellRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.ps1"
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorialPython2.py"
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
                    "Name": "[parameters('sku')]",
                    "name": "CapacityReservation",
                    "capacityReservationLevel": 100
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
        "resources": [
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [
                 "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
            ],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
            "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('sampleGraphicalRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePowerShellRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePython2RunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
        },
        {
            "apiVersion": "2015-11-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
       ]
      }
     ]
    }
    ```

2. Modifiez le modèle en fonction de vos besoins. Envisagez de créer un [fichier de paramètres Resource Manager](../azure-resource-manager/templates/parameter-files.md) au lieu de transmettre des paramètres en tant que valeurs incluses.

3. Enregistrez ce fichier sous deployAzAutomationAccttemplate.json dans un dossier local.

4. Vous êtes prêt à déployer ce modèle. Vous pouvez utiliser PowerShell ou l’interface de ligne de commande Azure. Lorsque vous êtes invité à entrer un nom d’espace de travail et de compte Automation, fournissez un nom globalement unique dans tous les abonnements Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    Le déploiement peut prendre plusieurs minutes. Dans ce cas, un message semblable au suivant s’affiche, qui inclut le résultat.

    ![Exemple de résultat lorsque le déploiement est achevé](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>Étapes suivantes

Pour transférer l’état de la tâche du Runbook et les flux de tâches à votre espace de travail Log Analytics lié, consultez [Transférer des données de travaux Azure Automation aux journaux d’activité Azure Monitor](automation-manage-send-joblogs-log-analytics.md). Cela a pour effet de configurer les paramètres de diagnostic du compte Automation à l’aide de commandes Azure PowerShell pour accomplir l’intégration pour l’envoi de journaux à l’espace de travail à des fins d’analyse. 
