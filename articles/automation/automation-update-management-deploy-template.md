---
title: Utiliser des modèles Azure Resource Manager pour intégrer Update Management | Microsoft Docs
description: Vous pouvez utiliser un modèle Azure Resource Manager pour intégrer la solution Update Management d’Azure Automation.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/27/2020
ms.openlocfilehash: a8b382663b56d7481da876979e33194fb0ac533d
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921752"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Intégration de la solution Update Management à l’aide du modèle Azure Resource Manager

Vous pouvez utiliser des [modèles Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) pour activer la solution Update Management d’Azure Automation dans votre groupe de ressources. Cet article fournit un exemple de modèle qui automatise les éléments suivants :

* Création d’un espace de travail Log Analytics dans Azure Monitor.
* Création d’un compte de Azure Automation.
* Lie le compte Automation à l’espace de travail Log Analytics s’il n’est pas déjà lié.
* Intégrer la solution Update Management d’Azure Automation

Le modèle n’automatise pas l’intégration d’une ou de plusieurs machines virtuelles Azure ou non-Azure.

Si vous disposez déjà d’un espace de travail Log Analytics et d’un compte Automation déployés dans une région prise en charge dans votre abonnement, qu’ils ne sont pas liés et que la solution Update Management n’est pas encore déployée sur l’espace de travail, l’utilisation de ce modèle permet de créer le lien et de déployer la solution Update Management. 

## <a name="api-versions"></a>Versions d’API

La table suivante répertorie la version d’API pour les ressources utilisées dans cet exemple.

| Ressource | Type de ressource | Version de l'API |
|:---|:---|:---|
| Espace de travail | workspaces | 2017-03-15-preview |
| Compte Automation | automation | 2015-10-31 | 
| Solution | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Avant d’utiliser le modèle

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Az d’Azure PowerShell. Exécutez `Get-Module -ListAvailable Az` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure. Avec Azure PowerShell, le déploiement utilise [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez exécuter Azure CLI version 2.1.0 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Avec Azure CLI, ce déploiement utilise [az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

Le modèle JSON est configuré de manière à vous demander les éléments suivants :

* Nom de l’espace de travail
* Région dans laquelle créer l’espace de travail
* Nom du compte Automation
* Région dans laquelle créer le compte

Le modèle JSON spécifie une valeur par défaut pour les autres paramètres susceptibles d’être utilisés comme configuration standard dans votre environnement. Vous pouvez stocker le modèle dans un compte de stockage Azure pour mettre en place un accès partagé dans votre organisation. Pour plus d’informations sur l’utilisation des modèles, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Les paramètres suivants dans le modèle sont définis avec une valeur par défaut pour l’espace de travail Log Analytics :

* Référence SKU : la valeur par défaut est le nouveau niveau de tarification par Go publié dans le modèle de tarification d’avril 2018.
* Conservation des données : 30 jours par défaut

>[!WARNING]
>Lors de la création ou de la configuration d’un espace de travail Log Analytics dans un abonnement pour lequel le nouveau modèle de tarification d’avril 2018 a été choisi, le seul niveau tarifaire Log Analytics valide est **PerGB2018**.
>

>[!NOTE]
>Avant d’utiliser ce modèle, examinez les [détails supplémentaires](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) pour comprendre pleinement les options de configuration de l’espace de travail, telles que le mode de contrôle d’accès, le niveau tarifaire, la conservation des données et le niveau de réservation de capacité. Si vous débutez avec les journaux d’activité Azure Monitor et que vous n’avez pas encore déployé d’espace de travail, vous devriez consulter les conseils de [conception de l’espace de travail](../azure-monitor/platform/design-logs-deployment.md) pour en savoir plus sur le contrôle d’accès et comprendre les stratégies d’implémentation de conception que nous recommandons pour votre organisation.

## <a name="deploy-template"></a>Déployer un modèle

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
        "pricingTier": {
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
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "australiacentral",
                "australiaeast",
                "australiasoutheast",
                "brazilsouth",
                "canadacentral",
                "centralindia",
                "centralus",
                "eastasia",
                "eastus",
                "eastus2",
                "francecentral",
                "japaneast",
                "koreacentral",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus",
                "westus2"
            ],
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
        }
    },
    "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
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
                    "apiVersion": "2015-11-01-preview",
                    "location": "[resourceGroup().location]",
                    "name": "[variables('Updates').name]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    },
                    "plan": {
                        "name": "[variables('Updates').name]",
                        "publisher": "Microsoft",
                        "promotionCode": "",
                        "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                    }
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
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
    ```

2. Modifiez le modèle en fonction de vos besoins.

3. Enregistrez ce fichier sous deployUMSolutiontemplate.json dans un dossier local.

4. Vous êtes prêt à déployer ce modèle. Vous pouvez utiliser PowerShell ou l’interface de ligne de commande Azure. Lorsque vous êtes invité à entrer un nom d’espace de travail et de compte Automation, fournissez un nom globalement unique dans tous les abonnements Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Le déploiement peut prendre plusieurs minutes. Lorsqu’il est terminé, vous voyez un message similaire au message suivant qui inclut le résultat :

    ![Exemple de résultat lorsque le déploiement est achevé](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez déployé la solution Update Management, vous pouvez activer des machines virtuelles pour la gestion, passer en revue les évaluations de mise à jour et déployer des mises à jour pour les mettre en conformité.

- À partir de votre [compte Azure Automation](automation-onboard-solutions-from-automation-account.md) pour une ou plusieurs machines Azure et manuellement pour les machines non-Azure.

- Pour une machine virtuelle Azure unique à partir de la page de la machine virtuelle dans le Portail Azure. Ce scénario est disponible pour les machines virtuelles [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) et [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management).

- Pour [plusieurs machines virtuelles Azure](manage-update-multi.md), sélectionnez-les dans la page **Machines virtuelles** dans le Portail Azure. 