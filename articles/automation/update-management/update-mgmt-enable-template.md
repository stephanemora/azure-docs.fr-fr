---
title: Activer Update Management à l’aide d’un modèle Azure Resource Manager | Microsoft Docs
description: Cet article explique comment utiliser un modèle Resource Manager pour activer Update Management.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/10/2020
ms.openlocfilehash: 37f4b7208d7c8fafcd3aa1d25106a2cd5e4949c6
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449544"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Activer Update Management à l’aide d’un modèle Azure Resource Manager

Vous pouvez utiliser un [modèle Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) pour activer la fonctionnalité Update Management d’Azure Automation dans votre groupe de ressources. Cet article fournit un exemple de modèle qui automatise les éléments suivants :

* Création d’un espace de travail Log Analytics dans Azure Monitor.
* Création d’un compte de Azure Automation.
* Liaison du compte Automation à l’espace de travail Log Analytics, s’il n’est pas déjà lié.
* Activation d’Update Management.

Le modèle n’automatise pas l’activation d’Update Management sur une ou plusieurs machines virtuelles Azure ou non-Azure.

Si vous disposez déjà d’un espace de travail Log Analytics et d’un compte Automation déployés dans une région prise en charge dans votre abonnement, ils ne sont pas liés. Ce modèle permet de créer le lien et de déployer Update Management.

## <a name="api-versions"></a>Versions d’API

La table suivante répertorie les versions d’API pour les ressources utilisées dans ce modèle.

| Ressource | Type de ressource | Version de l'API |
|:---|:---|:---|
| Espace de travail | workspaces | 2020-03-01-preview |
| Compte Automation | automation | 2018-06-30 |
| Solution | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Avant d’utiliser le modèle

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Az d’Azure PowerShell. Exécutez `Get-Module -ListAvailable Az` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) pour créer une connexion à Azure. Avec Azure PowerShell, le déploiement utilise [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez exécuter Azure CLI version 2.1.0 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Avec Azure CLI, ce déploiement utilise [az group deployment create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

Le modèle JSON est configuré de manière à vous demander les éléments suivants :

* Nom de l’espace de travail.
* Région dans laquelle créer l’espace de travail.
* Pour activer les autorisations de ressource ou d’espace de travail.
* Nom du compte Automation
* Région dans laquelle créer le compte.

Le modèle JSON spécifie une valeur par défaut pour les autres paramètres susceptibles d’être utilisés pour une configuration standard dans votre environnement. Vous pouvez stocker le modèle dans un compte de stockage Azure pour mettre en place un accès partagé dans votre organisation. Pour plus d’informations sur l’utilisation des modèles, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure CLI](../../azure-resource-manager/templates/deploy-cli.md).

Les paramètres suivants dans le modèle sont définis avec une valeur par défaut pour l’espace de travail Log Analytics :

* Référence SKU : la valeur par défaut est le nouveau niveau de tarification par Go publié dans le modèle de tarification d’avril 2018.
* Conservation des données : 30 jours par défaut

>[!WARNING]
>Lors de la création ou de la configuration d’un espace de travail Log Analytics dans un abonnement pour lequel le nouveau modèle de tarification d’avril 2018 a été choisi, le seul niveau tarifaire Log Analytics valide est **PerGB2018**.
>

Le modèle JSON spécifie une valeur par défaut pour les autres paramètres susceptibles d’être utilisés comme configuration standard dans votre environnement. Vous pouvez stocker le modèle dans un compte de stockage Azure pour mettre en place un accès partagé dans votre organisation. Pour plus d’informations sur l’utilisation des modèles, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure CLI](../../azure-resource-manager/templates/deploy-cli.md).

Il est important de comprendre les détails de configuration suivants si vous débutez avec Azure Automation et Azure Monitor, afin d’éviter les erreurs lors de la tentative de création, de configuration et d’utilisation d’un espace de travail Log Analytics lié à votre nouveau compte Automation.

* Examinez les [détails supplémentaires](../../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) pour comprendre pleinement les options de configuration de l’espace de travail, telles que le mode de contrôle d’accès, le niveau tarifaire, la conservation des données et le niveau de réservation de capacité.

* Étant donné que seules certaines régions sont prises en charge pour lier un espace de travail Log Analytics et un compte Automation dans votre abonnement, passez en revue [Mappages d’espace de travail](../how-to/region-mappings.md) pour spécifier les régions prises en charge directement ou dans un fichier de paramètres.

* Si vous débutez avec les journaux d’activité Azure Monitor et que vous n’avez pas encore déployé d’espace de travail, vous devriez consulter les conseils de [conception de l’espace de travail](../../azure-monitor/platform/design-logs-deployment.md) pour en savoir plus sur le contrôle d’accès et comprendre les stratégies d’implémentation de conception que nous recommandons pour votre organisation.

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
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "resourcePermissions": {
              "type": "bool",
              "metadata": {
                "description": "true to use resource or workspace permissions. false to require workspace permissions."
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
            "apiVersion": "2020-03-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]",
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
                    "location": "[parameters('location')]",
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
            "apiVersion": "2018-06-30",
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
            "apiVersion": "2020-03-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[parameters('location')]",
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

2. Modifiez le modèle en fonction de vos besoins. Envisagez de créer un [fichier de paramètres Resource Manager](../../azure-resource-manager/templates/parameter-files.md) au lieu de passer des paramètres en tant que valeurs inline.

3. Enregistrez ce fichier dans un dossier local sous **deployUMSolutiontemplate.json**.

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

    ![Exemple de résultat lorsque le déploiement est achevé](media/update-mgmt-enable-template/template-output.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser Update Management pour des machines virtuelles, consultez [Gérer les mises à jour et les correctifs pour vos machines virtuelles](update-mgmt-manage-updates-for-vm.md).

* Si vous ne souhaitez plus utiliser Update Management et que vous voulez le supprimer, consultez les instructions dans [Supprimer Update Management](update-mgmt-remove-feature.md).

* Pour supprimer des machines virtuelles d’Update Management, consultez [Supprimer des machines virtuelles d’Update Management](update-mgmt-remove-vms.md).
