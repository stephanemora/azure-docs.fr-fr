---
title: Gestion des extensions de machine virtuelle avec des serveur activés par Azure Arc
description: Les serveurs activés par Azure Arc peuvent gérer le déploiement d’extensions de machine virtuelle qui permettent d’effectuer des tâches d’automatisation et de configuration post-déploiement sur des machines virtuelles non Azure.
ms.date: 09/23/2020
ms.topic: conceptual
ms.openlocfilehash: 1c3d50f407f4412a14201dfe669334dbb083d323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329072"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Gestion des extensions de machine virtuelle avec des serveurs activés par Azure Arc

Les extensions de machine virtuelle sont de petites applications qui permettent d’effectuer des tâches de configuration et d’automatisation de post-déploiement sur des machines virtuelles Azure. Par exemple, si une machine virtuelle nécessite l’installation d’un logiciel, une protection antivirus ou l’exécution d’un script, vous pouvez utiliser une extension de machine virtuelle.

Les serveurs activés par Azure Arc vous permettent de déployer des extensions de machine virtuelle Azure sur des machines virtuelles Linux et Windows non Azure, ce qui simplifie la gestion de vos environnements de machines hybrides locaux, périphériques et sur d’autres clouds tout au long de leur cycle de vie.

## <a name="key-benefits"></a>Principaux avantages

La prise en charge des extensions de machine virtuelle des serveurs activés par Azure Arc offre les avantages clés suivants :

* Utilise [Azure Automation State Configuration](../../automation/automation-dsc-overview.md) pour stocker de manière centralisée les configurations et conserver l’état souhaité des machines connectées hybrides activées via l’extension de machine virtuelle DSC.

* Collecte les données de journaux à des fins d’analyse en activant [Journaux dans Azure Monitor](../../azure-monitor/platform/data-platform-logs.md) via l’extension de machine virtuelle de l’agent Log Analytics. Cela s’avère utile pour effectuer des analyses complexes de données issues de diverses sources.

* Avec [Azure Monitor pour machines virtuelles](../../azure-monitor/insights/vminsights-overview.md), analyse les performances de vos machines virtuelles Windows et Linux et supervise leurs processus et dépendances vis-à-vis d’autres ressources et les processus externes. Pour cela, il convient d’activer les extensions de machine virtuelle de l’agent Log Analytics et de Dependency Agent.

* Télécharge et exécute des scripts sur des machines connectées hybrides à l’aide de l’extension de script personnalisé. Elle est utile pour la configuration de post-déploiement, l’installation de logiciels ou toute autre tâche de configuration ou de gestion.

## <a name="availability"></a>Disponibilité

La fonctionnalité d’extension de machine virtuelle n’est disponible que dans la liste des [régions prises en charge](overview.md#supported-regions). Veillez à intégrer votre machine dans l’une de ces régions.

## <a name="extensions"></a>Extensions

Dans cette version, nous prenons en charge les extensions de machine virtuelle suivantes sur les machines Windows et Linux.

|Extension |Système d’exploitation |Serveur de publication |Informations supplémentaires |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Extension de script personnalisée Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft.PowerShell|[Extension DSC Windows PowerShell](../../virtual-machines/extensions/dsc-windows.md)|
|Agent Log Analytics |Windows |Microsoft.EnterpriseCloud.Monitoring |[Extension de machine virtuelle Log Analytics pour Windows](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft Dependency Agent | Windows |Microsoft.Compute | [Extension de machine virtuelle Dependency Agent pour Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft.Azure.Extension |[Extension de script personnalisé Linux version 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[Extension DSC PowerShell pour Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Agent Log Analytics |Linux |Microsoft.EnterpriseCloud.Monitoring |[Extension de machine virtuelle Log Analytics pour Linux](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft Dependency Agent | Linux |Microsoft.Compute | [Extension de machine virtuelle Dependency Agent pour Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |

Les extensions de machine virtuelle peuvent être exécutées avec des modèles Azure Resource Manager, à partir du portail Azure, ou Azure PowerShell sur des serveurs hybrides managés par des serveurs activés par Arc.

Pour découvrir le package d’agent Azure Connected Machine et obtenir des détails sur le composant Agent Extension, consultez [Vue d’ensemble de l’agent](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Prérequis

Cette fonctionnalité dépend des fournisseurs de ressources Azure suivants de votre abonnement :

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

S’ils ne sont pas déjà inscrits, suivez les étapes indiquées sous [Inscrire des fournisseurs de ressources Azure](agent-overview.md#register-azure-resource-providers).

L’extension de machine virtuelle de l’agent Log Analytics pour Linux nécessite l’installation de Python 2.x sur la machine cible.

### <a name="connected-machine-agent"></a>Agent Connected Machine

Vérifiez que votre machine correspond aux [versions prises en charge](agent-overview.md#supported-operating-systems) des systèmes d’exploitation Windows et Linux pour l’agent Azure Connected Machine.

La version minimale de l’agent Connected Machine prise en charge avec cette fonctionnalité sur Windows et Linux est la version 1.0.

Pour mettre à niveau votre machine vers la version exigée de l’agent, consultez [Mettre à niveau l’agent](manage-agent.md#upgrading-agent).

## <a name="enable-extensions-from-the-portal"></a>Activer les extensions à partir du portail

Les extensions de machine virtuelle peuvent être appliquées pour une machine managée par serveur via le portail Azure.

1. À partir de votre navigateur, accédez au [portail Azure](https://portal.azure.com).

2. Sur le portail, accédez à **Serveurs – Azure Arc**, puis sélectionnez votre machine hybride dans la liste.

3. Choisissez **Extensions**, puis sélectionnez **Ajouter**. Choisissez l’extension souhaitée dans la liste des extensions disponibles, puis suivez les instructions de l’Assistant. Dans cet exemple, nous allons déployer l’extension de machine virtuelle Log Analytics.

    ![Sélectionner l’extension de machine virtuelle pour la machine sélectionnée](./media/manage-vm-extensions/add-vm-extensions.png)

    L’exemple suivant illustre l’installation de l’extension de machine virtuelle Log Analytics à partir du portail Azure :

    ![Installer l’extension de machine virtuelle Log Analytics](./media/manage-vm-extensions/mma-extension-config.png)

    Pour mener à bien l’installation, il vous est demandé de fournir l’ID et la clé primaire de l’espace de travail. Si vous ne savez pas comment vous procurer ces informations, consultez [Obtenir l’ID et la clé d’espace de travail](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

4. Après avoir confirmé les informations nécessaires que vous avez fournies, sélectionnez **Créer**. Dans le résumé du déploiement qui s’affiche, examinez l’état du déploiement.

>[!NOTE]
>Même si plusieurs extensions peuvent être regroupées et traitées ensemble, elles sont installées en série. Une fois l’installation de la première extension terminée, l’installation de la suivante est entreprise.

## <a name="azure-resource-manager-templates"></a>Modèles Microsoft Azure Resource Manager

Les extensions de machine virtuelle peuvent être ajoutées à un modèle Azure Resource Manager et exécutées avec le déploiement du modèle. Vous pouvez déployer les extensions de machine virtuelle prises en charge par les serveurs activés par Arc sur des machines Linux ou Windows à l’aide d’Azure PowerShell. Chaque exemple ci-dessous comprend un fichier de modèle et un fichier de paramètres avec des exemples de valeurs à fournir au modèle.

>[!NOTE]
>Même si plusieurs extensions peuvent être regroupées et traitées ensemble, elles sont installées en série. Une fois l’installation de la première extension terminée, l’installation de la suivante est entreprise.

### <a name="deploy-the-log-analytics-vm-extension"></a>Déployer l’extension de machine virtuelle Log Analytics

Pour déployer facilement l’agent Log Analytics, l’exemple suivant est fourni pour installer l’agent sur Windows ou Linux.

#### <a name="template-file-for-linux"></a>Fichier de modèle pour Linux

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Fichier de modèle pour Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Fichier de paramètres

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

Enregistrez les fichiers de modèle et de paramètres sur le disque, puis modifiez le fichier de paramètres avec les valeurs qui conviennent pour votre déploiement. Vous pouvez ensuite installer l’extension sur toutes les machines connectées au sein d’un groupe de ressources avec la commande suivante. La commande utilise le paramètre *TemplateFile* pour spécifier le modèle et le paramètre *TemplateParameterFile* pour spécifier le fichier qui contient les paramètres et leurs valeurs.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgentWin.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentWinParms.json"
```

### <a name="deploy-the-custom-script-extension"></a>Déployer l’extension de script personnalisé

Pour utiliser l’extension de script personnalisé, l’exemple suivant est fourni pour s’exécuter sur Windows et Linux. Si vous ne connaissez pas bien l’extension de script personnalisé, consultez [Extension de script personnalisé pour Windows](../../virtual-machines/extensions/custom-script-windows.md) ou [Extension de script personnalisé pour Linux](../../virtual-machines/extensions/custom-script-linux.md). Avant d’utiliser cette extension avec des machines hybrides, vous devez comprendre quelques caractéristiques divergentes :

* La liste des systèmes d’exploitation pris en charge avec l’extension de script personnalisé de machine virtuelle Azure ne s’applique pas à un serveur avec Azure Arc. La liste des systèmes d’exploitation pris en charge pour Arc pour serveurs se trouve [ici](agent-overview.md#supported-operating-systems).

* Les détails de configuration concernant Azure Virtual Machine Scale Sets ou les machines virtuelles Classic ne sont pas applicables.

* Si vos machines doivent télécharger un script en externe et peuvent communiquer uniquement via un serveur proxy, vous devez [configurer l’agent Connected Machine](manage-agent.md#update-or-remove-proxy-settings) pour définir la variable d’environnement du serveur proxy.

La configuration de l’extension de script personnalisé spécifie des éléments tels que l’emplacement du script et la commande à exécuter. Cette configuration est spécifiée dans un modèle Azure Resource Manager, fourni ci-dessous pour les machines hybrides Linux et Windows.

#### <a name="template-file-for-linux"></a>Fichier de modèle pour Linux

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

#### <a name="template-file-for-windows"></a>Fichier de modèle pour Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Fichier de paramètres

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

### <a name="deploy-the-powershell-dsc-extension"></a>Déployer l’extension DSC PowerShell

Pour utiliser l’extension DSC PowerShell, l’exemple suivant est fourni pour s’exécuter sur Windows et Linux. Si vous ne connaissez pas bien l’extension DSC PowerShell, consultez [Vue d’ensemble du gestionnaire d’extensions DSC](../../virtual-machines/extensions/dsc-overview.md). Avant d’utiliser cette extension avec des machines hybrides, vous devez comprendre quelques caractéristiques divergentes :

* La liste des systèmes d’exploitation pris en charge avec l’extension DSC PowerShell de machine virtuelle Azure ne s’applique pas à un serveur avec Azure Arc. La liste des systèmes d’exploitation pris en charge pour Arc pour serveurs se trouve [ici](agent-overview.md#supported-operating-systems).

* Si vos machines doivent télécharger un script en externe et peuvent communiquer uniquement via un serveur proxy, vous devez [configurer l’agent Connected Machine](manage-agent.md#update-or-remove-proxy-settings) pour définir la variable d’environnement du serveur proxy.

#### <a name="template-file-for-linux"></a>Fichier de modèle pour Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vm, will be used as DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "mode": {
            "type": "string",
            "defaultValue": "Push",
            "metadata": {
                "description": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)"
            },
            "allowedValues": [
                "Push",
                "Pull",
                "Install",
                "Register"
            ]
        },
        "fileUri": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The uri of the MOF file/Meta MOF file/resource ZIP file"
            }
        },
        "registrationUrl": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The URL of the Azure Automation account"
            }
        },
        "registrationKey": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The access key of the Azure Automation account"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/DSCForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.OSTCExtensions",
                "type": "DSCForLinux",
                "settings": {
                    "Mode": "[parameters('mode')]",
                    "FileUri": "[parameters('fileUri')]"
                },
                "protectedSettings": {
                    "RegistrationUrl": "[parameters('registrationUrl')]",
                    "RegistrationKey": "[parameters('registrationKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Fichier de modèle pour Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "modulesUrl": {
            "type": "string"
        },
        "configurationFunction": {
            "type": "string"
        },
        "properties": {
            "type": "string",
            "defaultValue": ""
        },
        "dataBlobUri": {
            "type": "string",
            "defaultValue": ""
        },
        "wmfVersion": {
            "type": "string",
            "defaultValue": "latest",
            "allowedValues": [
                "4.0",
                "5.0",
                "5.1",
                "latest"
            ]
        },
        "privacy": {
            "type": "string",
            "defaultValue": ""
        },
        "autoUpdate": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/Microsoft.Powershell.DSC')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Powershell",
                "type": "DSC",
                "autoUpgradeMinorVersion": "[parameters('autoUpdate')]",
                "settings": {
                    "ModulesUrl": "[parameters('modulesUrl')]",
                    "ConfigurationFunction": "[parameters('configurationFunction')]",
                    "Properties": "[parameters('properties')]",
                    "WmfVersion": "[parameters('wmfVersion')]",
                    "Privacy": {
                        "DataCollection": "[parameters('privacy')]"
                    }
                },
                "protectedSettings": {
                    "DataBlobUri": "[parameters('dataBlobUri')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>Fichier de paramètres

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "dscExtension",
        "label": "Add DSC Extension",
        "elements": [
          {
            "name": "Mode",
            "type": "Microsoft.Common.OptionsGroup",
            "label": "Mode",
            "defaultValue": 0,
            "toolTip": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Push",
                  "value": "Push"
                },
                {
                  "label": "Pull",
                  "value": "Pull"
                },
                {
                  "label": "Install",
                  "value": "Install"
                },
                {
                  "label": "Register",
                  "value": "Register"
                }
              ]
            },
            "visible": true
          },
          {
            "name": "FileUri",
            "type": "Microsoft.Common.FileUpload",
            "label": "File URI",
            "toolTip": "The uri of the MOF file/Meta MOF file/resource ZIP file",
            "constraints": {
              "required": false,
              "accept": ".psd1"
            },
            "options": {
              "multiple": false,
              "uploadMode": "url",
              "openMode": "binary",
              "encoding": "UTF-8"
            }
          },
          {
            "name": "RegistrationUrl",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration URL",
            "toolTip": "The URL of the Azure Automation account",
            "constraints": {
              "required": false
            }
          },
          {
            "name": "RegistrationKey",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration key",
            "toolTip": "The access key of the Azure Automation account",
            "constraints": {
              "required": false
            }
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "mode": "[steps('dscExtension').Mode]",
      "fileUri": "[steps('dscExtension').FileUri]",
      "registrationUrl": "[steps('dscExtension').RegistrationUrl]",
      "registrationKey": "[steps('dscExtension').RegistrationKey]"
    }
  }
}
```

### <a name="dependency-agent"></a>Agent de dépendances

Pour utiliser l’extension d’Azure Monitor Dependency Agent, l’exemple suivant est fourni pour s’exécuter sur Windows et Linux. Si vous ne connaissez pas bien Dependency Agent, consultez [Vue d’ensemble des agents Azure Monitor](../../azure-monitor/platform/agents-overview.md#dependency-agent).

#### <a name="template-file-for-linux"></a>Fichier de modèle pour Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

#### <a name="template-file-for-windows"></a>Fichier de modèle pour Windows

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

## <a name="uninstall-extension"></a>Désinstaller une extension

La suppression d’une ou plusieurs extensions d’un serveur activé par Arc ne peut être effectuée qu’à partir du portail Azure. Ensuite, effectuez les opérations suivantes pour supprimer une extension.

1. À partir de votre navigateur, accédez au [portail Azure](https://portal.azure.com).

2. Sur le portail, accédez à **Serveurs – Azure Arc**, puis sélectionnez votre machine hybride dans la liste.

3. Choisissez **Extensions**, puis sélectionnez une extension dans la liste des extensions installées.

4. Sélectionnez **Désinstaller**, puis, lorsque vous êtes invité à vérifier, sélectionnez **Oui** pour continuer.

## <a name="next-steps"></a>Étapes suivantes

* Des informations de dépannage sont accessibles dans le [guide Résoudre les problèmes liés aux extensions de machine virtuelle](troubleshoot-vm-extensions.md).

* Apprenez à gérer votre machine à l’aide d’[Azure Policy](../../governance/policy/overview.md), par exemple pour la [configuration invité](../../governance/policy/concepts/guest-configuration.md) des machines virtuelles, pour vérifier que l’ordinateur crée des rapports sur l’espace de travail Log Analytics prévu, pour activer l’analyse d’[Azure Monitor sur des machines virtuelles](../../azure-monitor/insights/vminsights-enable-policy.md) et bien plus encore.

* En savoir plus sur [l’agent Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L’agent Log Analytics pour Windows et Linux est nécessaire quand vous souhaitez collecter des données de supervision du système d’exploitation et de la charge de travail, gérer les ressources à l’aide de runbooks Automation ou de fonctionnalités comme Update Management, ou utiliser d’autres services Azure comme [Azure Security Center](../../security-center/security-center-intro.md).