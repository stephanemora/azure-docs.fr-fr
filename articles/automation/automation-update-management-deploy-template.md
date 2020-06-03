---
title: Activer Update Management à l’aide d’un modèle Azure Resource Manager | Microsoft Docs
description: Cet article explique comment utiliser un modèle Resource Manager pour activer Update Management.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/24/2020
ms.openlocfilehash: 0a83117d6d58f45d6ee1de2b8d61c2157738fc75
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830989"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Activer Update Management à l’aide d’un modèle Azure Resource Manager

Vous pouvez utiliser un [modèle Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) pour activer la fonctionnalité Update Management d’Azure Automation dans votre groupe de ressources. Cet article fournit un exemple de modèle qui automatise les éléments suivants :

* Création d’un espace de travail Log Analytics dans Azure Monitor.
* Création d’un compte de Azure Automation.
* Liaison du compte Automation à l’espace de travail Log Analytics, s’il n’est pas déjà lié.
* Activation d’Update Management.

Le modèle n’automatise pas l’activation d’une ou de plusieurs machines virtuelles Azure ou non-Azure.

Si vous disposez déjà d’un espace de travail Log Analytics et d’un compte Automation déployés dans une région prise en charge dans votre abonnement, ils ne sont pas liés. Update Management n’est pas encore activé pour l’espace de travail. Ce modèle permet de créer le lien et de déployer Update Management pour vos machines virtuelles. 

>[!NOTE]
>L’utilisateur **nxautomation** activé en lien avec Update Management sur Linux exécute uniquement des runbooks signés.

## <a name="api-versions"></a>Versions d’API

La table suivante répertorie les versions d’API pour les ressources utilisées dans ce modèle.

| Ressource | Type de ressource | Version de l'API |
|:---|:---|:---|
| Espace de travail | workspaces | 2017-03-15-preview |
| Compte Automation | automation | 2015-10-31 | 
| Solution | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Avant d’utiliser le modèle

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Az d’Azure PowerShell. Exécutez `Get-Module -ListAvailable Az` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) pour créer une connexion à Azure. Avec Azure PowerShell, le déploiement utilise [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez exécuter Azure CLI version 2.1.0 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Avec Azure CLI, ce déploiement utilise [az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

Le modèle JSON est configuré de manière à vous demander les éléments suivants :

* Nom de l’espace de travail
* Région dans laquelle créer l’espace de travail
* Nom du compte Automation
* Région dans laquelle créer le compte

Le modèle JSON spécifie une valeur par défaut pour les autres paramètres susceptibles d’être utilisés pour une configuration standard dans votre environnement. Vous pouvez stocker le modèle dans un compte de stockage Azure pour mettre en place un accès partagé dans votre organisation. Pour plus d’informations sur l’utilisation des modèles, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Les paramètres suivants dans le modèle sont définis avec une valeur par défaut pour l’espace de travail Log Analytics :

* Référence SKU : la valeur par défaut est le nouveau niveau de tarification par Go publié dans le modèle de tarification d’avril 2018.
* Conservation des données : 30 jours par défaut
* Réservation de capacité : 100 Go par défaut

>[!WARNING]
>Lors de la création ou de la configuration d’un espace de travail Log Analytics dans un abonnement pour lequel le nouveau modèle de tarification d’avril 2018 a été choisi, le seul niveau tarifaire Log Analytics valide est **PerGB2018**.
>

Le modèle JSON spécifie une valeur par défaut pour les autres paramètres susceptibles d’être utilisés comme configuration standard dans votre environnement. Vous pouvez stocker le modèle dans un compte de stockage Azure pour mettre en place un accès partagé dans votre organisation. Pour plus d’informations sur l’utilisation des modèles, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Il est important de comprendre les détails de configuration suivants si vous débutez avec Azure Automation et Azure Monitor, afin d’éviter les erreurs lors de la tentative de création, de configuration et d’utilisation d’un espace de travail Log Analytics lié à votre nouveau compte Automation.

* Examinez les [détails supplémentaires](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) pour comprendre pleinement les options de configuration de l’espace de travail, telles que le mode de contrôle d’accès, le niveau tarifaire, la conservation des données et le niveau de réservation de capacité.

* Étant donné que seules certaines régions sont prises en charge pour lier un espace de travail Log Analytics et un compte Automation dans votre abonnement, passez en revue [Mappages d’espace de travail](how-to/region-mappings.md) pour spécifier les régions prises en charge directement ou dans un fichier de paramètres.

* Si vous débutez avec les journaux d’activité Azure Monitor et que vous n’avez pas encore déployé d’espace de travail, vous devriez consulter les conseils de [conception de l’espace de travail](../azure-monitor/platform/design-logs-deployment.md) pour en savoir plus sur le contrôle d’accès et comprendre les stratégies d’implémentation de conception que nous recommandons pour votre organisation.

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
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
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

2. Modifiez le modèle en fonction de vos besoins. Envisagez de créer un [fichier de paramètres Resource Manager](../azure-resource-manager/templates/parameter-files.md) au lieu de passer des paramètres en tant que valeurs inline.

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

    ![Exemple de résultat lorsque le déploiement est achevé](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser Update Management pour des machines virtuelles, consultez [Gérer les mises à jour et les correctifs pour vos machines virtuelles Azure](automation-tutorial-update-management.md).
* Si vous n’avez plus besoin de l’espace de travail Log Analytics, consultez les instructions fournies dans [Dissocier un espace de travail d’un compte Automation pour Update Management](automation-unlink-workspace-update-management.md).
* Pour supprimer des machines virtuelles d’Update Management, consultez [Supprimer des machines virtuelles d’Update Management](automation-remove-vms-from-update-management.md).
* Pour résoudre les erreurs générales d’Update Management, consultez [Résoudre les problèmes liés à Update Management](troubleshoot/update-management.md).
* Pour résoudre les problèmes liés à l’agent de mise à jour Windows, consultez [Résoudre les problèmes de l’agent de mise à jour Windows](troubleshoot/update-agent-issues.md).
* Pour résoudre les problèmes liés à l’agent de mise à jour Linux, consultez [Résoudre les problèmes de l’agent de mise à jour Linux](troubleshoot/update-agent-issues-linux.md).