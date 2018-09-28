---
title: Envoyer des métriques de système d’exploitation invité vers le magasin de métriques d’Azure Monitor à l’aide d’un modèle Resource Manager pour une machine virtuelle Windows
description: Envoyer des métriques de système d’exploitation invité vers le magasin de métriques d’Azure Monitor à l’aide d’un modèle Resource Manager pour une machine virtuelle Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 4ed911766a14dd35ea662326a5d50df11cf81698
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984062"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Envoyer des métriques de système d’exploitation invité vers le magasin de métriques d’Azure Monitor à l’aide d’un modèle Resource Manager pour une machine virtuelle Windows

L’[extension Windows Azure Diagnostics](azure-diagnostics.md) (WAD) d’Azure Monitor vous permet de collecter des métriques et des journaux à partir du système d’exploitation invité (SE invité) exécuté dans le cadre d’une machine virtuelle, d’un service cloud ou d’un cluster Service Fabric.  L’extension peut envoyer des données de télémétrie vers de nombreux emplacements différents répertoriés dans l’article précédemment lié.  

Cet article décrit le processus pour envoyer les métriques de performance du SE invité d’une machine virtuelle Windows vers le magasin de données d’Azure Monitor. À partir de WAD version 1.11, vous pouvez écrire des métriques directement dans le magasin de métriques d’Azure Monitor où les métriques standard de la plateforme sont déjà collectées. En les stockant dans cet emplacement, vous avez accès aux mêmes actions que celles disponibles pour les métriques de la plateforme.  Ces actions sont les suivantes : génération d’alertes en temps quasi réel, création de graphiques, routage, accès à partir de l’API REST et bien plus encore.  Avant, l’extension WAD écrivait dans Stockage Azure, mais pas dans le magasin de données d’Azure Monitor.   

Si vous ne connaissez pas les modèles Resource Manager, apprenez-en plus sur les [déploiements de modèle](../azure-resource-manager/resource-group-overview.md), leur structure et leur syntaxe.  

## <a name="pre-requisites"></a>Conditions préalables

- Votre abonnement doit être inscrit auprès de [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Vous devez avoir installé [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1), ou vous pouvez utiliser [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 

 
## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurer Azure Monitor en tant que récepteur de données 
L’extension Azure Diagnostics utilise une fonctionnalité appelée « récepteurs de données » pour acheminer les métriques et les journaux vers différents emplacements.  Les étapes suivantes montrent comment utiliser un modèle Resource Manager et PowerShell pour déployer une machine virtuelle à l’aide du nouveau récepteur de données « Azure Monitor ». 

## <a name="author-resource-manager-template"></a>Créer un modèle Resource Manager 
Pour cet exemple, vous pouvez utiliser un exemple de modèle rendu public. Les modèles de démarrage se trouvent dans https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows 

- **Azuredeploy.json** est un modèle Resource Manager préconfiguré pour le déploiement d’une machine virtuelle. 

- **Azuredeploy.parameters.json** est un fichier de paramètres qui stocke des informations telles que le nom d’utilisateur et le mot de passe que vous souhaitez définir pour votre machine virtuelle. Pendant le déploiement, le modèle Resource Manager utilise les paramètres définis dans ce fichier. 

Téléchargez et enregistrez les deux fichiers en local. 

###  <a name="modify-azuredeployparametersjson"></a>Modifier le fichier azuredeploy.parameters.json
Ouvrez le fichier *azuredeploy.parameters.json* 

1. Entrez les valeurs *adminUsername* et *adminPassword* pour la machine virtuelle. Ces paramètres sont utilisés pour l’accès à distance à la machine virtuelle. N’UTILISEZ PAS les paramètres de ce modèle pour éviter un accès non autorisé à votre machine virtuelle. Les bots recherchent sur Internet des noms d’utilisateur et mots de passe dans les référentiels GitHub publics. Avec ces valeurs par défaut, ils sont susceptibles de se transformer en machines virtuelles de test.  

1. Créez un dnsname unique pour la machine virtuelle.  

### <a name="modify-azuredeployjson"></a>Modifier le fichier azuredeploy.json

Ouvrez le fichier *azuredeploy.json* 

Ajoutez un identificateur de compte de stockage à la section **variables** du modèle après l’entrée de **nomCompteStorageAccount**.  

```json
// Find these lines 
"variables": { 
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]", 

// Add this line directly below.  
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]", 
```

Ajoutez cette extension MSI (Managed Service Identity) au modèle en haut de la section « ressources ».  L’extension garantit qu’Azure Monitor accepte les métriques émises.  

```json
//Find this code 
"resources": [
// Add this code directly below
    { 
        "type": "Microsoft.Compute/virtualMachines/extensions", 
        "name": "WADExtensionSetup", 
        "apiVersion": "2015-05-01-preview", 
        "location": "[resourceGroup().location]", 
        "dependsOn": [ 
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ], 
        "properties": { 
            "publisher": "Microsoft.ManagedIdentity", 
            "type": "ManagedIdentityExtensionForWindows", 
            "typeHandlerVersion": "1.0", 
            "autoUpgradeMinorVersion": true, 
            "settings": { 
                "port": 50342 
            } 
        } 
    }, 
```

Ajoutez la configuration « identité » à la ressource de la machine virtuelle pour vous assurer qu’Azure attribue une identité système à l’extension MSI. Cette étape permet de s’assurer que la machine virtuelle peut envoyer des métriques invitées la concernant à Azure Monitor 

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{ 
    "apiVersion": "2017-03-30", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[variables('vmName')]", 
    "location": "[resourceGroup().location]", 
    // add these 3 lines below
    "identity": {  
    "type": "SystemAssigned" 
    }, 
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {   
    ...
```

Ajoutez la configuration suivante pour activer l’extension de diagnostic sur une machine virtuelle Windows.  Pour une simple machine virtuelle basée sur Resource Manager, nous pouvons ajouter la configuration de l’extension au tableau Ressources de la machine virtuelle. La ligne « récepteurs » : « AzMonSink » et la configuration correspondante « SinksConfig » plus loin dans la section permettent à l’extension d’envoyer les métriques directement à Azure Monitor. N’hésitez pas à ajouter/supprimer des compteurs de performances si nécessaire.  


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": { 
    "bootDiagnostics": { 
    "enabled": true, 
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]" 
    } 
} 
}, 
//Start of section to add 
"resources": [        
{ 
            "type": "extensions", 
            "name": "Microsoft.Insights.VMDiagnosticsSettings", 
            "apiVersion": "2015-05-01-preview", 
            "location": "[resourceGroup().location]", 
            "dependsOn": [ 
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" 
            ], 
            "properties": { 
            "publisher": "Microsoft.Azure.Diagnostics", 
            "type": "IaaSDiagnostics", 
            "typeHandlerVersion": "1.4", 
            "autoUpgradeMinorVersion": true, 
            "settings": { 
                "WadCfg": { 
                "DiagnosticMonitorConfiguration": { 
    "overallQuotaInMB": 4096, 
    "DiagnosticInfrastructureLogs": { 
                    "scheduledTransferLogLevelFilter": "Error" 
        }, 
                    "Directories": { 
                    "scheduledTransferPeriod": "PT1M", 
    "IISLogs": { 
                        "containerName": "wad-iis-logfiles" 
                    }, 
                    "FailedRequestLogs": { 
                        "containerName": "wad-failedrequestlogs" 
                    } 
                    }, 
                    "PerformanceCounters": { 
                    "scheduledTransferPeriod": "PT1M", 
                    "sinks": "AzMonSink", 
                    "PerformanceCounterConfiguration": [ 
                        { 
                        "counterSpecifier": "\\Memory\\Available Bytes", 
                        "sampleRate": "PT15S" 
                        }, 
                        { 
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                        "sampleRate": "PT15S" 
                        }, 
                        { 
                        "counterSpecifier": "\\Memory\\Committed Bytes", 
                        "sampleRate": "PT15S" 
                        } 
                    ] 
                    }, 
                    "WindowsEventLog": { 
                    "scheduledTransferPeriod": "PT1M", 
                    "DataSource": [ 
                        { 
                        "name": "Application!*" 
                        } 
                    ] 
                    }, 
                    "Logs": { 
                    "scheduledTransferPeriod": "PT1M", 
                    "scheduledTransferLogLevelFilter": "Error" 
                    } 
                }, 
                "SinksConfig": { 
                    "Sink": [ 
                    { 
                        "name" : "AzMonSink", 
                        "AzureMonitor" : {} 
                    } 
                    ] 
                } 
                }, 
                "StorageAccount": "[variables('storageAccountName')]" 
            }, 
            "protectedSettings": { 
                "storageAccountName": "[variables('storageAccountName')]", 
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]", 
                "storageAccountEndPoint": "https://core.windows.net/" 
            } 
            } 
        } 
        ] 
//End of section to add 
```


Enregistrez et fermez les deux fichiers 
 

## <a name="deploy-the-resource-manager-template"></a>Déployer le modèle Resource Manager 

> [!NOTE]
> Vous devez exécuter la version 1.5 ou supérieure de l’extension Azure Diagnostics ET la valeur de la propriété « autoUpgradeMinorVersion » doit être définie sur « true » dans votre modèle Resource Manager.  Azure charge alors l’extension appropriée lorsqu’il démarre la machine virtuelle. Si ces paramètres ne figurent pas dans votre modèle, modifiez-les et redéployez le modèle. 


Pour déployer le modèle Resource Manager, vous allez utiliser Azure PowerShell.  

1. Lancement de PowerShell 
1. Connexion à Azure avec `Login-AzureRmAccount`
1. Obtenez la liste de vos abonnements à l’aide de `Get-AzureRmSubscription`
1. Définissez l’abonnement dans lequel vous allez créer/mettre à jour la machine virtuelle 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Créez un nouveau groupe de ressources pour la machine virtuelle déployée, exécutez la commande ci-dessous 

   ```PowerShell
    New-AzureRmResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>" 
   ```
   > [!NOTE] 
   > N’oubliez pas d’[utiliser une région Azure dans laquelle les mesures personnalisées sont activées](metrics-custom-overview.md). 
 
1. Exécutez les commandes suivantes pour déployer la machine virtuelle avec le  
   > [!NOTE] 
   > Si vous souhaitez mettre à jour une machine virtuelle existante, ajoutez simplement *-Mode Incremental* à la fin de la commande suivante. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>" 
   ```
  
1. Lorsque votre déploiement a abouti, vous devez être en mesure de retrouver la machine virtuelle dans le portail Azure, et il doit transmettre des métriques à Azure Monitor. 

   > [!NOTE] 
   > Vous pouvez rencontrer des erreurs liées à la vmSkuSize sélectionnée. Si cela se produit, revenez à votre fichier azuredeploy.json et mettez à jour la valeur par défaut du paramètre vmSkuSize. Dans ce cas, nous vous recommandons d’essayer « Standard_DS1_v2 »). 

## <a name="chart-your-metrics"></a>Représenter vos métriques graphiquement 

1. Connectez-vous au portail Azure 

1. Dans le menu de gauche, cliquez sur **Surveiller** 

1. Dans la page Surveiller, cliquez sur **Métriques**. 

   ![Page Métriques](./media/metrics-store-custom-rest-api/metrics.png) 

1. Modifiez la période d’agrégation afin d’indiquer **30 dernières minutes**.  

1. Dans la liste déroulante de la ressource, sélectionnez la machine virtuelle qui vient d’être créée. Si vous n’avez pas modifié le nom dans le modèle, elle doit s’appeler *SimpleWinVM2*.  

1. Dans la liste déroulante d’espaces de noms, sélectionnez **azure.vm.windows.guest** 

1. Dans la liste déroulante des métriques, sélectionnez **Memory\%Committed Bytes in Use** (Mémoire\Octets validés utilisés).  
 

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [métriques personnalisées](metrics-custom-overview.md).